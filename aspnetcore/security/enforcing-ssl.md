---
title: Vynucení protokolu HTTPS v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak chcete vyžadovat protokol HTTPS/TLS ve webové aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/18/2018
uid: security/enforcing-ssl
ms.openlocfilehash: d287d30203fbf367203afe65e05478806fafab34
ms.sourcegitcommit: 408921a932448f66cb46fd53c307a864f5323fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51570045"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="58c63-103">Vynucení protokolu HTTPS v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58c63-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="58c63-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="58c63-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="58c63-105">Tento dokument ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="58c63-105">This document shows how to:</span></span>

* <span data-ttu-id="58c63-106">Vyžadovat protokol HTTPS pro všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="58c63-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="58c63-107">Přesměrujte všechny požadavky HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="58c63-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="58c63-108">Žádné rozhraní API můžete zabránit posláním citlivých dat na první požadavek klienta.</span><span class="sxs-lookup"><span data-stu-id="58c63-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

> [!WARNING]
> <span data-ttu-id="58c63-109">Proveďte **není** použít [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) na webová rozhraní API, která zobrazit citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="58c63-109">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="58c63-110">`RequireHttpsAttribute` stavové kódy HTTP se používá pro přesměrování prohlížeče z HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="58c63-110">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="58c63-111">Klienti rozhraní API nemusí pochopit a dodržují přesměrování z HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="58c63-111">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="58c63-112">Tito klienti mohou odesílat informace přes protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="58c63-112">Such clients may send information over HTTP.</span></span> <span data-ttu-id="58c63-113">Webová rozhraní API by buď:</span><span class="sxs-lookup"><span data-stu-id="58c63-113">Web APIs should either:</span></span>
>
> * <span data-ttu-id="58c63-114">Nelze naslouchat na HTTP.</span><span class="sxs-lookup"><span data-stu-id="58c63-114">Not listen on HTTP.</span></span>
> * <span data-ttu-id="58c63-115">Ukončete připojení s stavový kód 400 (Chybný požadavek) a není obsluhovat žádosti.</span><span class="sxs-lookup"><span data-stu-id="58c63-115">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

## <a name="require-https"></a><span data-ttu-id="58c63-116">Vyžadovat protokol HTTPS</span><span class="sxs-lookup"><span data-stu-id="58c63-116">Require HTTPS</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="58c63-117">Doporučujeme tuto produkční ASP.NET Core volání webové aplikace:</span><span class="sxs-lookup"><span data-stu-id="58c63-117">We recommend that production ASP.NET Core web apps call:</span></span>

