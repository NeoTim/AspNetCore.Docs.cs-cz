---
title: Middleware pro přepis adres URL v ASP.NET Core
author: rick-anderson
description: Přečtěte si informace o přepsání adresy URL a přesměrování pomocí middlewaru pro přepis adres URL v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/url-rewriting
ms.openlocfilehash: c41ba2e067c8dc978b1359db548733c5f8890a2b
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408380"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="da414-103">Middleware pro přepis adres URL v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da414-103">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="da414-104">Od [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="da414-104">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="da414-105">Tento dokument zavádí přepis adres URL s pokyny k použití middlewaru přepisu adresy URL v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="da414-105">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="da414-106">Přepsání adresy URL je to, že se jedná o úpravu adres URL žádostí na základě jednoho nebo více předdefinovaných pravidel.</span><span class="sxs-lookup"><span data-stu-id="da414-106">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="da414-107">Přepsání adresy URL vytvoří abstrakci mezi umístěními prostředků a jejich adresami tak, aby umístění a adresy nebyly pevně propojené.</span><span class="sxs-lookup"><span data-stu-id="da414-107">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="da414-108">Přepsání adresy URL je v několika scénářích užitečné pro:</span><span class="sxs-lookup"><span data-stu-id="da414-108">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="da414-109">Dočasné nebo trvalé přesunutí nebo nahrazení prostředků serveru a udržování stabilních lokátorů pro tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="da414-109">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="da414-110">Rozdělte zpracování požadavků napříč různými aplikacemi nebo napříč oblastmi jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="da414-110">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="da414-111">Umožňuje odebrat, přidat nebo změnit uspořádání segmentů adresy URL příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="da414-111">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="da414-112">Optimalizujte veřejné adresy URL pro optimalizaci vyhledávače (SEO).</span><span class="sxs-lookup"><span data-stu-id="da414-112">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="da414-113">Povolí použití přívětivých veřejných adres URL, které návštěvníkům pomůžou odhadnout obsah vrácený požadavkem na prostředek.</span><span class="sxs-lookup"><span data-stu-id="da414-113">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="da414-114">Přesměrovat nezabezpečené požadavky na zabezpečené koncové body.</span><span class="sxs-lookup"><span data-stu-id="da414-114">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="da414-115">Zabránit hotlinking, kde externí lokalita používá hostovaný statický prostředek na jiné lokalitě propojením prostředku s vlastním obsahem.</span><span class="sxs-lookup"><span data-stu-id="da414-115">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="da414-116">Přepsání adresy URL může snížit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="da414-116">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="da414-117">Pokud je to možné, omezte počet a složitost pravidel.</span><span class="sxs-lookup"><span data-stu-id="da414-117">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="da414-118">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="da414-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="da414-119">Přesměrování adresy URL a přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="da414-119">URL redirect and URL rewrite</span></span>

<span data-ttu-id="da414-120">Rozdíl mezi přesměrováním *URL* a *přepsáním adresy URL* je malý, ale má důležitý dopad na poskytování prostředků klientům.</span><span class="sxs-lookup"><span data-stu-id="da414-120">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="da414-121">Rozhraní middleware pro přepis adres URL ASP.NET Core je schopné splnit nutnost obou.</span><span class="sxs-lookup"><span data-stu-id="da414-121">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="da414-122">*Přesměrování adresy URL* zahrnuje i operaci na straně klienta, kde klient má pokyn k přístupu k prostředku na jiné adrese, než jakou požadoval klient původně.</span><span class="sxs-lookup"><span data-stu-id="da414-122">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="da414-123">To vyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="da414-123">This requires a round trip to the server.</span></span> <span data-ttu-id="da414-124">Adresa URL pro přesměrování vracená klientovi se zobrazí v adresním řádku prohlížeče, když klient vytvoří nový požadavek na prostředek.</span><span class="sxs-lookup"><span data-stu-id="da414-124">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="da414-125">Pokud `/resource` je *přesměrován* na `/different-resource` , server odpoví, že by měl klient získat prostředek na `/different-resource` stavovém kódu, což značí, že přesměrování je buď dočasné, nebo trvalé.</span><span class="sxs-lookup"><span data-stu-id="da414-125">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Koncový bod služby WebAPI se dočasně změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="da414-131">Při přesměrování požadavků na jinou adresu URL určete, zda je přesměrování trvalé nebo dočasné, zadáním stavového kódu s odpovědí:</span><span class="sxs-lookup"><span data-stu-id="da414-131">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="da414-132">V případě, že má prostředek novou, trvalou adresu URL a chcete dát klientovi pokyn, aby všechny budoucí požadavky na prostředek používaly novou adresu URL, se použije kód *trvale přesunutý stav 301* .</span><span class="sxs-lookup"><span data-stu-id="da414-132">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="da414-133">*Klient může ukládat odpověď do mezipaměti a znovu ji použít, když se přijme stavový kód 301.*</span><span class="sxs-lookup"><span data-stu-id="da414-133">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="da414-134">V případě, že je přesměrování dočasné nebo obecně se může změnit, je použit stavový kód *302* .</span><span class="sxs-lookup"><span data-stu-id="da414-134">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="da414-135">Stavový kód 302 indikuje klientovi, aby neukládal adresu URL a použil ho v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="da414-135">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="da414-136">Další informace o stavových kódech najdete v [dokumentu RFC 2616: definice stavového kódu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="da414-136">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="da414-137">*Přepsání adresy URL* je operace na straně serveru, která poskytuje prostředek z jiné adresy prostředku, než jakou požaduje klient.</span><span class="sxs-lookup"><span data-stu-id="da414-137">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="da414-138">Přepsání adresy URL nevyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="da414-138">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="da414-139">Přepsaná adresa URL se nevrátí klientovi a nezobrazí se v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="da414-139">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="da414-140">Pokud `/resource` je *přepsáno* na `/different-resource` , server *interně* načte a vrátí prostředek na `/different-resource` .</span><span class="sxs-lookup"><span data-stu-id="da414-140">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="da414-141">I když klient může být schopný načíst prostředek na adrese URL přepsané adresy, klient nebude informovat o tom, že prostředek existuje na přepsané adrese URL, když odešle svůj požadavek a obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="da414-141">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Koncový bod služby WebAPI se změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="da414-146">Ukázková aplikace pro přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="da414-146">URL rewriting sample app</span></span>

<span data-ttu-id="da414-147">Pomocí [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)můžete prozkoumat funkce middlewaru přepsaného adresou URL.</span><span class="sxs-lookup"><span data-stu-id="da414-147">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="da414-148">Aplikace použije pravidla přesměrování a přepisu a zobrazí přesměrovanou nebo přepsanou adresu URL pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="da414-148">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="da414-149">Kdy použít middleware přepisu adresy URL</span><span class="sxs-lookup"><span data-stu-id="da414-149">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="da414-150">Pokud nemůžete použít následující přístupy, použijte middleware přepisující adresu URL:</span><span class="sxs-lookup"><span data-stu-id="da414-150">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="da414-151">Modul pro přepsání adresy URL ve službě IIS v systému Windows Server</span><span class="sxs-lookup"><span data-stu-id="da414-151">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="da414-152">Apache mod_rewrite modul na serveru Apache</span><span class="sxs-lookup"><span data-stu-id="da414-152">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="da414-153">Přepsání adresy URL na Nginx</span><span class="sxs-lookup"><span data-stu-id="da414-153">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="da414-154">Používejte také middleware při hostování aplikace na [serveruHTTP.sys](xref:fundamentals/servers/httpsys) (dříve nazývaném weblisten).</span><span class="sxs-lookup"><span data-stu-id="da414-154">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="da414-155">Hlavními důvody pro použití technologie přepisování adres URL založené na serveru ve službě IIS, Apache a Nginx jsou:</span><span class="sxs-lookup"><span data-stu-id="da414-155">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="da414-156">Middleware nepodporuje všechny funkce těchto modulů.</span><span class="sxs-lookup"><span data-stu-id="da414-156">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="da414-157">Některé funkce v modulech serveru nefungují s ASP.NET Core projekty, jako jsou `IsFile` `IsDirectory` omezení a modulu IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="da414-157">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="da414-158">V těchto scénářích místo toho použijte middleware.</span><span class="sxs-lookup"><span data-stu-id="da414-158">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="da414-159">Výkon middleware se pravděpodobně neshoduje s modulem.</span><span class="sxs-lookup"><span data-stu-id="da414-159">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="da414-160">Srovnávací testy jsou jediným způsobem, jak zjistit, který přístup snižuje výkon, který je v nejvyšší nebo v případě, že je snížený výkon.</span><span class="sxs-lookup"><span data-stu-id="da414-160">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="da414-161">Balíček</span><span class="sxs-lookup"><span data-stu-id="da414-161">Package</span></span>

<span data-ttu-id="da414-162">Middleware pro přepis adres URL poskytuje balíček [Microsoft. AspNetCore. Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) , který je implicitně zahrnutý v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="da414-162">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="da414-163">Rozšíření a možnosti</span><span class="sxs-lookup"><span data-stu-id="da414-163">Extension and options</span></span>

<span data-ttu-id="da414-164">Vytvořte pravidla přepsání a přesměrování adresy URL vytvořením instance třídy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) s metodami rozšíření pro každé pravidlo přepsání.</span><span class="sxs-lookup"><span data-stu-id="da414-164">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="da414-165">Řetězení více pravidel v pořadí, ve kterém se mají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="da414-165">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="da414-166">Rozhraní se `RewriteOptions` předává do middleware přepisování adresy URL při jeho přidání do kanálu požadavků pomocí <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :</span><span class="sxs-lookup"><span data-stu-id="da414-166">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="da414-167">Přesměrovat neexistující na webovou</span><span class="sxs-lookup"><span data-stu-id="da414-167">Redirect non-www to www</span></span>

<span data-ttu-id="da414-168">Tři možnosti umožňují, aby aplikace přesměrovala `www` požadavky bez požadavků na `www` :</span><span class="sxs-lookup"><span data-stu-id="da414-168">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="da414-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Trvale přesměrujte požadavek na `www` subdoménu, pokud je požadavek jiný než `www` .</span><span class="sxs-lookup"><span data-stu-id="da414-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="da414-170">Přesměruje stavový kód [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) .</span><span class="sxs-lookup"><span data-stu-id="da414-170">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="da414-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Přesměrujte požadavek na `www` subdoménu, pokud je příchozí požadavek jiný než `www` .</span><span class="sxs-lookup"><span data-stu-id="da414-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="da414-172">Přesměruje stavový kód [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) .</span><span class="sxs-lookup"><span data-stu-id="da414-172">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="da414-173">Přetížení umožňuje poskytnout stavový kód pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="da414-173">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="da414-174">Použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy pro přiřazení stavového kódu.</span><span class="sxs-lookup"><span data-stu-id="da414-174">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="da414-175">Přesměrování adresy URL</span><span class="sxs-lookup"><span data-stu-id="da414-175">URL redirect</span></span>

<span data-ttu-id="da414-176">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> k přesměrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="da414-176">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="da414-177">První parametr obsahuje váš regulární výraz pro porovnání s cestou příchozí adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da414-177">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="da414-178">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="da414-178">The second parameter is the replacement string.</span></span> <span data-ttu-id="da414-179">Třetí parametr, pokud je k dispozici, určuje stavový kód.</span><span class="sxs-lookup"><span data-stu-id="da414-179">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="da414-180">Pokud nezadáte stavový kód, stavový kód se standardně *302 – Nalezeno*, což znamená, že prostředek je dočasně přesunut nebo nahrazen.</span><span class="sxs-lookup"><span data-stu-id="da414-180">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="da414-181">V prohlížeči s povolenými vývojářskými nástroji vytvořte žádost o ukázkovou aplikaci s cestou `/redirect-rule/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="da414-181">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="da414-182">Regulární výraz odpovídá cestě požadavku na `redirect-rule/(.*)` a cesta je nahrazena řetězcem `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="da414-182">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="da414-183">Adresa URL pro přesměrování se pošle zpátky do klienta s kódem stavu 302, který se *našel* .</span><span class="sxs-lookup"><span data-stu-id="da414-183">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="da414-184">Prohlížeč vytvoří novou žádost na adrese URL pro přesměrování, která se zobrazí v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="da414-184">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="da414-185">Vzhledem k tomu, že se žádná pravidla v ukázkové aplikaci neshodují s adresou URL pro přesměrování:</span><span class="sxs-lookup"><span data-stu-id="da414-185">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="da414-186">Druhá žádost obdrží odpověď *200-OK* od aplikace.</span><span class="sxs-lookup"><span data-stu-id="da414-186">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="da414-187">Tělo odpovědi zobrazuje adresu URL pro přesměrování.</span><span class="sxs-lookup"><span data-stu-id="da414-187">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="da414-188">Při *přesměrování*adresy URL se na Server provede cyklická výměna.</span><span class="sxs-lookup"><span data-stu-id="da414-188">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="da414-189">Buďte opatrní při vytváření pravidel přesměrování.</span><span class="sxs-lookup"><span data-stu-id="da414-189">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="da414-190">Pravidla přesměrování se vyhodnocují při každém požadavku na aplikaci, včetně po přesměrování.</span><span class="sxs-lookup"><span data-stu-id="da414-190">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="da414-191">Je snadné vytvořit *smyčku nekonečných přesměrování*.</span><span class="sxs-lookup"><span data-stu-id="da414-191">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="da414-192">Původní požadavek:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="da414-192">Original Request: `/redirect-rule/1234/5678`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="da414-194">Část výrazu obsažená v závorkách se nazývá *Skupina zachycení*.</span><span class="sxs-lookup"><span data-stu-id="da414-194">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="da414-195">Tečka ( `.` ) výrazu znamená, že *odpovídá libovolnému znaku*.</span><span class="sxs-lookup"><span data-stu-id="da414-195">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="da414-196">Hvězdička ( `*` ) značí, *že se předchozí znak rovná nule nebo vícekrát*.</span><span class="sxs-lookup"><span data-stu-id="da414-196">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="da414-197">Proto jsou poslední dva segmenty cesty adresy URL `1234/5678` zachyceny skupinou zachycení `(.*)` .</span><span class="sxs-lookup"><span data-stu-id="da414-197">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="da414-198">Jakákoli hodnota, kterou zadáte v adrese URL požadavku, potom, co `redirect-rule/` je tato jediná skupina zachycení zachycena.</span><span class="sxs-lookup"><span data-stu-id="da414-198">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="da414-199">V řetězci pro nahrazení jsou zachycené skupiny vloženy do řetězce s znakem dolaru ( `$` ) následovaným pořadovým číslem záznamu.</span><span class="sxs-lookup"><span data-stu-id="da414-199">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="da414-200">První hodnota skupiny zachycení je získána s `$1` , druhým s `$2` a pokračuje v pořadí pro skupiny zachycení ve vašem regulárním výrazu.</span><span class="sxs-lookup"><span data-stu-id="da414-200">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="da414-201">Regulární výraz pravidla přesměrování v ukázkové aplikaci obsahuje jenom jednu zachycenou skupinu, takže v náhradním řetězci je jenom jedna vložená skupina, která je `$1` .</span><span class="sxs-lookup"><span data-stu-id="da414-201">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="da414-202">Po použití pravidla se adresa URL bude nacházet `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="da414-202">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="da414-203">Přesměrování adresy URL na zabezpečený koncový bod</span><span class="sxs-lookup"><span data-stu-id="da414-203">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="da414-204">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> k přesměrování požadavků HTTP na stejného hostitele a cestu pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="da414-204">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="da414-205">Pokud kód stavu není zadaný, použije se ve výchozím nastavení middleware *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="da414-205">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="da414-206">Pokud port není dodán:</span><span class="sxs-lookup"><span data-stu-id="da414-206">If the port isn't supplied:</span></span>

* <span data-ttu-id="da414-207">Ve výchozím nastavení se používá middleware `null` .</span><span class="sxs-lookup"><span data-stu-id="da414-207">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="da414-208">Schéma se změní na `https` (protokol HTTPS) a klient přistupuje k prostředku na portu 443.</span><span class="sxs-lookup"><span data-stu-id="da414-208">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="da414-209">Následující příklad ukazuje, jak nastavit stavový kód na *301 – trvale přesunuto* a změnit port na 5001.</span><span class="sxs-lookup"><span data-stu-id="da414-209">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="da414-210">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> k přesměrování nezabezpečených požadavků na stejného hostitele a cestu s protokolem Secure https na portu 443.</span><span class="sxs-lookup"><span data-stu-id="da414-210">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="da414-211">Middleware nastaví stavový kód na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="da414-211">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="da414-212">Při přesměrování na zabezpečený koncový bod bez požadavku na další pravidla přesměrování doporučujeme použít middleware pro přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="da414-212">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="da414-213">Další informace najdete v tématu věnovaném [vykonání protokolu HTTPS](xref:security/enforcing-ssl#require-https) .</span><span class="sxs-lookup"><span data-stu-id="da414-213">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="da414-214">Ukázková aplikace dokáže demonstrovat, jak používat `AddRedirectToHttps` nebo `AddRedirectToHttpsPermanent` .</span><span class="sxs-lookup"><span data-stu-id="da414-214">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="da414-215">Přidejte metodu rozšíření do `RewriteOptions` .</span><span class="sxs-lookup"><span data-stu-id="da414-215">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="da414-216">Vytvořte nezabezpečenou žádost o aplikaci na libovolné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="da414-216">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="da414-217">Zrušte upozornění zabezpečení prohlížeče, že certifikát podepsaný svým držitelem je nedůvěryhodný, nebo vytvořte výjimku pro důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="da414-217">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="da414-218">Původní požadavek s použitím `AddRedirectToHttps(301, 5001)` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="da414-218">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="da414-220">Původní požadavek s použitím `AddRedirectToHttpsPermanent` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="da414-220">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="da414-222">Přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="da414-222">URL rewrite</span></span>

<span data-ttu-id="da414-223">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> k vytvoření pravidla pro přepis adres URL.</span><span class="sxs-lookup"><span data-stu-id="da414-223">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="da414-224">První parametr obsahuje regulární výraz pro porovnání na cestě příchozích adres URL.</span><span class="sxs-lookup"><span data-stu-id="da414-224">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="da414-225">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="da414-225">The second parameter is the replacement string.</span></span> <span data-ttu-id="da414-226">Třetí parametr, `skipRemainingRules: {true|false}` označuje middleware bez ohledu na to, zda má přeskočit další pravidla přepsání, pokud je použito aktuální pravidlo.</span><span class="sxs-lookup"><span data-stu-id="da414-226">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="da414-227">Původní požadavek:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="da414-227">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="da414-229">Kosočtverce ( `^` ) na začátku výrazu znamená, že shoda začíná na začátku cesty URL.</span><span class="sxs-lookup"><span data-stu-id="da414-229">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="da414-230">V předchozím příkladu s pravidlem přesměrování `redirect-rule/(.*)` není `^` na začátku regulárního výrazu k dispozici žádná kosočtverce ().</span><span class="sxs-lookup"><span data-stu-id="da414-230">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="da414-231">Proto jakékoli znaky mohou předcházet `redirect-rule/` v cestě k úspěšné shodě.</span><span class="sxs-lookup"><span data-stu-id="da414-231">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="da414-232">Cesta</span><span class="sxs-lookup"><span data-stu-id="da414-232">Path</span></span>                               | <span data-ttu-id="da414-233">Shoda</span><span class="sxs-lookup"><span data-stu-id="da414-233">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="da414-234">Ano</span><span class="sxs-lookup"><span data-stu-id="da414-234">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="da414-235">Ano</span><span class="sxs-lookup"><span data-stu-id="da414-235">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="da414-236">Ano</span><span class="sxs-lookup"><span data-stu-id="da414-236">Yes</span></span>   |

<span data-ttu-id="da414-237">Pravidlo přepsaného zápisu `^rewrite-rule/(\d+)/(\d+)` odpovídá pouze cestám, pokud začínají na `rewrite-rule/` .</span><span class="sxs-lookup"><span data-stu-id="da414-237">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="da414-238">V následující tabulce si všimněte rozdílu v porovnání.</span><span class="sxs-lookup"><span data-stu-id="da414-238">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="da414-239">Cesta</span><span class="sxs-lookup"><span data-stu-id="da414-239">Path</span></span>                              | <span data-ttu-id="da414-240">Shoda</span><span class="sxs-lookup"><span data-stu-id="da414-240">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="da414-241">Yes</span><span class="sxs-lookup"><span data-stu-id="da414-241">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="da414-242">Ne</span><span class="sxs-lookup"><span data-stu-id="da414-242">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="da414-243">Ne</span><span class="sxs-lookup"><span data-stu-id="da414-243">No</span></span>    |

<span data-ttu-id="da414-244">Po `^rewrite-rule/` části výrazu jsou k dispozici dvě skupiny zachycení, `(\d+)/(\d+)` .</span><span class="sxs-lookup"><span data-stu-id="da414-244">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="da414-245">`\d`Značí *shodu s číslicí (číslem)*.</span><span class="sxs-lookup"><span data-stu-id="da414-245">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="da414-246">Znaménko plus ( `+` ) znamená, že se *shoduje s jedním nebo více předcházejícím znakem*.</span><span class="sxs-lookup"><span data-stu-id="da414-246">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="da414-247">Proto musí adresa URL obsahovat číslo následované lomítkem, za kterým následuje jiné číslo.</span><span class="sxs-lookup"><span data-stu-id="da414-247">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="da414-248">Tyto skupiny zachycení jsou vloženy do přepsané adresy URL jako `$1` a `$2` .</span><span class="sxs-lookup"><span data-stu-id="da414-248">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="da414-249">Náhradní řetězec pravidla přepsaní umístí zachycené skupiny do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="da414-249">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="da414-250">Požadovaná cesta k `/rewrite-rule/1234/5678` je přepsána za účelem získání prostředku v `/rewritten?var1=1234&var2=5678` .</span><span class="sxs-lookup"><span data-stu-id="da414-250">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="da414-251">Pokud se v původním požadavku nachází řetězec dotazu, bude při přepisování adresy URL zachován.</span><span class="sxs-lookup"><span data-stu-id="da414-251">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="da414-252">K získání prostředku neexistuje žádná Přenosová cesta k serveru.</span><span class="sxs-lookup"><span data-stu-id="da414-252">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="da414-253">Pokud prostředek existuje, načte se a vrátí do klienta s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="da414-253">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="da414-254">Vzhledem k tomu, že klient není přesměrován, adresa URL v adresním řádku prohlížeče se nezmění.</span><span class="sxs-lookup"><span data-stu-id="da414-254">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="da414-255">Klienti nerozpoznají, že na serveru došlo k operaci přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da414-255">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="da414-256">`skipRemainingRules: true`Kdykoli je to možné, protože vyhovující pravidla jsou výpočetně náročná a zvyšují dobu odezvy aplikace.</span><span class="sxs-lookup"><span data-stu-id="da414-256">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="da414-257">Pro nejrychlejší aplikační odpověď:</span><span class="sxs-lookup"><span data-stu-id="da414-257">For the fastest app response:</span></span>
>
> * <span data-ttu-id="da414-258">Seřazení pravidel přepisu z nejčastěji spárovaného pravidla na nejméně často spárované pravidlo.</span><span class="sxs-lookup"><span data-stu-id="da414-258">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="da414-259">Pokud dojde ke shodě a není vyžadováno žádné další zpracování pravidla, přeskočte zpracování zbývajících pravidel.</span><span class="sxs-lookup"><span data-stu-id="da414-259">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="da414-260">Mod_rewrite Apache</span><span class="sxs-lookup"><span data-stu-id="da414-260">Apache mod_rewrite</span></span>

<span data-ttu-id="da414-261">Použijte pravidla mod_rewrite Apache s <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="da414-261">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="da414-262">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="da414-262">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="da414-263">Další informace a příklady pravidel mod_rewrite najdete v tématu [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="da414-263">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="da414-264"><xref:System.IO.StreamReader>Používá se ke čtení pravidel ze souboru pravidel *ApacheModRewrite.txt* :</span><span class="sxs-lookup"><span data-stu-id="da414-264">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="da414-265">Ukázková aplikace přesměrovává požadavky od `/apache-mod-rules-redirect/(.\*)` do `/redirected?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="da414-265">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="da414-266">Stavový kód odpovědi je *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="da414-266">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="da414-267">Původní požadavek:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="da414-267">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="da414-269">Middleware podporuje následující proměnné serveru Apache mod_rewrite:</span><span class="sxs-lookup"><span data-stu-id="da414-269">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="da414-270">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="da414-270">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="da414-271">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="da414-271">HTTP_ACCEPT</span></span>
* <span data-ttu-id="da414-272">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="da414-272">HTTP_CONNECTION</span></span>
* <span data-ttu-id="da414-273">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="da414-273">HTTP_COOKIE</span></span>
* <span data-ttu-id="da414-274">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="da414-274">HTTP_FORWARDED</span></span>
* <span data-ttu-id="da414-275">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="da414-275">HTTP_HOST</span></span>
* <span data-ttu-id="da414-276">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="da414-276">HTTP_REFERER</span></span>
* <span data-ttu-id="da414-277">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="da414-277">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="da414-278">HTTPS</span><span class="sxs-lookup"><span data-stu-id="da414-278">HTTPS</span></span>
* <span data-ttu-id="da414-279">PROTOKOLŮ</span><span class="sxs-lookup"><span data-stu-id="da414-279">IPV6</span></span>
* <span data-ttu-id="da414-280">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="da414-280">QUERY_STRING</span></span>
* <span data-ttu-id="da414-281">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="da414-281">REMOTE_ADDR</span></span>
* <span data-ttu-id="da414-282">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="da414-282">REMOTE_PORT</span></span>
* <span data-ttu-id="da414-283">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="da414-283">REQUEST_FILENAME</span></span>
* <span data-ttu-id="da414-284">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="da414-284">REQUEST_METHOD</span></span>
* <span data-ttu-id="da414-285">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="da414-285">REQUEST_SCHEME</span></span>
* <span data-ttu-id="da414-286">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="da414-286">REQUEST_URI</span></span>
* <span data-ttu-id="da414-287">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="da414-287">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="da414-288">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="da414-288">SERVER_ADDR</span></span>
* <span data-ttu-id="da414-289">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="da414-289">SERVER_PORT</span></span>
* <span data-ttu-id="da414-290">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="da414-290">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="da414-291">ČAS</span><span class="sxs-lookup"><span data-stu-id="da414-291">TIME</span></span>
* <span data-ttu-id="da414-292">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="da414-292">TIME_DAY</span></span>
* <span data-ttu-id="da414-293">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="da414-293">TIME_HOUR</span></span>
* <span data-ttu-id="da414-294">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="da414-294">TIME_MIN</span></span>
* <span data-ttu-id="da414-295">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="da414-295">TIME_MON</span></span>
* <span data-ttu-id="da414-296">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="da414-296">TIME_SEC</span></span>
* <span data-ttu-id="da414-297">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="da414-297">TIME_WDAY</span></span>
* <span data-ttu-id="da414-298">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="da414-298">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="da414-299">Pravidla pro přepsání adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="da414-299">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="da414-300">Pokud chcete použít stejnou sadu pravidel, která se vztahuje na modul IIS URL Rewrite, použijte <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="da414-300">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="da414-301">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="da414-301">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="da414-302">Nesměrujte middleware na použití *web.config* souboru aplikace při spuštění na Windows serveru IIS.</span><span class="sxs-lookup"><span data-stu-id="da414-302">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="da414-303">V případě služby IIS by tato pravidla měla být uložena mimo soubor *web.config* aplikace, aby nedocházelo ke konfliktům s modulem PŘEPISU služby IIS.</span><span class="sxs-lookup"><span data-stu-id="da414-303">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="da414-304">Další informace a příklady pravidel pro přepis adres URL služby IIS najdete v tématu použití odkazu na modul pro [přepis adres url 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [odkaz na konfiguraci modulu přepisu adresy](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)URL.</span><span class="sxs-lookup"><span data-stu-id="da414-304">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="da414-305"><xref:System.IO.StreamReader>Používá se ke čtení pravidel ze souboru pravidel *IISUrlRewrite.xml* :</span><span class="sxs-lookup"><span data-stu-id="da414-305">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="da414-306">Ukázková aplikace přepíše požadavky od `/iis-rules-rewrite/(.*)` do `/rewritten?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="da414-306">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="da414-307">Odpověď se pošle klientovi s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="da414-307">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="da414-308">Původní požadavek:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="da414-308">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="da414-310">Pokud máte aktivní modul pro přepis služby IIS s nakonfigurovanými pravidly na úrovni serveru, který by ovlivnil vaši aplikaci nežádoucím způsobem, můžete pro aplikaci zakázat modul IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="da414-310">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="da414-311">Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="da414-311">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="da414-312">Nepodporované funkce</span><span class="sxs-lookup"><span data-stu-id="da414-312">Unsupported features</span></span>

<span data-ttu-id="da414-313">Middleware vydaná ASP.NET Core 2. x nepodporuje následující funkce modulu pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="da414-313">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="da414-314">Pravidla pro odchozí provoz</span><span class="sxs-lookup"><span data-stu-id="da414-314">Outbound Rules</span></span>
* <span data-ttu-id="da414-315">Vlastní proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="da414-315">Custom Server Variables</span></span>
* <span data-ttu-id="da414-316">Zástupné znaky</span><span class="sxs-lookup"><span data-stu-id="da414-316">Wildcards</span></span>
* <span data-ttu-id="da414-317">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="da414-317">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="da414-318">Podporované proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="da414-318">Supported server variables</span></span>

<span data-ttu-id="da414-319">Middleware podporuje následující proměnné serveru pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="da414-319">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="da414-320">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="da414-320">CONTENT_LENGTH</span></span>
* <span data-ttu-id="da414-321">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="da414-321">CONTENT_TYPE</span></span>
* <span data-ttu-id="da414-322">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="da414-322">HTTP_ACCEPT</span></span>
* <span data-ttu-id="da414-323">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="da414-323">HTTP_CONNECTION</span></span>
* <span data-ttu-id="da414-324">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="da414-324">HTTP_COOKIE</span></span>
* <span data-ttu-id="da414-325">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="da414-325">HTTP_HOST</span></span>
* <span data-ttu-id="da414-326">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="da414-326">HTTP_REFERER</span></span>
* <span data-ttu-id="da414-327">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="da414-327">HTTP_URL</span></span>
* <span data-ttu-id="da414-328">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="da414-328">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="da414-329">HTTPS</span><span class="sxs-lookup"><span data-stu-id="da414-329">HTTPS</span></span>
* <span data-ttu-id="da414-330">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="da414-330">LOCAL_ADDR</span></span>
* <span data-ttu-id="da414-331">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="da414-331">QUERY_STRING</span></span>
* <span data-ttu-id="da414-332">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="da414-332">REMOTE_ADDR</span></span>
* <span data-ttu-id="da414-333">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="da414-333">REMOTE_PORT</span></span>
* <span data-ttu-id="da414-334">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="da414-334">REQUEST_FILENAME</span></span>
* <span data-ttu-id="da414-335">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="da414-335">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="da414-336">Můžete také získat <xref:Microsoft.Extensions.FileProviders.IFileProvider> prostřednictvím <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="da414-336">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="da414-337">Tento přístup může poskytovat větší flexibilitu pro umístění souborů pravidel přepisu.</span><span class="sxs-lookup"><span data-stu-id="da414-337">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="da414-338">Ujistěte se, že jsou v cestě, kterou zadáte, nasazené soubory pravidel přepisu na server.</span><span class="sxs-lookup"><span data-stu-id="da414-338">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="da414-339">Pravidlo založené na metodách</span><span class="sxs-lookup"><span data-stu-id="da414-339">Method-based rule</span></span>

<span data-ttu-id="da414-340">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k implementaci logiky vlastního pravidla v metodě.</span><span class="sxs-lookup"><span data-stu-id="da414-340">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="da414-341">`Add`zpřístupňuje <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> , který zpřístupňuje <xref:Microsoft.AspNetCore.Http.HttpContext> metodu pro použití v metodě.</span><span class="sxs-lookup"><span data-stu-id="da414-341">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="da414-342">[RewriteContext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) určuje, jak se zpracovává další zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="da414-342">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="da414-343">Nastavte hodnotu na jedno z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> polí popsaných v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="da414-343">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="da414-344">Akce</span><span class="sxs-lookup"><span data-stu-id="da414-344">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="da414-345">`RuleResult.ContinueRules`výchozí</span><span class="sxs-lookup"><span data-stu-id="da414-345">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="da414-346">Pokračovat v používání pravidel.</span><span class="sxs-lookup"><span data-stu-id="da414-346">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="da414-347">Zastavení použití pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="da414-347">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="da414-348">Zastavení použití pravidel a odeslání kontextu do dalšího middlewaru.</span><span class="sxs-lookup"><span data-stu-id="da414-348">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="da414-349">Ukázková aplikace ukazuje metodu, která přesměrovává požadavky na cesty, které končí na *. XML*.</span><span class="sxs-lookup"><span data-stu-id="da414-349">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="da414-350">Pokud je žádost určena pro `/file.xml` , je požadavek přesměrován na `/xmlfiles/file.xml` .</span><span class="sxs-lookup"><span data-stu-id="da414-350">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="da414-351">Stavový kód je nastaven na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="da414-351">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="da414-352">Když prohlížeč vytvoří nový požadavek na */xmlfiles/file.xml*, soubor middleware statických souborů obsluhuje soubor do klienta ze složky *wwwroot/xmlfiles* .</span><span class="sxs-lookup"><span data-stu-id="da414-352">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="da414-353">Pro přesměrování explicitně nastavte stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="da414-353">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="da414-354">V opačném případě se vrátí stavový kód *200-OK* a na klientovi se neobjeví přesměrování.</span><span class="sxs-lookup"><span data-stu-id="da414-354">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="da414-355">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="da414-355">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="da414-356">Tento přístup může také přezapisovat požadavky.</span><span class="sxs-lookup"><span data-stu-id="da414-356">This approach can also rewrite requests.</span></span> <span data-ttu-id="da414-357">Ukázková aplikace ukazuje přepis cesty k libovolné žádosti o textový soubor, aby sloužila *file.txt* textový soubor ze složky *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="da414-357">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="da414-358">Middleware statických souborů zachovává soubor na základě aktualizované cesty požadavku:</span><span class="sxs-lookup"><span data-stu-id="da414-358">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="da414-359">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="da414-359">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="da414-360">Pravidlo založené na IRule</span><span class="sxs-lookup"><span data-stu-id="da414-360">IRule-based rule</span></span>

<span data-ttu-id="da414-361">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k použití logiky pravidla ve třídě, která implementuje <xref:Microsoft.AspNetCore.Rewrite.IRule> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="da414-361">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="da414-362">`IRule`poskytuje větší flexibilitu při použití pravidla založeného na metodách.</span><span class="sxs-lookup"><span data-stu-id="da414-362">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="da414-363">Vaše třída implementace může obsahovat konstruktor, který umožňuje předat parametry pro <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> metodu.</span><span class="sxs-lookup"><span data-stu-id="da414-363">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="da414-364">Hodnoty parametrů v ukázkové aplikaci pro `extension` a `newPath` jsou zkontrolovány, aby splňovaly několik podmínek.</span><span class="sxs-lookup"><span data-stu-id="da414-364">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="da414-365">`extension`Musí obsahovat hodnotu a hodnota musí být *. png*, *. jpg*nebo *. gif*.</span><span class="sxs-lookup"><span data-stu-id="da414-365">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="da414-366">Pokud `newPath` není platná, <xref:System.ArgumentException> je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="da414-366">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="da414-367">Pokud se pro *image.png*požadavek provede, požadavek se přesměruje na `/png-images/image.png` .</span><span class="sxs-lookup"><span data-stu-id="da414-367">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="da414-368">Pokud se pro *image.jpg*požadavek provede, požadavek se přesměruje na `/jpg-images/image.jpg` .</span><span class="sxs-lookup"><span data-stu-id="da414-368">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="da414-369">Stavový kód je nastaven na *301 – trvale přesunuto*a `context.Result` je nastavené na zastavení zpracování pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="da414-369">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="da414-370">Původní požadavek:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="da414-370">Original Request: `/image.png`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="da414-372">Původní požadavek:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="da414-372">Original Request: `/image.jpg`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="da414-374">Příklady Regex</span><span class="sxs-lookup"><span data-stu-id="da414-374">Regex examples</span></span>

| <span data-ttu-id="da414-375">Cíl</span><span class="sxs-lookup"><span data-stu-id="da414-375">Goal</span></span> | <span data-ttu-id="da414-376">& řetězce regulárního výrazu</span><span class="sxs-lookup"><span data-stu-id="da414-376">Regex String &</span></span><br><span data-ttu-id="da414-377">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="da414-377">Match Example</span></span> | <span data-ttu-id="da414-378">& řetězce nahrazení</span><span class="sxs-lookup"><span data-stu-id="da414-378">Replacement String &</span></span><br><span data-ttu-id="da414-379">Příklad výstupu</span><span class="sxs-lookup"><span data-stu-id="da414-379">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="da414-380">Přepište cestu do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="da414-380">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="da414-381">Koncové lomítko pruhu</span><span class="sxs-lookup"><span data-stu-id="da414-381">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="da414-382">Vymáhat koncové lomítko</span><span class="sxs-lookup"><span data-stu-id="da414-382">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="da414-383">Vyhnout se přepisu konkrétních požadavků</span><span class="sxs-lookup"><span data-stu-id="da414-383">Avoid rewriting specific requests</span></span> | <span data-ttu-id="da414-384">`^(.*)(?<!\.axd)$` nebo `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="da414-384">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="da414-385">Ano:`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="da414-385">Yes: `/resource.htm`</span></span><br><span data-ttu-id="da414-386">Ne:`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="da414-386">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="da414-387">Změna uspořádání segmentů adresy URL</span><span class="sxs-lookup"><span data-stu-id="da414-387">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="da414-388">Nahradit segment adresy URL</span><span class="sxs-lookup"><span data-stu-id="da414-388">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="da414-389">Tento dokument zavádí přepis adres URL s pokyny k použití middlewaru přepisu adresy URL v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="da414-389">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="da414-390">Přepsání adresy URL je to, že se jedná o úpravu adres URL žádostí na základě jednoho nebo více předdefinovaných pravidel.</span><span class="sxs-lookup"><span data-stu-id="da414-390">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="da414-391">Přepsání adresy URL vytvoří abstrakci mezi umístěními prostředků a jejich adresami tak, aby umístění a adresy nebyly pevně propojené.</span><span class="sxs-lookup"><span data-stu-id="da414-391">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="da414-392">Přepsání adresy URL je v několika scénářích užitečné pro:</span><span class="sxs-lookup"><span data-stu-id="da414-392">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="da414-393">Dočasné nebo trvalé přesunutí nebo nahrazení prostředků serveru a udržování stabilních lokátorů pro tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="da414-393">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="da414-394">Rozdělte zpracování požadavků napříč různými aplikacemi nebo napříč oblastmi jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="da414-394">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="da414-395">Umožňuje odebrat, přidat nebo změnit uspořádání segmentů adresy URL příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="da414-395">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="da414-396">Optimalizujte veřejné adresy URL pro optimalizaci vyhledávače (SEO).</span><span class="sxs-lookup"><span data-stu-id="da414-396">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="da414-397">Povolí použití přívětivých veřejných adres URL, které návštěvníkům pomůžou odhadnout obsah vrácený požadavkem na prostředek.</span><span class="sxs-lookup"><span data-stu-id="da414-397">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="da414-398">Přesměrovat nezabezpečené požadavky na zabezpečené koncové body.</span><span class="sxs-lookup"><span data-stu-id="da414-398">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="da414-399">Zabránit hotlinking, kde externí lokalita používá hostovaný statický prostředek na jiné lokalitě propojením prostředku s vlastním obsahem.</span><span class="sxs-lookup"><span data-stu-id="da414-399">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="da414-400">Přepsání adresy URL může snížit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="da414-400">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="da414-401">Pokud je to možné, omezte počet a složitost pravidel.</span><span class="sxs-lookup"><span data-stu-id="da414-401">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="da414-402">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="da414-402">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="da414-403">Přesměrování adresy URL a přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="da414-403">URL redirect and URL rewrite</span></span>

<span data-ttu-id="da414-404">Rozdíl mezi přesměrováním *URL* a *přepsáním adresy URL* je malý, ale má důležitý dopad na poskytování prostředků klientům.</span><span class="sxs-lookup"><span data-stu-id="da414-404">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="da414-405">Rozhraní middleware pro přepis adres URL ASP.NET Core je schopné splnit nutnost obou.</span><span class="sxs-lookup"><span data-stu-id="da414-405">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="da414-406">*Přesměrování adresy URL* zahrnuje i operaci na straně klienta, kde klient má pokyn k přístupu k prostředku na jiné adrese, než jakou požadoval klient původně.</span><span class="sxs-lookup"><span data-stu-id="da414-406">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="da414-407">To vyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="da414-407">This requires a round trip to the server.</span></span> <span data-ttu-id="da414-408">Adresa URL pro přesměrování vracená klientovi se zobrazí v adresním řádku prohlížeče, když klient vytvoří nový požadavek na prostředek.</span><span class="sxs-lookup"><span data-stu-id="da414-408">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="da414-409">Pokud `/resource` je *přesměrován* na `/different-resource` , server odpoví, že by měl klient získat prostředek na `/different-resource` stavovém kódu, což značí, že přesměrování je buď dočasné, nebo trvalé.</span><span class="sxs-lookup"><span data-stu-id="da414-409">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Koncový bod služby WebAPI se dočasně změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="da414-415">Při přesměrování požadavků na jinou adresu URL určete, zda je přesměrování trvalé nebo dočasné, zadáním stavového kódu s odpovědí:</span><span class="sxs-lookup"><span data-stu-id="da414-415">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="da414-416">V případě, že má prostředek novou, trvalou adresu URL a chcete dát klientovi pokyn, aby všechny budoucí požadavky na prostředek používaly novou adresu URL, se použije kód *trvale přesunutý stav 301* .</span><span class="sxs-lookup"><span data-stu-id="da414-416">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="da414-417">*Klient může ukládat odpověď do mezipaměti a znovu ji použít, když se přijme stavový kód 301.*</span><span class="sxs-lookup"><span data-stu-id="da414-417">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="da414-418">V případě, že je přesměrování dočasné nebo obecně se může změnit, je použit stavový kód *302* .</span><span class="sxs-lookup"><span data-stu-id="da414-418">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="da414-419">Stavový kód 302 indikuje klientovi, aby neukládal adresu URL a použil ho v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="da414-419">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="da414-420">Další informace o stavových kódech najdete v [dokumentu RFC 2616: definice stavového kódu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="da414-420">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="da414-421">*Přepsání adresy URL* je operace na straně serveru, která poskytuje prostředek z jiné adresy prostředku, než jakou požaduje klient.</span><span class="sxs-lookup"><span data-stu-id="da414-421">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="da414-422">Přepsání adresy URL nevyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="da414-422">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="da414-423">Přepsaná adresa URL se nevrátí klientovi a nezobrazí se v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="da414-423">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="da414-424">Pokud `/resource` je *přepsáno* na `/different-resource` , server *interně* načte a vrátí prostředek na `/different-resource` .</span><span class="sxs-lookup"><span data-stu-id="da414-424">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="da414-425">I když klient může být schopný načíst prostředek na adrese URL přepsané adresy, klient nebude informovat o tom, že prostředek existuje na přepsané adrese URL, když odešle svůj požadavek a obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="da414-425">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Koncový bod služby WebAPI se změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="da414-430">Ukázková aplikace pro přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="da414-430">URL rewriting sample app</span></span>

<span data-ttu-id="da414-431">Pomocí [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)můžete prozkoumat funkce middlewaru přepsaného adresou URL.</span><span class="sxs-lookup"><span data-stu-id="da414-431">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="da414-432">Aplikace použije pravidla přesměrování a přepisu a zobrazí přesměrovanou nebo přepsanou adresu URL pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="da414-432">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="da414-433">Kdy použít middleware přepisu adresy URL</span><span class="sxs-lookup"><span data-stu-id="da414-433">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="da414-434">Pokud nemůžete použít následující přístupy, použijte middleware přepisující adresu URL:</span><span class="sxs-lookup"><span data-stu-id="da414-434">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="da414-435">Modul pro přepsání adresy URL ve službě IIS v systému Windows Server</span><span class="sxs-lookup"><span data-stu-id="da414-435">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="da414-436">Apache mod_rewrite modul na serveru Apache</span><span class="sxs-lookup"><span data-stu-id="da414-436">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="da414-437">Přepsání adresy URL na Nginx</span><span class="sxs-lookup"><span data-stu-id="da414-437">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="da414-438">Používejte také middleware při hostování aplikace na [serveruHTTP.sys](xref:fundamentals/servers/httpsys) (dříve nazývaném weblisten).</span><span class="sxs-lookup"><span data-stu-id="da414-438">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="da414-439">Hlavními důvody pro použití technologie přepisování adres URL založené na serveru ve službě IIS, Apache a Nginx jsou:</span><span class="sxs-lookup"><span data-stu-id="da414-439">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="da414-440">Middleware nepodporuje všechny funkce těchto modulů.</span><span class="sxs-lookup"><span data-stu-id="da414-440">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="da414-441">Některé funkce v modulech serveru nefungují s ASP.NET Core projekty, jako jsou `IsFile` `IsDirectory` omezení a modulu IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="da414-441">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="da414-442">V těchto scénářích místo toho použijte middleware.</span><span class="sxs-lookup"><span data-stu-id="da414-442">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="da414-443">Výkon middleware se pravděpodobně neshoduje s modulem.</span><span class="sxs-lookup"><span data-stu-id="da414-443">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="da414-444">Srovnávací testy jsou jediným způsobem, jak zjistit, který přístup snižuje výkon, který je v nejvyšší nebo v případě, že je snížený výkon.</span><span class="sxs-lookup"><span data-stu-id="da414-444">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="da414-445">Balíček</span><span class="sxs-lookup"><span data-stu-id="da414-445">Package</span></span>

<span data-ttu-id="da414-446">Chcete-li do projektu zahrnout middleware, přidejte odkaz na balíček do souboru [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) v souboru projektu, který obsahuje balíček [Microsoft. AspNetCore. Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) .</span><span class="sxs-lookup"><span data-stu-id="da414-446">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="da414-447">Pokud nepoužíváte `Microsoft.AspNetCore.App` Metapackage, přidejte do balíčku odkaz na projekt `Microsoft.AspNetCore.Rewrite` .</span><span class="sxs-lookup"><span data-stu-id="da414-447">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="da414-448">Rozšíření a možnosti</span><span class="sxs-lookup"><span data-stu-id="da414-448">Extension and options</span></span>

<span data-ttu-id="da414-449">Vytvořte pravidla přepsání a přesměrování adresy URL vytvořením instance třídy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) s metodami rozšíření pro každé pravidlo přepsání.</span><span class="sxs-lookup"><span data-stu-id="da414-449">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="da414-450">Řetězení více pravidel v pořadí, ve kterém se mají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="da414-450">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="da414-451">Rozhraní se `RewriteOptions` předává do middleware přepisování adresy URL při jeho přidání do kanálu požadavků pomocí <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :</span><span class="sxs-lookup"><span data-stu-id="da414-451">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="da414-452">Přesměrovat neexistující na webovou</span><span class="sxs-lookup"><span data-stu-id="da414-452">Redirect non-www to www</span></span>

<span data-ttu-id="da414-453">Tři možnosti umožňují, aby aplikace přesměrovala `www` požadavky bez požadavků na `www` :</span><span class="sxs-lookup"><span data-stu-id="da414-453">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="da414-454"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Trvale přesměrujte požadavek na `www` subdoménu, pokud je požadavek jiný než `www` .</span><span class="sxs-lookup"><span data-stu-id="da414-454"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="da414-455">Přesměruje stavový kód [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) .</span><span class="sxs-lookup"><span data-stu-id="da414-455">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="da414-456"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Přesměrujte požadavek na `www` subdoménu, pokud je příchozí požadavek jiný než `www` .</span><span class="sxs-lookup"><span data-stu-id="da414-456"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="da414-457">Přesměruje stavový kód [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) .</span><span class="sxs-lookup"><span data-stu-id="da414-457">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="da414-458">Přetížení umožňuje poskytnout stavový kód pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="da414-458">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="da414-459">Použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy pro přiřazení stavového kódu.</span><span class="sxs-lookup"><span data-stu-id="da414-459">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="da414-460">Přesměrování adresy URL</span><span class="sxs-lookup"><span data-stu-id="da414-460">URL redirect</span></span>

<span data-ttu-id="da414-461">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> k přesměrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="da414-461">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="da414-462">První parametr obsahuje váš regulární výraz pro porovnání s cestou příchozí adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da414-462">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="da414-463">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="da414-463">The second parameter is the replacement string.</span></span> <span data-ttu-id="da414-464">Třetí parametr, pokud je k dispozici, určuje stavový kód.</span><span class="sxs-lookup"><span data-stu-id="da414-464">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="da414-465">Pokud nezadáte stavový kód, stavový kód se standardně *302 – Nalezeno*, což znamená, že prostředek je dočasně přesunut nebo nahrazen.</span><span class="sxs-lookup"><span data-stu-id="da414-465">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="da414-466">V prohlížeči s povolenými vývojářskými nástroji vytvořte žádost o ukázkovou aplikaci s cestou `/redirect-rule/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="da414-466">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="da414-467">Regulární výraz odpovídá cestě požadavku na `redirect-rule/(.*)` a cesta je nahrazena řetězcem `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="da414-467">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="da414-468">Adresa URL pro přesměrování se pošle zpátky do klienta s kódem stavu 302, který se *našel* .</span><span class="sxs-lookup"><span data-stu-id="da414-468">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="da414-469">Prohlížeč vytvoří novou žádost na adrese URL pro přesměrování, která se zobrazí v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="da414-469">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="da414-470">Vzhledem k tomu, že se žádná pravidla v ukázkové aplikaci neshodují s adresou URL pro přesměrování:</span><span class="sxs-lookup"><span data-stu-id="da414-470">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="da414-471">Druhá žádost obdrží odpověď *200-OK* od aplikace.</span><span class="sxs-lookup"><span data-stu-id="da414-471">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="da414-472">Tělo odpovědi zobrazuje adresu URL pro přesměrování.</span><span class="sxs-lookup"><span data-stu-id="da414-472">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="da414-473">Při *přesměrování*adresy URL se na Server provede cyklická výměna.</span><span class="sxs-lookup"><span data-stu-id="da414-473">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="da414-474">Buďte opatrní při vytváření pravidel přesměrování.</span><span class="sxs-lookup"><span data-stu-id="da414-474">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="da414-475">Pravidla přesměrování se vyhodnocují při každém požadavku na aplikaci, včetně po přesměrování.</span><span class="sxs-lookup"><span data-stu-id="da414-475">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="da414-476">Je snadné vytvořit *smyčku nekonečných přesměrování*.</span><span class="sxs-lookup"><span data-stu-id="da414-476">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="da414-477">Původní požadavek:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="da414-477">Original Request: `/redirect-rule/1234/5678`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="da414-479">Část výrazu obsažená v závorkách se nazývá *Skupina zachycení*.</span><span class="sxs-lookup"><span data-stu-id="da414-479">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="da414-480">Tečka ( `.` ) výrazu znamená, že *odpovídá libovolnému znaku*.</span><span class="sxs-lookup"><span data-stu-id="da414-480">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="da414-481">Hvězdička ( `*` ) značí, *že se předchozí znak rovná nule nebo vícekrát*.</span><span class="sxs-lookup"><span data-stu-id="da414-481">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="da414-482">Proto jsou poslední dva segmenty cesty adresy URL `1234/5678` zachyceny skupinou zachycení `(.*)` .</span><span class="sxs-lookup"><span data-stu-id="da414-482">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="da414-483">Jakákoli hodnota, kterou zadáte v adrese URL požadavku, potom, co `redirect-rule/` je tato jediná skupina zachycení zachycena.</span><span class="sxs-lookup"><span data-stu-id="da414-483">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="da414-484">V řetězci pro nahrazení jsou zachycené skupiny vloženy do řetězce s znakem dolaru ( `$` ) následovaným pořadovým číslem záznamu.</span><span class="sxs-lookup"><span data-stu-id="da414-484">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="da414-485">První hodnota skupiny zachycení je získána s `$1` , druhým s `$2` a pokračuje v pořadí pro skupiny zachycení ve vašem regulárním výrazu.</span><span class="sxs-lookup"><span data-stu-id="da414-485">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="da414-486">Regulární výraz pravidla přesměrování v ukázkové aplikaci obsahuje jenom jednu zachycenou skupinu, takže v náhradním řetězci je jenom jedna vložená skupina, která je `$1` .</span><span class="sxs-lookup"><span data-stu-id="da414-486">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="da414-487">Po použití pravidla se adresa URL bude nacházet `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="da414-487">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="da414-488">Přesměrování adresy URL na zabezpečený koncový bod</span><span class="sxs-lookup"><span data-stu-id="da414-488">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="da414-489">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> k přesměrování požadavků HTTP na stejného hostitele a cestu pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="da414-489">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="da414-490">Pokud kód stavu není zadaný, použije se ve výchozím nastavení middleware *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="da414-490">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="da414-491">Pokud port není dodán:</span><span class="sxs-lookup"><span data-stu-id="da414-491">If the port isn't supplied:</span></span>

* <span data-ttu-id="da414-492">Ve výchozím nastavení se používá middleware `null` .</span><span class="sxs-lookup"><span data-stu-id="da414-492">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="da414-493">Schéma se změní na `https` (protokol HTTPS) a klient přistupuje k prostředku na portu 443.</span><span class="sxs-lookup"><span data-stu-id="da414-493">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="da414-494">Následující příklad ukazuje, jak nastavit stavový kód na *301 – trvale přesunuto* a změnit port na 5001.</span><span class="sxs-lookup"><span data-stu-id="da414-494">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="da414-495">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> k přesměrování nezabezpečených požadavků na stejného hostitele a cestu s protokolem Secure https na portu 443.</span><span class="sxs-lookup"><span data-stu-id="da414-495">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="da414-496">Middleware nastaví stavový kód na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="da414-496">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="da414-497">Při přesměrování na zabezpečený koncový bod bez požadavku na další pravidla přesměrování doporučujeme použít middleware pro přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="da414-497">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="da414-498">Další informace najdete v tématu věnovaném [vykonání protokolu HTTPS](xref:security/enforcing-ssl#require-https) .</span><span class="sxs-lookup"><span data-stu-id="da414-498">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="da414-499">Ukázková aplikace dokáže demonstrovat, jak používat `AddRedirectToHttps` nebo `AddRedirectToHttpsPermanent` .</span><span class="sxs-lookup"><span data-stu-id="da414-499">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="da414-500">Přidejte metodu rozšíření do `RewriteOptions` .</span><span class="sxs-lookup"><span data-stu-id="da414-500">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="da414-501">Vytvořte nezabezpečenou žádost o aplikaci na libovolné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="da414-501">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="da414-502">Zrušte upozornění zabezpečení prohlížeče, že certifikát podepsaný svým držitelem je nedůvěryhodný, nebo vytvořte výjimku pro důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="da414-502">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="da414-503">Původní požadavek s použitím `AddRedirectToHttps(301, 5001)` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="da414-503">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="da414-505">Původní požadavek s použitím `AddRedirectToHttpsPermanent` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="da414-505">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="da414-507">Přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="da414-507">URL rewrite</span></span>

<span data-ttu-id="da414-508">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> k vytvoření pravidla pro přepis adres URL.</span><span class="sxs-lookup"><span data-stu-id="da414-508">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="da414-509">První parametr obsahuje regulární výraz pro porovnání na cestě příchozích adres URL.</span><span class="sxs-lookup"><span data-stu-id="da414-509">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="da414-510">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="da414-510">The second parameter is the replacement string.</span></span> <span data-ttu-id="da414-511">Třetí parametr, `skipRemainingRules: {true|false}` označuje middleware bez ohledu na to, zda má přeskočit další pravidla přepsání, pokud je použito aktuální pravidlo.</span><span class="sxs-lookup"><span data-stu-id="da414-511">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="da414-512">Původní požadavek:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="da414-512">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="da414-514">Kosočtverce ( `^` ) na začátku výrazu znamená, že shoda začíná na začátku cesty URL.</span><span class="sxs-lookup"><span data-stu-id="da414-514">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="da414-515">V předchozím příkladu s pravidlem přesměrování `redirect-rule/(.*)` není `^` na začátku regulárního výrazu k dispozici žádná kosočtverce ().</span><span class="sxs-lookup"><span data-stu-id="da414-515">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="da414-516">Proto jakékoli znaky mohou předcházet `redirect-rule/` v cestě k úspěšné shodě.</span><span class="sxs-lookup"><span data-stu-id="da414-516">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="da414-517">Cesta</span><span class="sxs-lookup"><span data-stu-id="da414-517">Path</span></span>                               | <span data-ttu-id="da414-518">Shoda</span><span class="sxs-lookup"><span data-stu-id="da414-518">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="da414-519">Ano</span><span class="sxs-lookup"><span data-stu-id="da414-519">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="da414-520">Ano</span><span class="sxs-lookup"><span data-stu-id="da414-520">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="da414-521">Ano</span><span class="sxs-lookup"><span data-stu-id="da414-521">Yes</span></span>   |

<span data-ttu-id="da414-522">Pravidlo přepsaného zápisu `^rewrite-rule/(\d+)/(\d+)` odpovídá pouze cestám, pokud začínají na `rewrite-rule/` .</span><span class="sxs-lookup"><span data-stu-id="da414-522">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="da414-523">V následující tabulce si všimněte rozdílu v porovnání.</span><span class="sxs-lookup"><span data-stu-id="da414-523">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="da414-524">Cesta</span><span class="sxs-lookup"><span data-stu-id="da414-524">Path</span></span>                              | <span data-ttu-id="da414-525">Shoda</span><span class="sxs-lookup"><span data-stu-id="da414-525">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="da414-526">Yes</span><span class="sxs-lookup"><span data-stu-id="da414-526">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="da414-527">Ne</span><span class="sxs-lookup"><span data-stu-id="da414-527">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="da414-528">Ne</span><span class="sxs-lookup"><span data-stu-id="da414-528">No</span></span>    |

<span data-ttu-id="da414-529">Po `^rewrite-rule/` části výrazu jsou k dispozici dvě skupiny zachycení, `(\d+)/(\d+)` .</span><span class="sxs-lookup"><span data-stu-id="da414-529">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="da414-530">`\d`Značí *shodu s číslicí (číslem)*.</span><span class="sxs-lookup"><span data-stu-id="da414-530">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="da414-531">Znaménko plus ( `+` ) znamená, že se *shoduje s jedním nebo více předcházejícím znakem*.</span><span class="sxs-lookup"><span data-stu-id="da414-531">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="da414-532">Proto musí adresa URL obsahovat číslo následované lomítkem, za kterým následuje jiné číslo.</span><span class="sxs-lookup"><span data-stu-id="da414-532">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="da414-533">Tyto skupiny zachycení jsou vloženy do přepsané adresy URL jako `$1` a `$2` .</span><span class="sxs-lookup"><span data-stu-id="da414-533">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="da414-534">Náhradní řetězec pravidla přepsaní umístí zachycené skupiny do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="da414-534">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="da414-535">Požadovaná cesta k `/rewrite-rule/1234/5678` je přepsána za účelem získání prostředku v `/rewritten?var1=1234&var2=5678` .</span><span class="sxs-lookup"><span data-stu-id="da414-535">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="da414-536">Pokud se v původním požadavku nachází řetězec dotazu, bude při přepisování adresy URL zachován.</span><span class="sxs-lookup"><span data-stu-id="da414-536">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="da414-537">K získání prostředku neexistuje žádná Přenosová cesta k serveru.</span><span class="sxs-lookup"><span data-stu-id="da414-537">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="da414-538">Pokud prostředek existuje, načte se a vrátí do klienta s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="da414-538">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="da414-539">Vzhledem k tomu, že klient není přesměrován, adresa URL v adresním řádku prohlížeče se nezmění.</span><span class="sxs-lookup"><span data-stu-id="da414-539">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="da414-540">Klienti nerozpoznají, že na serveru došlo k operaci přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="da414-540">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="da414-541">`skipRemainingRules: true`Kdykoli je to možné, protože vyhovující pravidla jsou výpočetně náročná a zvyšují dobu odezvy aplikace.</span><span class="sxs-lookup"><span data-stu-id="da414-541">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="da414-542">Pro nejrychlejší aplikační odpověď:</span><span class="sxs-lookup"><span data-stu-id="da414-542">For the fastest app response:</span></span>
>
> * <span data-ttu-id="da414-543">Seřazení pravidel přepisu z nejčastěji spárovaného pravidla na nejméně často spárované pravidlo.</span><span class="sxs-lookup"><span data-stu-id="da414-543">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="da414-544">Pokud dojde ke shodě a není vyžadováno žádné další zpracování pravidla, přeskočte zpracování zbývajících pravidel.</span><span class="sxs-lookup"><span data-stu-id="da414-544">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="da414-545">Mod_rewrite Apache</span><span class="sxs-lookup"><span data-stu-id="da414-545">Apache mod_rewrite</span></span>

<span data-ttu-id="da414-546">Použijte pravidla mod_rewrite Apache s <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="da414-546">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="da414-547">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="da414-547">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="da414-548">Další informace a příklady pravidel mod_rewrite najdete v tématu [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="da414-548">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="da414-549"><xref:System.IO.StreamReader>Používá se ke čtení pravidel ze souboru pravidel *ApacheModRewrite.txt* :</span><span class="sxs-lookup"><span data-stu-id="da414-549">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="da414-550">Ukázková aplikace přesměrovává požadavky od `/apache-mod-rules-redirect/(.\*)` do `/redirected?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="da414-550">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="da414-551">Stavový kód odpovědi je *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="da414-551">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="da414-552">Původní požadavek:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="da414-552">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="da414-554">Middleware podporuje následující proměnné serveru Apache mod_rewrite:</span><span class="sxs-lookup"><span data-stu-id="da414-554">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="da414-555">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="da414-555">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="da414-556">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="da414-556">HTTP_ACCEPT</span></span>
* <span data-ttu-id="da414-557">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="da414-557">HTTP_CONNECTION</span></span>
* <span data-ttu-id="da414-558">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="da414-558">HTTP_COOKIE</span></span>
* <span data-ttu-id="da414-559">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="da414-559">HTTP_FORWARDED</span></span>
* <span data-ttu-id="da414-560">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="da414-560">HTTP_HOST</span></span>
* <span data-ttu-id="da414-561">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="da414-561">HTTP_REFERER</span></span>
* <span data-ttu-id="da414-562">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="da414-562">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="da414-563">HTTPS</span><span class="sxs-lookup"><span data-stu-id="da414-563">HTTPS</span></span>
* <span data-ttu-id="da414-564">PROTOKOLŮ</span><span class="sxs-lookup"><span data-stu-id="da414-564">IPV6</span></span>
* <span data-ttu-id="da414-565">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="da414-565">QUERY_STRING</span></span>
* <span data-ttu-id="da414-566">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="da414-566">REMOTE_ADDR</span></span>
* <span data-ttu-id="da414-567">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="da414-567">REMOTE_PORT</span></span>
* <span data-ttu-id="da414-568">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="da414-568">REQUEST_FILENAME</span></span>
* <span data-ttu-id="da414-569">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="da414-569">REQUEST_METHOD</span></span>
* <span data-ttu-id="da414-570">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="da414-570">REQUEST_SCHEME</span></span>
* <span data-ttu-id="da414-571">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="da414-571">REQUEST_URI</span></span>
* <span data-ttu-id="da414-572">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="da414-572">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="da414-573">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="da414-573">SERVER_ADDR</span></span>
* <span data-ttu-id="da414-574">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="da414-574">SERVER_PORT</span></span>
* <span data-ttu-id="da414-575">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="da414-575">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="da414-576">ČAS</span><span class="sxs-lookup"><span data-stu-id="da414-576">TIME</span></span>
* <span data-ttu-id="da414-577">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="da414-577">TIME_DAY</span></span>
* <span data-ttu-id="da414-578">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="da414-578">TIME_HOUR</span></span>
* <span data-ttu-id="da414-579">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="da414-579">TIME_MIN</span></span>
* <span data-ttu-id="da414-580">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="da414-580">TIME_MON</span></span>
* <span data-ttu-id="da414-581">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="da414-581">TIME_SEC</span></span>
* <span data-ttu-id="da414-582">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="da414-582">TIME_WDAY</span></span>
* <span data-ttu-id="da414-583">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="da414-583">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="da414-584">Pravidla pro přepsání adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="da414-584">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="da414-585">Pokud chcete použít stejnou sadu pravidel, která se vztahuje na modul IIS URL Rewrite, použijte <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="da414-585">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="da414-586">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="da414-586">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="da414-587">Nesměrujte middleware na použití *web.config* souboru aplikace při spuštění na Windows serveru IIS.</span><span class="sxs-lookup"><span data-stu-id="da414-587">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="da414-588">V případě služby IIS by tato pravidla měla být uložena mimo soubor *web.config* aplikace, aby nedocházelo ke konfliktům s modulem PŘEPISU služby IIS.</span><span class="sxs-lookup"><span data-stu-id="da414-588">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="da414-589">Další informace a příklady pravidel pro přepis adres URL služby IIS najdete v tématu použití odkazu na modul pro [přepis adres url 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [odkaz na konfiguraci modulu přepisu adresy](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)URL.</span><span class="sxs-lookup"><span data-stu-id="da414-589">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="da414-590"><xref:System.IO.StreamReader>Používá se ke čtení pravidel ze souboru pravidel *IISUrlRewrite.xml* :</span><span class="sxs-lookup"><span data-stu-id="da414-590">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="da414-591">Ukázková aplikace přepíše požadavky od `/iis-rules-rewrite/(.*)` do `/rewritten?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="da414-591">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="da414-592">Odpověď se pošle klientovi s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="da414-592">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="da414-593">Původní požadavek:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="da414-593">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="da414-595">Pokud máte aktivní modul pro přepis služby IIS s nakonfigurovanými pravidly na úrovni serveru, který by ovlivnil vaši aplikaci nežádoucím způsobem, můžete pro aplikaci zakázat modul IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="da414-595">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="da414-596">Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="da414-596">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="da414-597">Nepodporované funkce</span><span class="sxs-lookup"><span data-stu-id="da414-597">Unsupported features</span></span>

<span data-ttu-id="da414-598">Middleware vydaná ASP.NET Core 2. x nepodporuje následující funkce modulu pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="da414-598">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="da414-599">Pravidla pro odchozí provoz</span><span class="sxs-lookup"><span data-stu-id="da414-599">Outbound Rules</span></span>
* <span data-ttu-id="da414-600">Vlastní proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="da414-600">Custom Server Variables</span></span>
* <span data-ttu-id="da414-601">Zástupné znaky</span><span class="sxs-lookup"><span data-stu-id="da414-601">Wildcards</span></span>
* <span data-ttu-id="da414-602">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="da414-602">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="da414-603">Podporované proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="da414-603">Supported server variables</span></span>

<span data-ttu-id="da414-604">Middleware podporuje následující proměnné serveru pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="da414-604">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="da414-605">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="da414-605">CONTENT_LENGTH</span></span>
* <span data-ttu-id="da414-606">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="da414-606">CONTENT_TYPE</span></span>
* <span data-ttu-id="da414-607">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="da414-607">HTTP_ACCEPT</span></span>
* <span data-ttu-id="da414-608">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="da414-608">HTTP_CONNECTION</span></span>
* <span data-ttu-id="da414-609">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="da414-609">HTTP_COOKIE</span></span>
* <span data-ttu-id="da414-610">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="da414-610">HTTP_HOST</span></span>
* <span data-ttu-id="da414-611">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="da414-611">HTTP_REFERER</span></span>
* <span data-ttu-id="da414-612">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="da414-612">HTTP_URL</span></span>
* <span data-ttu-id="da414-613">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="da414-613">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="da414-614">HTTPS</span><span class="sxs-lookup"><span data-stu-id="da414-614">HTTPS</span></span>
* <span data-ttu-id="da414-615">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="da414-615">LOCAL_ADDR</span></span>
* <span data-ttu-id="da414-616">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="da414-616">QUERY_STRING</span></span>
* <span data-ttu-id="da414-617">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="da414-617">REMOTE_ADDR</span></span>
* <span data-ttu-id="da414-618">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="da414-618">REMOTE_PORT</span></span>
* <span data-ttu-id="da414-619">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="da414-619">REQUEST_FILENAME</span></span>
* <span data-ttu-id="da414-620">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="da414-620">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="da414-621">Můžete také získat <xref:Microsoft.Extensions.FileProviders.IFileProvider> prostřednictvím <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="da414-621">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="da414-622">Tento přístup může poskytovat větší flexibilitu pro umístění souborů pravidel přepisu.</span><span class="sxs-lookup"><span data-stu-id="da414-622">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="da414-623">Ujistěte se, že jsou v cestě, kterou zadáte, nasazené soubory pravidel přepisu na server.</span><span class="sxs-lookup"><span data-stu-id="da414-623">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="da414-624">Pravidlo založené na metodách</span><span class="sxs-lookup"><span data-stu-id="da414-624">Method-based rule</span></span>

<span data-ttu-id="da414-625">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k implementaci logiky vlastního pravidla v metodě.</span><span class="sxs-lookup"><span data-stu-id="da414-625">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="da414-626">`Add`zpřístupňuje <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> , který zpřístupňuje <xref:Microsoft.AspNetCore.Http.HttpContext> metodu pro použití v metodě.</span><span class="sxs-lookup"><span data-stu-id="da414-626">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="da414-627">[RewriteContext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) určuje, jak se zpracovává další zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="da414-627">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="da414-628">Nastavte hodnotu na jedno z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> polí popsaných v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="da414-628">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="da414-629">Akce</span><span class="sxs-lookup"><span data-stu-id="da414-629">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="da414-630">`RuleResult.ContinueRules`výchozí</span><span class="sxs-lookup"><span data-stu-id="da414-630">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="da414-631">Pokračovat v používání pravidel.</span><span class="sxs-lookup"><span data-stu-id="da414-631">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="da414-632">Zastavení použití pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="da414-632">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="da414-633">Zastavení použití pravidel a odeslání kontextu do dalšího middlewaru.</span><span class="sxs-lookup"><span data-stu-id="da414-633">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="da414-634">Ukázková aplikace ukazuje metodu, která přesměrovává požadavky na cesty, které končí na *. XML*.</span><span class="sxs-lookup"><span data-stu-id="da414-634">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="da414-635">Pokud je žádost určena pro `/file.xml` , je požadavek přesměrován na `/xmlfiles/file.xml` .</span><span class="sxs-lookup"><span data-stu-id="da414-635">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="da414-636">Stavový kód je nastaven na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="da414-636">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="da414-637">Když prohlížeč vytvoří nový požadavek na */xmlfiles/file.xml*, soubor middleware statických souborů obsluhuje soubor do klienta ze složky *wwwroot/xmlfiles* .</span><span class="sxs-lookup"><span data-stu-id="da414-637">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="da414-638">Pro přesměrování explicitně nastavte stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="da414-638">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="da414-639">V opačném případě se vrátí stavový kód *200-OK* a na klientovi se neobjeví přesměrování.</span><span class="sxs-lookup"><span data-stu-id="da414-639">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="da414-640">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="da414-640">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="da414-641">Tento přístup může také přezapisovat požadavky.</span><span class="sxs-lookup"><span data-stu-id="da414-641">This approach can also rewrite requests.</span></span> <span data-ttu-id="da414-642">Ukázková aplikace ukazuje přepis cesty k libovolné žádosti o textový soubor, aby sloužila *file.txt* textový soubor ze složky *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="da414-642">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="da414-643">Middleware statických souborů zachovává soubor na základě aktualizované cesty požadavku:</span><span class="sxs-lookup"><span data-stu-id="da414-643">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="da414-644">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="da414-644">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="da414-645">Pravidlo založené na IRule</span><span class="sxs-lookup"><span data-stu-id="da414-645">IRule-based rule</span></span>

<span data-ttu-id="da414-646">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k použití logiky pravidla ve třídě, která implementuje <xref:Microsoft.AspNetCore.Rewrite.IRule> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="da414-646">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="da414-647">`IRule`poskytuje větší flexibilitu při použití pravidla založeného na metodách.</span><span class="sxs-lookup"><span data-stu-id="da414-647">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="da414-648">Vaše třída implementace může obsahovat konstruktor, který umožňuje předat parametry pro <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> metodu.</span><span class="sxs-lookup"><span data-stu-id="da414-648">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="da414-649">Hodnoty parametrů v ukázkové aplikaci pro `extension` a `newPath` jsou zkontrolovány, aby splňovaly několik podmínek.</span><span class="sxs-lookup"><span data-stu-id="da414-649">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="da414-650">`extension`Musí obsahovat hodnotu a hodnota musí být *. png*, *. jpg*nebo *. gif*.</span><span class="sxs-lookup"><span data-stu-id="da414-650">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="da414-651">Pokud `newPath` není platná, <xref:System.ArgumentException> je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="da414-651">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="da414-652">Pokud se pro *image.png*požadavek provede, požadavek se přesměruje na `/png-images/image.png` .</span><span class="sxs-lookup"><span data-stu-id="da414-652">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="da414-653">Pokud se pro *image.jpg*požadavek provede, požadavek se přesměruje na `/jpg-images/image.jpg` .</span><span class="sxs-lookup"><span data-stu-id="da414-653">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="da414-654">Stavový kód je nastaven na *301 – trvale přesunuto*a `context.Result` je nastavené na zastavení zpracování pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="da414-654">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="da414-655">Původní požadavek:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="da414-655">Original Request: `/image.png`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="da414-657">Původní požadavek:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="da414-657">Original Request: `/image.jpg`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="da414-659">Příklady Regex</span><span class="sxs-lookup"><span data-stu-id="da414-659">Regex examples</span></span>

| <span data-ttu-id="da414-660">Cíl</span><span class="sxs-lookup"><span data-stu-id="da414-660">Goal</span></span> | <span data-ttu-id="da414-661">& řetězce regulárního výrazu</span><span class="sxs-lookup"><span data-stu-id="da414-661">Regex String &</span></span><br><span data-ttu-id="da414-662">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="da414-662">Match Example</span></span> | <span data-ttu-id="da414-663">& řetězce nahrazení</span><span class="sxs-lookup"><span data-stu-id="da414-663">Replacement String &</span></span><br><span data-ttu-id="da414-664">Příklad výstupu</span><span class="sxs-lookup"><span data-stu-id="da414-664">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="da414-665">Přepište cestu do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="da414-665">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="da414-666">Koncové lomítko pruhu</span><span class="sxs-lookup"><span data-stu-id="da414-666">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="da414-667">Vymáhat koncové lomítko</span><span class="sxs-lookup"><span data-stu-id="da414-667">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="da414-668">Vyhnout se přepisu konkrétních požadavků</span><span class="sxs-lookup"><span data-stu-id="da414-668">Avoid rewriting specific requests</span></span> | <span data-ttu-id="da414-669">`^(.*)(?<!\.axd)$` nebo `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="da414-669">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="da414-670">Ano:`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="da414-670">Yes: `/resource.htm`</span></span><br><span data-ttu-id="da414-671">Ne:`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="da414-671">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="da414-672">Změna uspořádání segmentů adresy URL</span><span class="sxs-lookup"><span data-stu-id="da414-672">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="da414-673">Nahradit segment adresy URL</span><span class="sxs-lookup"><span data-stu-id="da414-673">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="da414-674">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="da414-674">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="da414-675">Regulární výrazy v rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="da414-675">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="da414-676">Jazyk regulárních výrazů – stručná referenční dokumentace</span><span class="sxs-lookup"><span data-stu-id="da414-676">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="da414-677">Mod_rewrite Apache</span><span class="sxs-lookup"><span data-stu-id="da414-677">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="da414-678">Použití modulu URL pro přepis 2,0 (pro IIS)</span><span class="sxs-lookup"><span data-stu-id="da414-678">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="da414-679">Odkaz na konfiguraci modulu pro přepis adresy URL</span><span class="sxs-lookup"><span data-stu-id="da414-679">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="da414-680">Fórum modulu pro přepsání adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="da414-680">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="da414-681">Zachování jednoduché struktury URL</span><span class="sxs-lookup"><span data-stu-id="da414-681">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="da414-682">10 tipů a triky pro přepis adres URL</span><span class="sxs-lookup"><span data-stu-id="da414-682">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="da414-683">Na lomítko nebo ne na lomítko</span><span class="sxs-lookup"><span data-stu-id="da414-683">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