* <span data-ttu-id="58c63-118">Middleware přesměrování protokolu HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) pro přesměrování požadavků protokolu HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="58c63-118">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="58c63-119">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) k odeslání hlavičky protokolu HTTP striktní přenosu zabezpečení protokolu (HSTS) pro klienty.</span><span class="sxs-lookup"><span data-stu-id="58c63-119">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="58c63-120">Aplikace nasazené v konfigurace reverzního proxy serveru povolit proxy pro zpracování zabezpečení připojení (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="58c63-120">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="58c63-121">Pokud proxy server zpracovává také přesměrování protokolu HTTPS, není nutné používat Middleware přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="58c63-121">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="58c63-122">Pokud proxy server také zpracovává zápis HSTS hlavičky (například [HSTS nativní podporu v IIS 10.0 (1709) nebo novější](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware není potřebné aplikace.</span><span class="sxs-lookup"><span data-stu-id="58c63-122">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="58c63-123">Další informace najdete v tématu [výslovného nesouhlasu z HTTPS/HSTS při vytvoření projektu](#opt-out-of-httpshsts-on-project-creation).</span><span class="sxs-lookup"><span data-stu-id="58c63-123">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="58c63-124">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="58c63-124">UseHttpsRedirection</span></span>

<span data-ttu-id="58c63-125">Následující kód volá `UseHttpsRedirection` v `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="58c63-125">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=13)]

<span data-ttu-id="58c63-126">Předchozí zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="58c63-126">The preceding highlighted code:</span></span>

* <span data-ttu-id="58c63-127">Používá výchozí [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span><span class="sxs-lookup"><span data-stu-id="58c63-127">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="58c63-128">Používá výchozí [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), pokud není přepsán `ASPNETCORE_HTTPS_PORT` proměnné prostředí nebo [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span><span class="sxs-lookup"><span data-stu-id="58c63-128">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="58c63-129">Doporučujeme používat dočasné přesměrování spíše než trvalé přesměrování.</span><span class="sxs-lookup"><span data-stu-id="58c63-129">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="58c63-130">Ukládání do mezipaměti odkazu může způsobit nestabilní chování ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="58c63-130">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="58c63-131">Pokud chcete poslat kód stavu trvalé přesměrování, když se aplikace nachází mimo vývojové prostředí, najdete v článku [Konfigurace trvalé přesměrování v produkčním prostředí](#configure-permanent-redirects-in-production) oddílu.</span><span class="sxs-lookup"><span data-stu-id="58c63-131">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="58c63-132">Doporučujeme používat [HSTS](#http-strict-transport-security-protocol-hsts) na signál pro klienty, kteří pouze zabezpečit prostředek požadavků odesílaných do aplikace (jenom v produkčním prostředí).</span><span class="sxs-lookup"><span data-stu-id="58c63-132">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="58c63-133">Konfigurace portu</span><span class="sxs-lookup"><span data-stu-id="58c63-133">Port configuration</span></span>

<span data-ttu-id="58c63-134">Port musí být k dispozici pro daný middleware pro přesměrování nezabezpečený požadavek na protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="58c63-134">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="58c63-135">Pokud je k dispozici žádný port:</span><span class="sxs-lookup"><span data-stu-id="58c63-135">If no port is available:</span></span>

* <span data-ttu-id="58c63-136">Nedojde k přesměrování na protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="58c63-136">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="58c63-137">Middleware protokoluje upozornění "Nepovedlo se určit port https pro přesměrování."</span><span class="sxs-lookup"><span data-stu-id="58c63-137">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="58c63-138">Zadejte port HTTPS pomocí kteréhokoli z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="58c63-138">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="58c63-139">Nastavte [HttpsRedirectionOptions.HttpsPort](#options).</span><span class="sxs-lookup"><span data-stu-id="58c63-139">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>
* <span data-ttu-id="58c63-140">Nastavte `ASPNETCORE_HTTPS_PORT` proměnné prostředí nebo [nastavení konfigurace webového hostitele https_port](xref:fundamentals/host/web-host#https-port):</span><span class="sxs-lookup"><span data-stu-id="58c63-140">Set the `ASPNETCORE_HTTPS_PORT` environment variable or [https_port Web Host configuration setting](xref:fundamentals/host/web-host#https-port):</span></span>

  <span data-ttu-id="58c63-141">**Klíč**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="58c63-141">**Key**: `https_port`</span></span>  
  <span data-ttu-id="58c63-142">**Typ**: *řetězec*</span><span class="sxs-lookup"><span data-stu-id="58c63-142">**Type**: *string*</span></span>  
  <span data-ttu-id="58c63-143">**Výchozí**: výchozí hodnota není nastavená.</span><span class="sxs-lookup"><span data-stu-id="58c63-143">**Default**: A default value isn't set.</span></span>  
  <span data-ttu-id="58c63-144">**Sada s použitím**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="58c63-144">**Set using**: `UseSetting`</span></span>  
  <span data-ttu-id="58c63-145">**Proměnná prostředí**: `<PREFIX_>HTTPS_PORT` (předpona je `ASPNETCORE_` při použití [webového hostitele](xref:fundamentals/host/web-host).)</span><span class="sxs-lookup"><span data-stu-id="58c63-145">**Environment variable**: `<PREFIX_>HTTPS_PORT` (The prefix is `ASPNETCORE_` when using the [Web Host](xref:fundamentals/host/web-host).)</span></span>

  <span data-ttu-id="58c63-146">Při konfiguraci <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> v `Program`:</span><span class="sxs-lookup"><span data-stu-id="58c63-146">When configuring an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> in `Program`:</span></span>

  [!code-csharp[](enforcing-ssl/sample-snapshot/Program.cs?name=snippet_Program&highlight=10)]
* <span data-ttu-id="58c63-147">Označení portu pomocí zabezpečené schéma `ASPNETCORE_URLS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="58c63-147">Indicate a port with the secure scheme using the `ASPNETCORE_URLS` environment variable.</span></span> <span data-ttu-id="58c63-148">Proměnná prostředí nakonfiguruje server.</span><span class="sxs-lookup"><span data-stu-id="58c63-148">The environment variable configures the server.</span></span> <span data-ttu-id="58c63-149">Middleware nepřímo zjistí port HTTPS přes <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="58c63-149">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="58c63-150">(Nemá **není** práce v nasazeních reverzního proxy serveru.)</span><span class="sxs-lookup"><span data-stu-id="58c63-150">(Does **not** work in reverse proxy deployments.)</span></span>
* <span data-ttu-id="58c63-151">Při vývoji, nastavte adresu URL HTTPS *launchsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="58c63-151">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="58c63-152">Povolení HTTPS, když se používá služba IIS Express.</span><span class="sxs-lookup"><span data-stu-id="58c63-152">Enable HTTPS when IIS Express is used.</span></span>
* <span data-ttu-id="58c63-153">Konfigurace koncového bodu adresy URL HTTPS nasazení veřejnou hraniční [Kestrel](xref:fundamentals/servers/kestrel) nebo [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="58c63-153">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="58c63-154">Pouze **jeden port HTTPS** používají aplikaci.</span><span class="sxs-lookup"><span data-stu-id="58c63-154">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="58c63-155">Middleware zjistí port přes <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="58c63-155">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="58c63-156">Při spuštění aplikace za reverzní proxy server (například služby IIS, IIS Express), `IServerAddressesFeature` není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="58c63-156">When an app is run behind a reverse proxy (for example, IIS, IIS Express), `IServerAddressesFeature` isn't available.</span></span> <span data-ttu-id="58c63-157">Port musí být ručně nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="58c63-157">The port must be manually configured.</span></span> <span data-ttu-id="58c63-158">Pokud port není nastavena, přesměrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="58c63-158">When the port isn't set, requests aren't redirected.</span></span>

<span data-ttu-id="58c63-159">Při Kestrel nebo ovladač HTTP.sys se používá jako veřejnou hraniční server, musí být Kestrel nebo ovladač HTTP.sys nakonfigurovaná k naslouchání na obojí:</span><span class="sxs-lookup"><span data-stu-id="58c63-159">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="58c63-160">Zabezpečený port, ve kterém se klient přesměruje (obvykle 443 v produkčním prostředí a 5001 ve vývoji).</span><span class="sxs-lookup"><span data-stu-id="58c63-160">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="58c63-161">Nezabezpečené port (standardně 80 v produkčním prostředí) a 5000 ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="58c63-161">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="58c63-162">Nezabezpečené port musí být přístupné pro klienta v pořadí pro aplikaci pro příjem nezabezpečený požadavek a přesměrování klienta na zabezpečený port.</span><span class="sxs-lookup"><span data-stu-id="58c63-162">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="58c63-163">Další informace najdete v tématu [konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) nebo <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="58c63-163">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="58c63-164">Scénáře nasazení</span><span class="sxs-lookup"><span data-stu-id="58c63-164">Deployment scenarios</span></span>

<span data-ttu-id="58c63-165">Všechny brány firewall mezi klientem a serverem musí mít také komunikační porty otevřete pro provoz.</span><span class="sxs-lookup"><span data-stu-id="58c63-165">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="58c63-166">Pokud žádosti se předávají v konfiguraci reverzní proxy server, použijte [předané Middleware záhlaví](xref:host-and-deploy/proxy-load-balancer) před voláním Middleware přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="58c63-166">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="58c63-167">Předané Middleware záhlaví aktualizací `Request.Scheme`, použije `X-Forwarded-Proto` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="58c63-167">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="58c63-168">Povolení middleware přesměrování identifikátory URI a další zásady zabezpečení fungovat správně.</span><span class="sxs-lookup"><span data-stu-id="58c63-168">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="58c63-169">Kdy předané Middleware záhlaví nepoužívá, back-endu aplikace nemusí dostávat správné schéma a ukládaly ve smyčce přesměrování.</span><span class="sxs-lookup"><span data-stu-id="58c63-169">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="58c63-170">Běžné chybová zpráva koncovým uživatelem se, že došlo k příliš mnoha přesměrování.</span><span class="sxs-lookup"><span data-stu-id="58c63-170">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="58c63-171">Při nasazování do služby Azure App Service, postupujte podle pokynů v [kurz: vytvoření vazby existujícího vlastního certifikátu SSL k Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="58c63-171">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="58c63-172">Možnosti</span><span class="sxs-lookup"><span data-stu-id="58c63-172">Options</span></span>

<span data-ttu-id="58c63-173">Následující zvýrazněný kód volá [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) konfigurace middlewaru možností:</span><span class="sxs-lookup"><span data-stu-id="58c63-173">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=14-99)]

<span data-ttu-id="58c63-174">Volání `AddHttpsRedirection` jenom je potřeba změnit hodnoty `HttpsPort` nebo `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="58c63-174">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="58c63-175">Předchozí zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="58c63-175">The preceding highlighted code:</span></span>

* <span data-ttu-id="58c63-176">Nastaví [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) k <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, což je výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="58c63-176">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="58c63-177">Použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídu pro přiřazení k `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="58c63-177">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="58c63-178">Nastaví HTTPS port 5001.</span><span class="sxs-lookup"><span data-stu-id="58c63-178">Sets the HTTPS port to 5001.</span></span> <span data-ttu-id="58c63-179">Výchozí hodnota je 443.</span><span class="sxs-lookup"><span data-stu-id="58c63-179">The default value is 443.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="58c63-180">Konfigurace trvalé přesměrování v produkčním prostředí</span><span class="sxs-lookup"><span data-stu-id="58c63-180">Configure permanent redirects in production</span></span>

<span data-ttu-id="58c63-181">Middleware odeslání výchozí hodnota je [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) s všechny přesměrování.</span><span class="sxs-lookup"><span data-stu-id="58c63-181">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="58c63-182">Pokud chcete poslat kód stavu trvalé přesměrování, když se aplikace nachází mimo vývojové prostředí, zabalte možnosti konfigurace middlewaru v podmíněnou kontrolu mimo vývojové prostředí.</span><span class="sxs-lookup"><span data-stu-id="58c63-182">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

<span data-ttu-id="58c63-183">Při konfiguraci `IWebHostBuilder` v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="58c63-183">When configuring an `IWebHostBuilder` in *Startup.cs*:</span></span>

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

## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="58c63-184">Alternativním přístupem Middleware přesměrování protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="58c63-184">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="58c63-185">Alternativou k používání HTTPS přesměrování Middleware (`UseHttpsRedirection`), je použít Middleware pro přepis adres URL (`AddRedirectToHttps`).</span><span class="sxs-lookup"><span data-stu-id="58c63-185">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="58c63-186">`AddRedirectToHttps` Můžete také nastavit stavový kód a portu při spuštění přesměrování.</span><span class="sxs-lookup"><span data-stu-id="58c63-186">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="58c63-187">Další informace najdete v tématu [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="58c63-187">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="58c63-188">Při přesměrování na HTTPS bez nutnosti další přesměrování pravidla, doporučujeme používat Middleware přesměrování protokolu HTTPS (`UseHttpsRedirection`) popsané v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="58c63-188">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="58c63-189">[RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) se používá tak, aby vyžadovala protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="58c63-189">The [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) is used to require HTTPS.</span></span> <span data-ttu-id="58c63-190">`[RequireHttpsAttribute]` můžete uspořádání, řadiče nebo metod, nebo je možné uplatňovat globálně.</span><span class="sxs-lookup"><span data-stu-id="58c63-190">`[RequireHttpsAttribute]` can decorate controllers or methods, or can be applied globally.</span></span> <span data-ttu-id="58c63-191">Použít atribut globálně, přidejte následující kód, který `ConfigureServices` v `Startup`:</span><span class="sxs-lookup"><span data-stu-id="58c63-191">To apply the attribute globally, add the following code to `ConfigureServices` in `Startup`:</span></span>

[!code-csharp[](~/security/authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-999)]

<span data-ttu-id="58c63-192">Předchozí zvýrazněný kód technologie používat všechny požadavky `HTTPS`, proto požadavky HTTP jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="58c63-192">The preceding highlighted code requires all requests use `HTTPS`; therefore, HTTP requests are ignored.</span></span> <span data-ttu-id="58c63-193">Následující zvýrazněný kód přesměruje všechny požadavky HTTP na HTTPS:</span><span class="sxs-lookup"><span data-stu-id="58c63-193">The following highlighted code redirects all HTTP requests to HTTPS:</span></span>

[!code-csharp[](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-999)]

<span data-ttu-id="58c63-194">Další informace najdete v tématu [Middleware pro přepis adres URL](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="58c63-194">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span> <span data-ttu-id="58c63-195">Middleware také umožňuje, že aplikace nastaví stavový kód nebo kód stavu a port, který při spuštění přesměrování.</span><span class="sxs-lookup"><span data-stu-id="58c63-195">The middleware also permits the app to set the status code or the status code and the port when the redirect is executed.</span></span>

<span data-ttu-id="58c63-196">Globálně vyžadování protokolu HTTPS (`options.Filters.Add(new RequireHttpsAttribute());`) je z bezpečnostních důvodů.</span><span class="sxs-lookup"><span data-stu-id="58c63-196">Requiring HTTPS globally (`options.Filters.Add(new RequireHttpsAttribute());`) is a security best practice.</span></span> <span data-ttu-id="58c63-197">Použití `[RequireHttps]` atribut na všechny řadiče/Razor Pages není považován za stejně bezpečné jako globálně vyžadování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="58c63-197">Applying the `[RequireHttps]` attribute to all controllers/Razor Pages isn't considered as secure as requiring HTTPS globally.</span></span> <span data-ttu-id="58c63-198">Nebudete moct zaručit, `[RequireHttps]` atributu se použije při přidání nových řadičů a stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="58c63-198">You can't guarantee the `[RequireHttps]` attribute is applied when new controllers and Razor Pages are added.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="58c63-199">Protokol zabezpečení striktní přenos HTTP (HSTS)</span><span class="sxs-lookup"><span data-stu-id="58c63-199">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="58c63-200">Za [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [striktní přenos HTTP zabezpečení (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) je vylepšení zabezpečení přihlášení, která je zadána pomocí hlavičky odpovědi webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="58c63-200">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="58c63-201">Když [prohlížeč, který podporuje HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support) obdrží toto záhlaví:</span><span class="sxs-lookup"><span data-stu-id="58c63-201">When a [browser that supports HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support) receives this header:</span></span>

* <span data-ttu-id="58c63-202">Prohlížeč ukládá konfiguraci pro doménu, která zabrání odeslání libovolné komunikaci přes protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="58c63-202">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="58c63-203">Prohlížeč vynutí veškerou komunikaci přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="58c63-203">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="58c63-204">Prohlížeč znemožní uživateli pomocí certifikátů nedůvěryhodný nebo neplatný.</span><span class="sxs-lookup"><span data-stu-id="58c63-204">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="58c63-205">Prohlížeč zakáže výzev, které umožní uživateli tohoto certifikátu dočasně důvěřovat.</span><span class="sxs-lookup"><span data-stu-id="58c63-205">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="58c63-206">Protože klient se vynucuje HSTS má určitá omezení:</span><span class="sxs-lookup"><span data-stu-id="58c63-206">Because HSTS is enforced by the client it has some limitations:</span></span>

* <span data-ttu-id="58c63-207">Klient musí podporovat HSTS.</span><span class="sxs-lookup"><span data-stu-id="58c63-207">The client must support HSTS.</span></span>
* <span data-ttu-id="58c63-208">HSTS vyžaduje alespoň jeden úspěšného požadavku HTTPS k navázání HSTS zásad.</span><span class="sxs-lookup"><span data-stu-id="58c63-208">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="58c63-209">Aplikace musí zkontrolovat každého požadavku HTTP a přesměrování nebo zamítnutí žádosti protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="58c63-209">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="58c63-210">Implementuje HSTS s ASP.NET Core 2.1 nebo vyšší `UseHsts` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="58c63-210">ASP.NET Core 2.1 or later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="58c63-211">Následující kód volá `UseHsts` když aplikace není v [vývojový režim](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="58c63-211">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=10)]

<span data-ttu-id="58c63-212">`UseHsts` není doporučeno při vývoji vzhledem k tomu, že jsou nastavení HSTS dokonalé prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="58c63-212">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="58c63-213">Ve výchozím nastavení `UseHsts` nezahrnuje adresu místní zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="58c63-213">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="58c63-214">Pro produkční prostředí implementace HTTPS poprvé, nastavte počáteční [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) malou hodnotu pomocí jedné z <xref:System.TimeSpan> metody.</span><span class="sxs-lookup"><span data-stu-id="58c63-214">For production environments implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="58c63-215">Nastavte hodnotu hodiny na no více než jeden den v případě, že budete potřebovat obnovit infrastruktury HTTPS do HTTP.</span><span class="sxs-lookup"><span data-stu-id="58c63-215">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="58c63-216">Poté, co jste si jisti udržitelnost konfigurace protokolu HTTPS, zvýšit hodnotu max-age HSTS. běžně používané hodnota je 1 rok.</span><span class="sxs-lookup"><span data-stu-id="58c63-216">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS max-age value; a commonly used value is one year.</span></span>

<span data-ttu-id="58c63-217">Následující kód:</span><span class="sxs-lookup"><span data-stu-id="58c63-217">The following code:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=5-12)]

* <span data-ttu-id="58c63-218">Nastaví parametr přednačtení záhlaví zabezpečení přenosu Strict.</span><span class="sxs-lookup"><span data-stu-id="58c63-218">Sets the preload parameter of the Strict-Transport-Security header.</span></span> <span data-ttu-id="58c63-219">Předinstalační není součástí [specifikaci RFC HSTS](https://tools.ietf.org/html/rfc6797), ale podporuje webových prohlížečů přednačtení HSTS weby na čerstvou instalaci.</span><span class="sxs-lookup"><span data-stu-id="58c63-219">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="58c63-220">Zobrazit [ https://hstspreload.org/ ](https://hstspreload.org/) Další informace.</span><span class="sxs-lookup"><span data-stu-id="58c63-220">See [https://hstspreload.org/](https://hstspreload.org/) for more information.</span></span>
* <span data-ttu-id="58c63-221">Umožňuje [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), které se vztahují zásady HSTS na hostiteli subdomény.</span><span class="sxs-lookup"><span data-stu-id="58c63-221">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="58c63-222">Explicitně nastaví parametr max-age záhlaví zabezpečení přenosu Strict na 60 dnů.</span><span class="sxs-lookup"><span data-stu-id="58c63-222">Explicitly sets the max-age parameter of the Strict-Transport-Security header to 60 days.</span></span> <span data-ttu-id="58c63-223">Pokud není nastavena výchozí hodnota je 30 dní.</span><span class="sxs-lookup"><span data-stu-id="58c63-223">If not set, defaults to 30 days.</span></span> <span data-ttu-id="58c63-224">Najdete v článku [max-age směrnice](https://tools.ietf.org/html/rfc6797#section-6.1.1) Další informace.</span><span class="sxs-lookup"><span data-stu-id="58c63-224">See the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1) for more information.</span></span>
* <span data-ttu-id="58c63-225">Přidá `example.com` do seznamu hostitelů mají vyloučit.</span><span class="sxs-lookup"><span data-stu-id="58c63-225">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="58c63-226">`UseHsts` vyloučí následující zpětné smyčky hostitele:</span><span class="sxs-lookup"><span data-stu-id="58c63-226">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="58c63-227">`localhost` : IPv4 adresu zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="58c63-227">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="58c63-228">`127.0.0.1` : IPv4 adresu zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="58c63-228">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="58c63-229">`[::1]` : IPv6 adresu zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="58c63-229">`[::1]` : The IPv6 loopback address.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="58c63-230">Odhlásit HTTPS/HSTS při vytvoření projektu</span><span class="sxs-lookup"><span data-stu-id="58c63-230">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="58c63-231">V některých scénářích služby back-endu kde zabezpečení připojení probíhá v veřejnou hraniční části sítě není vyžadována konfigurace zabezpečení připojení v každém uzlu.</span><span class="sxs-lookup"><span data-stu-id="58c63-231">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="58c63-232">Webová aplikace vygenerované ze šablony v sadě Visual Studio nebo z [dotnet nové](/dotnet/core/tools/dotnet-new) povolit příkaz [HTTPS přesměrování](#require-https) a [HSTS](#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="58c63-232">Web apps generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="58c63-233">Pro nasazení, které nevyžadují tyto scénáře které můžete výslovný nesouhlas s HTTPS/HSTS při vytvoření aplikace ze šablony.</span><span class="sxs-lookup"><span data-stu-id="58c63-233">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="58c63-234">Chcete odhlásit HTTPS/HSTS:</span><span class="sxs-lookup"><span data-stu-id="58c63-234">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="58c63-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58c63-235">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="58c63-236">Zrušte zaškrtnutí políčka **konfigurace pro protokol HTTPS** zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="58c63-236">Uncheck the **Configure for HTTPS** check box.</span></span>

![Nové webové aplikace ASP.NET Core dialogové okno zobrazující konfigurací pro HTTPS zaškrtávací políčko nevybranou.](enforcing-ssl/_static/out.png)

#   <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="58c63-238">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="58c63-238">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="58c63-239">Použití `--no-https` možnost.</span><span class="sxs-lookup"><span data-stu-id="58c63-239">Use the `--no-https` option.</span></span> <span data-ttu-id="58c63-240">Příklad</span><span class="sxs-lookup"><span data-stu-id="58c63-240">For example</span></span>

```console
dotnet new webapp --no-https
```

---

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="58c63-241">Důvěřovat certifikátu vývoj pro ASP.NET Core HTTPS ve Windows a macOS</span><span class="sxs-lookup"><span data-stu-id="58c63-241">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="58c63-242">Sada .NET core SDK zahrnuje vývoj nesprávný certifikát HTTPS.</span><span class="sxs-lookup"><span data-stu-id="58c63-242">.NET Core SDK includes a HTTPS development certificate.</span></span> <span data-ttu-id="58c63-243">Certifikát je nainstalován jako součást prvního spuštění.</span><span class="sxs-lookup"><span data-stu-id="58c63-243">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="58c63-244">Například `dotnet --info` vytváří výstup podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="58c63-244">For example, `dotnet --info` produces output similar to the following:</span></span>

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="58c63-245">Instalace sady .NET Core SDK nainstaluje certifikát pro vývoj ASP.NET Core HTTPS do úložiště certifikátů místního uživatele.</span><span class="sxs-lookup"><span data-stu-id="58c63-245">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="58c63-246">Certifikát je nainstalovaný, ale není důvěryhodný.</span><span class="sxs-lookup"><span data-stu-id="58c63-246">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="58c63-247">Důvěřovat certifikátu provést jednorázové krok a spusťte dotnet `dev-certs` nástroje:</span><span class="sxs-lookup"><span data-stu-id="58c63-247">To trust the certificate perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="58c63-248">Následující příkaz nápovědy týkající `dev-certs` nástroje:</span><span class="sxs-lookup"><span data-stu-id="58c63-248">The following command provides help on the `dev-certs` tool:</span></span>

```console
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="58c63-249">Jak nastavit certifikát pro vývojáře pro Docker</span><span class="sxs-lookup"><span data-stu-id="58c63-249">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="58c63-250">Zobrazit [tento problém Githubu](https://github.com/aspnet/Docs/issues/6199).</span><span class="sxs-lookup"><span data-stu-id="58c63-250">See [this GitHub issue](https://github.com/aspnet/Docs/issues/6199).</span></span>

::: moniker-end

## <a name="additional-information"></a><span data-ttu-id="58c63-251">Další informace</span><span class="sxs-lookup"><span data-stu-id="58c63-251">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="58c63-252">Hostování ASP.NET Core v Linuxu pomocí Apache: Konfigurace protokolu SSL</span><span class="sxs-lookup"><span data-stu-id="58c63-252">Host ASP.NET Core on Linux with Apache: SSL configuration</span></span>](xref:host-and-deploy/linux-apache#ssl-configuration)
* [<span data-ttu-id="58c63-253">Hostování ASP.NET Core v Linuxu se serverem Nginx: Konfigurace protokolu SSL</span><span class="sxs-lookup"><span data-stu-id="58c63-253">Host ASP.NET Core on Linux with Nginx: SSL configuration</span></span>](xref:host-and-deploy/linux-nginx#configure-ssl)
* [<span data-ttu-id="58c63-254">Nastavení protokolu SSL ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="58c63-254">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="58c63-255">Podpora prohlížeče OWASP HSTS</span><span class="sxs-lookup"><span data-stu-id="58c63-255">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
