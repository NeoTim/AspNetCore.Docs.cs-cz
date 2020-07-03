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
ms.openlocfilehash: dbdb7cd86218fd9ba63ae4ac2aa516836d4fd1a1
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944292"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="04b2e-103">Middleware pro přepis adres URL v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="04b2e-103">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="04b2e-104">Od [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="04b2e-104">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="04b2e-105">Tento dokument zavádí přepis adres URL s pokyny k použití middlewaru přepisu adresy URL v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="04b2e-105">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="04b2e-106">Přepsání adresy URL je to, že se jedná o úpravu adres URL žádostí na základě jednoho nebo více předdefinovaných pravidel.</span><span class="sxs-lookup"><span data-stu-id="04b2e-106">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="04b2e-107">Přepsání adresy URL vytvoří abstrakci mezi umístěními prostředků a jejich adresami tak, aby umístění a adresy nebyly pevně propojené.</span><span class="sxs-lookup"><span data-stu-id="04b2e-107">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="04b2e-108">Přepsání adresy URL je v několika scénářích užitečné pro:</span><span class="sxs-lookup"><span data-stu-id="04b2e-108">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="04b2e-109">Dočasné nebo trvalé přesunutí nebo nahrazení prostředků serveru a udržování stabilních lokátorů pro tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="04b2e-109">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="04b2e-110">Rozdělte zpracování požadavků napříč různými aplikacemi nebo napříč oblastmi jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="04b2e-110">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="04b2e-111">Umožňuje odebrat, přidat nebo změnit uspořádání segmentů adresy URL příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="04b2e-111">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="04b2e-112">Optimalizujte veřejné adresy URL pro optimalizaci vyhledávače (SEO).</span><span class="sxs-lookup"><span data-stu-id="04b2e-112">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="04b2e-113">Povolí použití přívětivých veřejných adres URL, které návštěvníkům pomůžou odhadnout obsah vrácený požadavkem na prostředek.</span><span class="sxs-lookup"><span data-stu-id="04b2e-113">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="04b2e-114">Přesměrovat nezabezpečené požadavky na zabezpečené koncové body.</span><span class="sxs-lookup"><span data-stu-id="04b2e-114">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="04b2e-115">Zabránit hotlinking, kde externí lokalita používá hostovaný statický prostředek na jiné lokalitě propojením prostředku s vlastním obsahem.</span><span class="sxs-lookup"><span data-stu-id="04b2e-115">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="04b2e-116">Přepsání adresy URL může snížit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="04b2e-116">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="04b2e-117">Pokud je to možné, omezte počet a složitost pravidel.</span><span class="sxs-lookup"><span data-stu-id="04b2e-117">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="04b2e-118">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="04b2e-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="04b2e-119">Přesměrování adresy URL a přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-119">URL redirect and URL rewrite</span></span>

<span data-ttu-id="04b2e-120">Rozdíl mezi přesměrováním *URL* a *přepsáním adresy URL* je malý, ale má důležitý dopad na poskytování prostředků klientům.</span><span class="sxs-lookup"><span data-stu-id="04b2e-120">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="04b2e-121">Rozhraní middleware pro přepis adres URL ASP.NET Core je schopné splnit nutnost obou.</span><span class="sxs-lookup"><span data-stu-id="04b2e-121">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="04b2e-122">*Přesměrování adresy URL* zahrnuje i operaci na straně klienta, kde klient má pokyn k přístupu k prostředku na jiné adrese, než jakou požadoval klient původně.</span><span class="sxs-lookup"><span data-stu-id="04b2e-122">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="04b2e-123">To vyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="04b2e-123">This requires a round trip to the server.</span></span> <span data-ttu-id="04b2e-124">Adresa URL pro přesměrování vracená klientovi se zobrazí v adresním řádku prohlížeče, když klient vytvoří nový požadavek na prostředek.</span><span class="sxs-lookup"><span data-stu-id="04b2e-124">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="04b2e-125">Pokud `/resource` je *přesměrován* na `/different-resource` , server odpoví, že by měl klient získat prostředek na `/different-resource` stavovém kódu, což značí, že přesměrování je buď dočasné, nebo trvalé.</span><span class="sxs-lookup"><span data-stu-id="04b2e-125">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Koncový bod služby WebAPI se dočasně změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="04b2e-131">Při přesměrování požadavků na jinou adresu URL určete, zda je přesměrování trvalé nebo dočasné, zadáním stavového kódu s odpovědí:</span><span class="sxs-lookup"><span data-stu-id="04b2e-131">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="04b2e-132">V případě, že má prostředek novou, trvalou adresu URL a chcete dát klientovi pokyn, aby všechny budoucí požadavky na prostředek používaly novou adresu URL, se použije kód *trvale přesunutý stav 301* .</span><span class="sxs-lookup"><span data-stu-id="04b2e-132">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="04b2e-133">*Klient může ukládat odpověď do mezipaměti a znovu ji použít, když se přijme stavový kód 301.*</span><span class="sxs-lookup"><span data-stu-id="04b2e-133">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="04b2e-134">V případě, že je přesměrování dočasné nebo obecně se může změnit, je použit stavový kód *302* .</span><span class="sxs-lookup"><span data-stu-id="04b2e-134">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="04b2e-135">Stavový kód 302 indikuje klientovi, aby neukládal adresu URL a použil ho v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-135">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="04b2e-136">Další informace o stavových kódech najdete v [dokumentu RFC 2616: definice stavového kódu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="04b2e-136">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="04b2e-137">*Přepsání adresy URL* je operace na straně serveru, která poskytuje prostředek z jiné adresy prostředku, než jakou požaduje klient.</span><span class="sxs-lookup"><span data-stu-id="04b2e-137">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="04b2e-138">Přepsání adresy URL nevyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="04b2e-138">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="04b2e-139">Přepsaná adresa URL se nevrátí klientovi a nezobrazí se v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="04b2e-139">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="04b2e-140">Pokud `/resource` je *přepsáno* na `/different-resource` , server *interně* načte a vrátí prostředek na `/different-resource` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-140">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="04b2e-141">I když klient může být schopný načíst prostředek na adrese URL přepsané adresy, klient nebude informovat o tom, že prostředek existuje na přepsané adrese URL, když odešle svůj požadavek a obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="04b2e-141">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Koncový bod služby WebAPI se změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="04b2e-146">Ukázková aplikace pro přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-146">URL rewriting sample app</span></span>

<span data-ttu-id="04b2e-147">Pomocí [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)můžete prozkoumat funkce middlewaru přepsaného adresou URL.</span><span class="sxs-lookup"><span data-stu-id="04b2e-147">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="04b2e-148">Aplikace použije pravidla přesměrování a přepisu a zobrazí přesměrovanou nebo přepsanou adresu URL pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="04b2e-148">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="04b2e-149">Kdy použít middleware přepisu adresy URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-149">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="04b2e-150">Pokud nemůžete použít následující přístupy, použijte middleware přepisující adresu URL:</span><span class="sxs-lookup"><span data-stu-id="04b2e-150">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="04b2e-151">Modul pro přepsání adresy URL ve službě IIS v systému Windows Server</span><span class="sxs-lookup"><span data-stu-id="04b2e-151">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="04b2e-152">Apache mod_rewrite modul na serveru Apache</span><span class="sxs-lookup"><span data-stu-id="04b2e-152">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="04b2e-153">Přepsání adresy URL na Nginx</span><span class="sxs-lookup"><span data-stu-id="04b2e-153">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="04b2e-154">Používejte také middleware při hostování aplikace na [serveruHTTP.sys](xref:fundamentals/servers/httpsys) (dříve nazývaném weblisten).</span><span class="sxs-lookup"><span data-stu-id="04b2e-154">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="04b2e-155">Hlavními důvody pro použití technologie přepisování adres URL založené na serveru ve službě IIS, Apache a Nginx jsou:</span><span class="sxs-lookup"><span data-stu-id="04b2e-155">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="04b2e-156">Middleware nepodporuje všechny funkce těchto modulů.</span><span class="sxs-lookup"><span data-stu-id="04b2e-156">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="04b2e-157">Některé funkce v modulech serveru nefungují s ASP.NET Core projekty, jako jsou `IsFile` `IsDirectory` omezení a modulu IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="04b2e-157">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="04b2e-158">V těchto scénářích místo toho použijte middleware.</span><span class="sxs-lookup"><span data-stu-id="04b2e-158">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="04b2e-159">Výkon middleware se pravděpodobně neshoduje s modulem.</span><span class="sxs-lookup"><span data-stu-id="04b2e-159">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="04b2e-160">Srovnávací testy jsou jediným způsobem, jak zjistit, který přístup snižuje výkon, který je v nejvyšší nebo v případě, že je snížený výkon.</span><span class="sxs-lookup"><span data-stu-id="04b2e-160">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="04b2e-161">Balíček</span><span class="sxs-lookup"><span data-stu-id="04b2e-161">Package</span></span>

<span data-ttu-id="04b2e-162">Middleware pro přepis adres URL poskytuje balíček [Microsoft. AspNetCore. Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) , který je implicitně zahrnutý v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="04b2e-162">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="04b2e-163">Rozšíření a možnosti</span><span class="sxs-lookup"><span data-stu-id="04b2e-163">Extension and options</span></span>

<span data-ttu-id="04b2e-164">Vytvořte pravidla přepsání a přesměrování adresy URL vytvořením instance třídy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) s metodami rozšíření pro každé pravidlo přepsání.</span><span class="sxs-lookup"><span data-stu-id="04b2e-164">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="04b2e-165">Řetězení více pravidel v pořadí, ve kterém se mají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="04b2e-165">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="04b2e-166">Rozhraní se `RewriteOptions` předává do middleware přepisování adresy URL při jeho přidání do kanálu požadavků pomocí <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :</span><span class="sxs-lookup"><span data-stu-id="04b2e-166">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="04b2e-167">Přesměrovat neexistující na webovou</span><span class="sxs-lookup"><span data-stu-id="04b2e-167">Redirect non-www to www</span></span>

<span data-ttu-id="04b2e-168">Tři možnosti umožňují, aby aplikace přesměrovala `www` požadavky bez požadavků na `www` :</span><span class="sxs-lookup"><span data-stu-id="04b2e-168">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="04b2e-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Trvale přesměrujte požadavek na `www` subdoménu, pokud je požadavek jiný než `www` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="04b2e-170">Přesměruje stavový kód [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) .</span><span class="sxs-lookup"><span data-stu-id="04b2e-170">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="04b2e-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Přesměrujte požadavek na `www` subdoménu, pokud je příchozí požadavek jiný než `www` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="04b2e-172">Přesměruje stavový kód [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) .</span><span class="sxs-lookup"><span data-stu-id="04b2e-172">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="04b2e-173">Přetížení umožňuje poskytnout stavový kód pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="04b2e-173">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="04b2e-174">Použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy pro přiřazení stavového kódu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-174">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="04b2e-175">Přesměrování adresy URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-175">URL redirect</span></span>

<span data-ttu-id="04b2e-176">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> k přesměrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="04b2e-176">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="04b2e-177">První parametr obsahuje váš regulární výraz pro porovnání s cestou příchozí adresy URL.</span><span class="sxs-lookup"><span data-stu-id="04b2e-177">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="04b2e-178">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="04b2e-178">The second parameter is the replacement string.</span></span> <span data-ttu-id="04b2e-179">Třetí parametr, pokud je k dispozici, určuje stavový kód.</span><span class="sxs-lookup"><span data-stu-id="04b2e-179">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="04b2e-180">Pokud nezadáte stavový kód, stavový kód se standardně *302 – Nalezeno*, což znamená, že prostředek je dočasně přesunut nebo nahrazen.</span><span class="sxs-lookup"><span data-stu-id="04b2e-180">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="04b2e-181">V prohlížeči s povolenými vývojářskými nástroji vytvořte žádost o ukázkovou aplikaci s cestou `/redirect-rule/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-181">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="04b2e-182">Regulární výraz odpovídá cestě požadavku na `redirect-rule/(.*)` a cesta je nahrazena řetězcem `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-182">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="04b2e-183">Adresa URL pro přesměrování se pošle zpátky do klienta s kódem stavu 302, který se *našel* .</span><span class="sxs-lookup"><span data-stu-id="04b2e-183">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="04b2e-184">Prohlížeč vytvoří novou žádost na adrese URL pro přesměrování, která se zobrazí v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="04b2e-184">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="04b2e-185">Vzhledem k tomu, že se žádná pravidla v ukázkové aplikaci neshodují s adresou URL pro přesměrování:</span><span class="sxs-lookup"><span data-stu-id="04b2e-185">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="04b2e-186">Druhá žádost obdrží odpověď *200-OK* od aplikace.</span><span class="sxs-lookup"><span data-stu-id="04b2e-186">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="04b2e-187">Tělo odpovědi zobrazuje adresu URL pro přesměrování.</span><span class="sxs-lookup"><span data-stu-id="04b2e-187">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="04b2e-188">Při *přesměrování*adresy URL se na Server provede cyklická výměna.</span><span class="sxs-lookup"><span data-stu-id="04b2e-188">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="04b2e-189">Buďte opatrní při vytváření pravidel přesměrování.</span><span class="sxs-lookup"><span data-stu-id="04b2e-189">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="04b2e-190">Pravidla přesměrování se vyhodnocují při každém požadavku na aplikaci, včetně po přesměrování.</span><span class="sxs-lookup"><span data-stu-id="04b2e-190">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="04b2e-191">Je snadné vytvořit *smyčku nekonečných přesměrování*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-191">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="04b2e-192">Původní požadavek:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="04b2e-192">Original Request: `/redirect-rule/1234/5678`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="04b2e-194">Část výrazu obsažená v závorkách se nazývá *Skupina zachycení*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-194">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="04b2e-195">Tečka ( `.` ) výrazu znamená, že *odpovídá libovolnému znaku*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-195">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="04b2e-196">Hvězdička ( `*` ) značí, *že se předchozí znak rovná nule nebo vícekrát*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-196">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="04b2e-197">Proto jsou poslední dva segmenty cesty adresy URL `1234/5678` zachyceny skupinou zachycení `(.*)` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-197">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="04b2e-198">Jakákoli hodnota, kterou zadáte v adrese URL požadavku, potom, co `redirect-rule/` je tato jediná skupina zachycení zachycena.</span><span class="sxs-lookup"><span data-stu-id="04b2e-198">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="04b2e-199">V řetězci pro nahrazení jsou zachycené skupiny vloženy do řetězce s znakem dolaru ( `$` ) následovaným pořadovým číslem záznamu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-199">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="04b2e-200">První hodnota skupiny zachycení je získána s `$1` , druhým s `$2` a pokračuje v pořadí pro skupiny zachycení ve vašem regulárním výrazu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-200">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="04b2e-201">Regulární výraz pravidla přesměrování v ukázkové aplikaci obsahuje jenom jednu zachycenou skupinu, takže v náhradním řetězci je jenom jedna vložená skupina, která je `$1` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-201">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="04b2e-202">Po použití pravidla se adresa URL bude nacházet `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-202">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="04b2e-203">Přesměrování adresy URL na zabezpečený koncový bod</span><span class="sxs-lookup"><span data-stu-id="04b2e-203">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="04b2e-204">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> k přesměrování požadavků HTTP na stejného hostitele a cestu pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="04b2e-204">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="04b2e-205">Pokud kód stavu není zadaný, použije se ve výchozím nastavení middleware *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-205">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="04b2e-206">Pokud port není dodán:</span><span class="sxs-lookup"><span data-stu-id="04b2e-206">If the port isn't supplied:</span></span>

* <span data-ttu-id="04b2e-207">Ve výchozím nastavení se používá middleware `null` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-207">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="04b2e-208">Schéma se změní na `https` (protokol HTTPS) a klient přistupuje k prostředku na portu 443.</span><span class="sxs-lookup"><span data-stu-id="04b2e-208">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="04b2e-209">Následující příklad ukazuje, jak nastavit stavový kód na *301 – trvale přesunuto* a změnit port na 5001.</span><span class="sxs-lookup"><span data-stu-id="04b2e-209">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="04b2e-210">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> k přesměrování nezabezpečených požadavků na stejného hostitele a cestu s protokolem Secure https na portu 443.</span><span class="sxs-lookup"><span data-stu-id="04b2e-210">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="04b2e-211">Middleware nastaví stavový kód na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-211">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="04b2e-212">Při přesměrování na zabezpečený koncový bod bez požadavku na další pravidla přesměrování doporučujeme použít middleware pro přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="04b2e-212">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="04b2e-213">Další informace najdete v tématu věnovaném [vykonání protokolu HTTPS](xref:security/enforcing-ssl#require-https) .</span><span class="sxs-lookup"><span data-stu-id="04b2e-213">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="04b2e-214">Ukázková aplikace dokáže demonstrovat, jak používat `AddRedirectToHttps` nebo `AddRedirectToHttpsPermanent` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-214">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="04b2e-215">Přidejte metodu rozšíření do `RewriteOptions` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-215">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="04b2e-216">Vytvořte nezabezpečenou žádost o aplikaci na libovolné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="04b2e-216">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="04b2e-217">Zrušte upozornění zabezpečení prohlížeče, že certifikát podepsaný svým držitelem je nedůvěryhodný, nebo vytvořte výjimku pro důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-217">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="04b2e-218">Původní požadavek s použitím `AddRedirectToHttps(301, 5001)` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="04b2e-218">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="04b2e-220">Původní požadavek s použitím `AddRedirectToHttpsPermanent` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="04b2e-220">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="04b2e-222">Přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-222">URL rewrite</span></span>

<span data-ttu-id="04b2e-223">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> k vytvoření pravidla pro přepis adres URL.</span><span class="sxs-lookup"><span data-stu-id="04b2e-223">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="04b2e-224">První parametr obsahuje regulární výraz pro porovnání na cestě příchozích adres URL.</span><span class="sxs-lookup"><span data-stu-id="04b2e-224">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="04b2e-225">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="04b2e-225">The second parameter is the replacement string.</span></span> <span data-ttu-id="04b2e-226">Třetí parametr, `skipRemainingRules: {true|false}` označuje middleware bez ohledu na to, zda má přeskočit další pravidla přepsání, pokud je použito aktuální pravidlo.</span><span class="sxs-lookup"><span data-stu-id="04b2e-226">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="04b2e-227">Původní požadavek:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="04b2e-227">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="04b2e-229">Kosočtverce ( `^` ) na začátku výrazu znamená, že shoda začíná na začátku cesty URL.</span><span class="sxs-lookup"><span data-stu-id="04b2e-229">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="04b2e-230">V předchozím příkladu s pravidlem přesměrování `redirect-rule/(.*)` není `^` na začátku regulárního výrazu k dispozici žádná kosočtverce ().</span><span class="sxs-lookup"><span data-stu-id="04b2e-230">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="04b2e-231">Proto jakékoli znaky mohou předcházet `redirect-rule/` v cestě k úspěšné shodě.</span><span class="sxs-lookup"><span data-stu-id="04b2e-231">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="04b2e-232">Cesta</span><span class="sxs-lookup"><span data-stu-id="04b2e-232">Path</span></span>                               | <span data-ttu-id="04b2e-233">Shoda</span><span class="sxs-lookup"><span data-stu-id="04b2e-233">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="04b2e-234">Ano</span><span class="sxs-lookup"><span data-stu-id="04b2e-234">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="04b2e-235">Ano</span><span class="sxs-lookup"><span data-stu-id="04b2e-235">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="04b2e-236">Ano</span><span class="sxs-lookup"><span data-stu-id="04b2e-236">Yes</span></span>   |

<span data-ttu-id="04b2e-237">Pravidlo přepsaného zápisu `^rewrite-rule/(\d+)/(\d+)` odpovídá pouze cestám, pokud začínají na `rewrite-rule/` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-237">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="04b2e-238">V následující tabulce si všimněte rozdílu v porovnání.</span><span class="sxs-lookup"><span data-stu-id="04b2e-238">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="04b2e-239">Cesta</span><span class="sxs-lookup"><span data-stu-id="04b2e-239">Path</span></span>                              | <span data-ttu-id="04b2e-240">Shoda</span><span class="sxs-lookup"><span data-stu-id="04b2e-240">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="04b2e-241">Yes</span><span class="sxs-lookup"><span data-stu-id="04b2e-241">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="04b2e-242">No</span><span class="sxs-lookup"><span data-stu-id="04b2e-242">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="04b2e-243">No</span><span class="sxs-lookup"><span data-stu-id="04b2e-243">No</span></span>    |

<span data-ttu-id="04b2e-244">Po `^rewrite-rule/` části výrazu jsou k dispozici dvě skupiny zachycení, `(\d+)/(\d+)` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-244">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="04b2e-245">`\d`Značí *shodu s číslicí (číslem)*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-245">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="04b2e-246">Znaménko plus ( `+` ) znamená, že se *shoduje s jedním nebo více předcházejícím znakem*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-246">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="04b2e-247">Proto musí adresa URL obsahovat číslo následované lomítkem, za kterým následuje jiné číslo.</span><span class="sxs-lookup"><span data-stu-id="04b2e-247">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="04b2e-248">Tyto skupiny zachycení jsou vloženy do přepsané adresy URL jako `$1` a `$2` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-248">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="04b2e-249">Náhradní řetězec pravidla přepsaní umístí zachycené skupiny do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-249">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="04b2e-250">Požadovaná cesta k `/rewrite-rule/1234/5678` je přepsána za účelem získání prostředku v `/rewritten?var1=1234&var2=5678` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-250">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="04b2e-251">Pokud se v původním požadavku nachází řetězec dotazu, bude při přepisování adresy URL zachován.</span><span class="sxs-lookup"><span data-stu-id="04b2e-251">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="04b2e-252">K získání prostředku neexistuje žádná Přenosová cesta k serveru.</span><span class="sxs-lookup"><span data-stu-id="04b2e-252">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="04b2e-253">Pokud prostředek existuje, načte se a vrátí do klienta s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="04b2e-253">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="04b2e-254">Vzhledem k tomu, že klient není přesměrován, adresa URL v adresním řádku prohlížeče se nezmění.</span><span class="sxs-lookup"><span data-stu-id="04b2e-254">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="04b2e-255">Klienti nerozpoznají, že na serveru došlo k operaci přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="04b2e-255">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="04b2e-256">`skipRemainingRules: true`Kdykoli je to možné, protože vyhovující pravidla jsou výpočetně náročná a zvyšují dobu odezvy aplikace.</span><span class="sxs-lookup"><span data-stu-id="04b2e-256">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="04b2e-257">Pro nejrychlejší aplikační odpověď:</span><span class="sxs-lookup"><span data-stu-id="04b2e-257">For the fastest app response:</span></span>
>
> * <span data-ttu-id="04b2e-258">Seřazení pravidel přepisu z nejčastěji spárovaného pravidla na nejméně často spárované pravidlo.</span><span class="sxs-lookup"><span data-stu-id="04b2e-258">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="04b2e-259">Pokud dojde ke shodě a není vyžadováno žádné další zpracování pravidla, přeskočte zpracování zbývajících pravidel.</span><span class="sxs-lookup"><span data-stu-id="04b2e-259">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="04b2e-260">Mod_rewrite Apache</span><span class="sxs-lookup"><span data-stu-id="04b2e-260">Apache mod_rewrite</span></span>

<span data-ttu-id="04b2e-261">Použijte pravidla mod_rewrite Apache s <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="04b2e-261">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="04b2e-262">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="04b2e-262">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="04b2e-263">Další informace a příklady pravidel mod_rewrite najdete v tématu [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="04b2e-263">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="04b2e-264"><xref:System.IO.StreamReader>Používá se ke čtení pravidel ze souboru pravidel *ApacheModRewrite.txt* :</span><span class="sxs-lookup"><span data-stu-id="04b2e-264">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="04b2e-265">Ukázková aplikace přesměrovává požadavky od `/apache-mod-rules-redirect/(.\*)` do `/redirected?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-265">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="04b2e-266">Stavový kód odpovědi je *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-266">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="04b2e-267">Původní požadavek:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="04b2e-267">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="04b2e-269">Middleware podporuje následující proměnné serveru Apache mod_rewrite:</span><span class="sxs-lookup"><span data-stu-id="04b2e-269">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="04b2e-270">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="04b2e-270">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="04b2e-271">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="04b2e-271">HTTP_ACCEPT</span></span>
* <span data-ttu-id="04b2e-272">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="04b2e-272">HTTP_CONNECTION</span></span>
* <span data-ttu-id="04b2e-273">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="04b2e-273">HTTP_COOKIE</span></span>
* <span data-ttu-id="04b2e-274">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="04b2e-274">HTTP_FORWARDED</span></span>
* <span data-ttu-id="04b2e-275">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="04b2e-275">HTTP_HOST</span></span>
* <span data-ttu-id="04b2e-276">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="04b2e-276">HTTP_REFERER</span></span>
* <span data-ttu-id="04b2e-277">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="04b2e-277">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="04b2e-278">HTTPS</span><span class="sxs-lookup"><span data-stu-id="04b2e-278">HTTPS</span></span>
* <span data-ttu-id="04b2e-279">PROTOKOLŮ</span><span class="sxs-lookup"><span data-stu-id="04b2e-279">IPV6</span></span>
* <span data-ttu-id="04b2e-280">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="04b2e-280">QUERY_STRING</span></span>
* <span data-ttu-id="04b2e-281">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="04b2e-281">REMOTE_ADDR</span></span>
* <span data-ttu-id="04b2e-282">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="04b2e-282">REMOTE_PORT</span></span>
* <span data-ttu-id="04b2e-283">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="04b2e-283">REQUEST_FILENAME</span></span>
* <span data-ttu-id="04b2e-284">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="04b2e-284">REQUEST_METHOD</span></span>
* <span data-ttu-id="04b2e-285">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="04b2e-285">REQUEST_SCHEME</span></span>
* <span data-ttu-id="04b2e-286">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="04b2e-286">REQUEST_URI</span></span>
* <span data-ttu-id="04b2e-287">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="04b2e-287">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="04b2e-288">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="04b2e-288">SERVER_ADDR</span></span>
* <span data-ttu-id="04b2e-289">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="04b2e-289">SERVER_PORT</span></span>
* <span data-ttu-id="04b2e-290">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="04b2e-290">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="04b2e-291">ČAS</span><span class="sxs-lookup"><span data-stu-id="04b2e-291">TIME</span></span>
* <span data-ttu-id="04b2e-292">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="04b2e-292">TIME_DAY</span></span>
* <span data-ttu-id="04b2e-293">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="04b2e-293">TIME_HOUR</span></span>
* <span data-ttu-id="04b2e-294">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="04b2e-294">TIME_MIN</span></span>
* <span data-ttu-id="04b2e-295">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="04b2e-295">TIME_MON</span></span>
* <span data-ttu-id="04b2e-296">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="04b2e-296">TIME_SEC</span></span>
* <span data-ttu-id="04b2e-297">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="04b2e-297">TIME_WDAY</span></span>
* <span data-ttu-id="04b2e-298">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="04b2e-298">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="04b2e-299">Pravidla pro přepsání adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="04b2e-299">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="04b2e-300">Pokud chcete použít stejnou sadu pravidel, která se vztahuje na modul IIS URL Rewrite, použijte <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="04b2e-300">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="04b2e-301">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="04b2e-301">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="04b2e-302">Nesměrujte middleware na použití *web.config* souboru aplikace při spuštění na Windows serveru IIS.</span><span class="sxs-lookup"><span data-stu-id="04b2e-302">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="04b2e-303">V případě služby IIS by tato pravidla měla být uložena mimo soubor *web.config* aplikace, aby nedocházelo ke konfliktům s modulem PŘEPISU služby IIS.</span><span class="sxs-lookup"><span data-stu-id="04b2e-303">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="04b2e-304">Další informace a příklady pravidel pro přepis adres URL služby IIS najdete v tématu použití odkazu na modul pro [přepis adres url 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [odkaz na konfiguraci modulu přepisu adresy](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)URL.</span><span class="sxs-lookup"><span data-stu-id="04b2e-304">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="04b2e-305"><xref:System.IO.StreamReader>Používá se ke čtení pravidel ze souboru pravidel *IISUrlRewrite.xml* :</span><span class="sxs-lookup"><span data-stu-id="04b2e-305">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="04b2e-306">Ukázková aplikace přepíše požadavky od `/iis-rules-rewrite/(.*)` do `/rewritten?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-306">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="04b2e-307">Odpověď se pošle klientovi s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="04b2e-307">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="04b2e-308">Původní požadavek:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="04b2e-308">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="04b2e-310">Pokud máte aktivní modul pro přepis služby IIS s nakonfigurovanými pravidly na úrovni serveru, který by ovlivnil vaši aplikaci nežádoucím způsobem, můžete pro aplikaci zakázat modul IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="04b2e-310">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="04b2e-311">Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="04b2e-311">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="04b2e-312">Nepodporované funkce</span><span class="sxs-lookup"><span data-stu-id="04b2e-312">Unsupported features</span></span>

<span data-ttu-id="04b2e-313">Middleware vydaná ASP.NET Core 2. x nepodporuje následující funkce modulu pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="04b2e-313">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="04b2e-314">Pravidla pro odchozí provoz</span><span class="sxs-lookup"><span data-stu-id="04b2e-314">Outbound Rules</span></span>
* <span data-ttu-id="04b2e-315">Vlastní proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="04b2e-315">Custom Server Variables</span></span>
* <span data-ttu-id="04b2e-316">Zástupné znaky</span><span class="sxs-lookup"><span data-stu-id="04b2e-316">Wildcards</span></span>
* <span data-ttu-id="04b2e-317">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="04b2e-317">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="04b2e-318">Podporované proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="04b2e-318">Supported server variables</span></span>

<span data-ttu-id="04b2e-319">Middleware podporuje následující proměnné serveru pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="04b2e-319">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="04b2e-320">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="04b2e-320">CONTENT_LENGTH</span></span>
* <span data-ttu-id="04b2e-321">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="04b2e-321">CONTENT_TYPE</span></span>
* <span data-ttu-id="04b2e-322">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="04b2e-322">HTTP_ACCEPT</span></span>
* <span data-ttu-id="04b2e-323">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="04b2e-323">HTTP_CONNECTION</span></span>
* <span data-ttu-id="04b2e-324">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="04b2e-324">HTTP_COOKIE</span></span>
* <span data-ttu-id="04b2e-325">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="04b2e-325">HTTP_HOST</span></span>
* <span data-ttu-id="04b2e-326">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="04b2e-326">HTTP_REFERER</span></span>
* <span data-ttu-id="04b2e-327">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-327">HTTP_URL</span></span>
* <span data-ttu-id="04b2e-328">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="04b2e-328">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="04b2e-329">HTTPS</span><span class="sxs-lookup"><span data-stu-id="04b2e-329">HTTPS</span></span>
* <span data-ttu-id="04b2e-330">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="04b2e-330">LOCAL_ADDR</span></span>
* <span data-ttu-id="04b2e-331">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="04b2e-331">QUERY_STRING</span></span>
* <span data-ttu-id="04b2e-332">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="04b2e-332">REMOTE_ADDR</span></span>
* <span data-ttu-id="04b2e-333">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="04b2e-333">REMOTE_PORT</span></span>
* <span data-ttu-id="04b2e-334">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="04b2e-334">REQUEST_FILENAME</span></span>
* <span data-ttu-id="04b2e-335">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="04b2e-335">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="04b2e-336">Můžete také získat <xref:Microsoft.Extensions.FileProviders.IFileProvider> prostřednictvím <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="04b2e-336">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="04b2e-337">Tento přístup může poskytovat větší flexibilitu pro umístění souborů pravidel přepisu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-337">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="04b2e-338">Ujistěte se, že jsou v cestě, kterou zadáte, nasazené soubory pravidel přepisu na server.</span><span class="sxs-lookup"><span data-stu-id="04b2e-338">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="04b2e-339">Pravidlo založené na metodách</span><span class="sxs-lookup"><span data-stu-id="04b2e-339">Method-based rule</span></span>

<span data-ttu-id="04b2e-340">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k implementaci logiky vlastního pravidla v metodě.</span><span class="sxs-lookup"><span data-stu-id="04b2e-340">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="04b2e-341">`Add`zpřístupňuje <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> , který zpřístupňuje <xref:Microsoft.AspNetCore.Http.HttpContext> metodu pro použití v metodě.</span><span class="sxs-lookup"><span data-stu-id="04b2e-341">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="04b2e-342">[RewriteContext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) určuje, jak se zpracovává další zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-342">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="04b2e-343">Nastavte hodnotu na jedno z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> polí popsaných v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="04b2e-343">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| <span data-ttu-id="04b2e-344">Výsledek kontextu přepsání</span><span class="sxs-lookup"><span data-stu-id="04b2e-344">Rewrite context result</span></span>               | <span data-ttu-id="04b2e-345">Akce</span><span class="sxs-lookup"><span data-stu-id="04b2e-345">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="04b2e-346">`RuleResult.ContinueRules`výchozí</span><span class="sxs-lookup"><span data-stu-id="04b2e-346">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="04b2e-347">Pokračovat v používání pravidel.</span><span class="sxs-lookup"><span data-stu-id="04b2e-347">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="04b2e-348">Zastavení použití pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="04b2e-348">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="04b2e-349">Zastavení použití pravidel a odeslání kontextu do dalšího middlewaru.</span><span class="sxs-lookup"><span data-stu-id="04b2e-349">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="04b2e-350">Ukázková aplikace ukazuje metodu, která přesměrovává požadavky na cesty, které končí na *. XML*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-350">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="04b2e-351">Pokud je žádost určena pro `/file.xml` , je požadavek přesměrován na `/xmlfiles/file.xml` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-351">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="04b2e-352">Stavový kód je nastaven na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-352">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="04b2e-353">Když prohlížeč vytvoří nový požadavek na */xmlfiles/file.xml*, soubor middleware statických souborů obsluhuje soubor do klienta ze složky *wwwroot/xmlfiles* .</span><span class="sxs-lookup"><span data-stu-id="04b2e-353">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="04b2e-354">Pro přesměrování explicitně nastavte stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="04b2e-354">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="04b2e-355">V opačném případě se vrátí stavový kód *200-OK* a na klientovi se neobjeví přesměrování.</span><span class="sxs-lookup"><span data-stu-id="04b2e-355">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="04b2e-356">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="04b2e-356">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="04b2e-357">Tento přístup může také přezapisovat požadavky.</span><span class="sxs-lookup"><span data-stu-id="04b2e-357">This approach can also rewrite requests.</span></span> <span data-ttu-id="04b2e-358">Ukázková aplikace ukazuje přepis cesty k libovolné žádosti o textový soubor, aby sloužila *file.txt* textový soubor ze složky *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="04b2e-358">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="04b2e-359">Middleware statických souborů zachovává soubor na základě aktualizované cesty požadavku:</span><span class="sxs-lookup"><span data-stu-id="04b2e-359">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="04b2e-360">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="04b2e-360">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="04b2e-361">Pravidlo založené na IRule</span><span class="sxs-lookup"><span data-stu-id="04b2e-361">IRule-based rule</span></span>

<span data-ttu-id="04b2e-362">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k použití logiky pravidla ve třídě, která implementuje <xref:Microsoft.AspNetCore.Rewrite.IRule> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="04b2e-362">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="04b2e-363">`IRule`poskytuje větší flexibilitu při použití pravidla založeného na metodách.</span><span class="sxs-lookup"><span data-stu-id="04b2e-363">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="04b2e-364">Vaše třída implementace může obsahovat konstruktor, který umožňuje předat parametry pro <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> metodu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-364">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="04b2e-365">Hodnoty parametrů v ukázkové aplikaci pro `extension` a `newPath` jsou zkontrolovány, aby splňovaly několik podmínek.</span><span class="sxs-lookup"><span data-stu-id="04b2e-365">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="04b2e-366">`extension`Musí obsahovat hodnotu a hodnota musí být *. png*, *. jpg*nebo *. gif*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-366">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="04b2e-367">Pokud `newPath` není platná, <xref:System.ArgumentException> je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="04b2e-367">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="04b2e-368">Pokud se pro *image.png*požadavek provede, požadavek se přesměruje na `/png-images/image.png` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-368">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="04b2e-369">Pokud se pro *image.jpg*požadavek provede, požadavek se přesměruje na `/jpg-images/image.jpg` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-369">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="04b2e-370">Stavový kód je nastaven na *301 – trvale přesunuto*a `context.Result` je nastavené na zastavení zpracování pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="04b2e-370">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="04b2e-371">Původní požadavek:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="04b2e-371">Original Request: `/image.png`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="04b2e-373">Původní požadavek:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="04b2e-373">Original Request: `/image.jpg`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="04b2e-375">Příklady Regex</span><span class="sxs-lookup"><span data-stu-id="04b2e-375">Regex examples</span></span>

| <span data-ttu-id="04b2e-376">Cíl</span><span class="sxs-lookup"><span data-stu-id="04b2e-376">Goal</span></span> | <span data-ttu-id="04b2e-377">& řetězce regulárního výrazu</span><span class="sxs-lookup"><span data-stu-id="04b2e-377">Regex String &</span></span><br><span data-ttu-id="04b2e-378">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="04b2e-378">Match Example</span></span> | <span data-ttu-id="04b2e-379">& řetězce nahrazení</span><span class="sxs-lookup"><span data-stu-id="04b2e-379">Replacement String &</span></span><br><span data-ttu-id="04b2e-380">Příklad výstupu</span><span class="sxs-lookup"><span data-stu-id="04b2e-380">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="04b2e-381">Přepište cestu do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-381">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="04b2e-382">Koncové lomítko pruhu</span><span class="sxs-lookup"><span data-stu-id="04b2e-382">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="04b2e-383">Vymáhat koncové lomítko</span><span class="sxs-lookup"><span data-stu-id="04b2e-383">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="04b2e-384">Vyhnout se přepisu konkrétních požadavků</span><span class="sxs-lookup"><span data-stu-id="04b2e-384">Avoid rewriting specific requests</span></span> | <span data-ttu-id="04b2e-385">`^(.*)(?<!\.axd)$` nebo `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="04b2e-385">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="04b2e-386">Ano:`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="04b2e-386">Yes: `/resource.htm`</span></span><br><span data-ttu-id="04b2e-387">Ne:`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="04b2e-387">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="04b2e-388">Změna uspořádání segmentů adresy URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-388">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="04b2e-389">Nahradit segment adresy URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-389">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="04b2e-390">Tento dokument zavádí přepis adres URL s pokyny k použití middlewaru přepisu adresy URL v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="04b2e-390">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="04b2e-391">Přepsání adresy URL je to, že se jedná o úpravu adres URL žádostí na základě jednoho nebo více předdefinovaných pravidel.</span><span class="sxs-lookup"><span data-stu-id="04b2e-391">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="04b2e-392">Přepsání adresy URL vytvoří abstrakci mezi umístěními prostředků a jejich adresami tak, aby umístění a adresy nebyly pevně propojené.</span><span class="sxs-lookup"><span data-stu-id="04b2e-392">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="04b2e-393">Přepsání adresy URL je v několika scénářích užitečné pro:</span><span class="sxs-lookup"><span data-stu-id="04b2e-393">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="04b2e-394">Dočasné nebo trvalé přesunutí nebo nahrazení prostředků serveru a udržování stabilních lokátorů pro tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="04b2e-394">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="04b2e-395">Rozdělte zpracování požadavků napříč různými aplikacemi nebo napříč oblastmi jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="04b2e-395">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="04b2e-396">Umožňuje odebrat, přidat nebo změnit uspořádání segmentů adresy URL příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="04b2e-396">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="04b2e-397">Optimalizujte veřejné adresy URL pro optimalizaci vyhledávače (SEO).</span><span class="sxs-lookup"><span data-stu-id="04b2e-397">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="04b2e-398">Povolí použití přívětivých veřejných adres URL, které návštěvníkům pomůžou odhadnout obsah vrácený požadavkem na prostředek.</span><span class="sxs-lookup"><span data-stu-id="04b2e-398">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="04b2e-399">Přesměrovat nezabezpečené požadavky na zabezpečené koncové body.</span><span class="sxs-lookup"><span data-stu-id="04b2e-399">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="04b2e-400">Zabránit hotlinking, kde externí lokalita používá hostovaný statický prostředek na jiné lokalitě propojením prostředku s vlastním obsahem.</span><span class="sxs-lookup"><span data-stu-id="04b2e-400">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="04b2e-401">Přepsání adresy URL může snížit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="04b2e-401">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="04b2e-402">Pokud je to možné, omezte počet a složitost pravidel.</span><span class="sxs-lookup"><span data-stu-id="04b2e-402">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="04b2e-403">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="04b2e-403">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="04b2e-404">Přesměrování adresy URL a přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-404">URL redirect and URL rewrite</span></span>

<span data-ttu-id="04b2e-405">Rozdíl mezi přesměrováním *URL* a *přepsáním adresy URL* je malý, ale má důležitý dopad na poskytování prostředků klientům.</span><span class="sxs-lookup"><span data-stu-id="04b2e-405">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="04b2e-406">Rozhraní middleware pro přepis adres URL ASP.NET Core je schopné splnit nutnost obou.</span><span class="sxs-lookup"><span data-stu-id="04b2e-406">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="04b2e-407">*Přesměrování adresy URL* zahrnuje i operaci na straně klienta, kde klient má pokyn k přístupu k prostředku na jiné adrese, než jakou požadoval klient původně.</span><span class="sxs-lookup"><span data-stu-id="04b2e-407">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="04b2e-408">To vyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="04b2e-408">This requires a round trip to the server.</span></span> <span data-ttu-id="04b2e-409">Adresa URL pro přesměrování vracená klientovi se zobrazí v adresním řádku prohlížeče, když klient vytvoří nový požadavek na prostředek.</span><span class="sxs-lookup"><span data-stu-id="04b2e-409">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="04b2e-410">Pokud `/resource` je *přesměrován* na `/different-resource` , server odpoví, že by měl klient získat prostředek na `/different-resource` stavovém kódu, což značí, že přesměrování je buď dočasné, nebo trvalé.</span><span class="sxs-lookup"><span data-stu-id="04b2e-410">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Koncový bod služby WebAPI se dočasně změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="04b2e-416">Při přesměrování požadavků na jinou adresu URL určete, zda je přesměrování trvalé nebo dočasné, zadáním stavového kódu s odpovědí:</span><span class="sxs-lookup"><span data-stu-id="04b2e-416">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="04b2e-417">V případě, že má prostředek novou, trvalou adresu URL a chcete dát klientovi pokyn, aby všechny budoucí požadavky na prostředek používaly novou adresu URL, se použije kód *trvale přesunutý stav 301* .</span><span class="sxs-lookup"><span data-stu-id="04b2e-417">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="04b2e-418">*Klient může ukládat odpověď do mezipaměti a znovu ji použít, když se přijme stavový kód 301.*</span><span class="sxs-lookup"><span data-stu-id="04b2e-418">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="04b2e-419">V případě, že je přesměrování dočasné nebo obecně se může změnit, je použit stavový kód *302* .</span><span class="sxs-lookup"><span data-stu-id="04b2e-419">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="04b2e-420">Stavový kód 302 indikuje klientovi, aby neukládal adresu URL a použil ho v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-420">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="04b2e-421">Další informace o stavových kódech najdete v [dokumentu RFC 2616: definice stavového kódu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="04b2e-421">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="04b2e-422">*Přepsání adresy URL* je operace na straně serveru, která poskytuje prostředek z jiné adresy prostředku, než jakou požaduje klient.</span><span class="sxs-lookup"><span data-stu-id="04b2e-422">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="04b2e-423">Přepsání adresy URL nevyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="04b2e-423">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="04b2e-424">Přepsaná adresa URL se nevrátí klientovi a nezobrazí se v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="04b2e-424">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="04b2e-425">Pokud `/resource` je *přepsáno* na `/different-resource` , server *interně* načte a vrátí prostředek na `/different-resource` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-425">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="04b2e-426">I když klient může být schopný načíst prostředek na adrese URL přepsané adresy, klient nebude informovat o tom, že prostředek existuje na přepsané adrese URL, když odešle svůj požadavek a obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="04b2e-426">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Koncový bod služby WebAPI se změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="04b2e-431">Ukázková aplikace pro přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-431">URL rewriting sample app</span></span>

<span data-ttu-id="04b2e-432">Pomocí [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)můžete prozkoumat funkce middlewaru přepsaného adresou URL.</span><span class="sxs-lookup"><span data-stu-id="04b2e-432">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="04b2e-433">Aplikace použije pravidla přesměrování a přepisu a zobrazí přesměrovanou nebo přepsanou adresu URL pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="04b2e-433">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="04b2e-434">Kdy použít middleware přepisu adresy URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-434">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="04b2e-435">Pokud nemůžete použít následující přístupy, použijte middleware přepisující adresu URL:</span><span class="sxs-lookup"><span data-stu-id="04b2e-435">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="04b2e-436">Modul pro přepsání adresy URL ve službě IIS v systému Windows Server</span><span class="sxs-lookup"><span data-stu-id="04b2e-436">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="04b2e-437">Apache mod_rewrite modul na serveru Apache</span><span class="sxs-lookup"><span data-stu-id="04b2e-437">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="04b2e-438">Přepsání adresy URL na Nginx</span><span class="sxs-lookup"><span data-stu-id="04b2e-438">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="04b2e-439">Používejte také middleware při hostování aplikace na [serveruHTTP.sys](xref:fundamentals/servers/httpsys) (dříve nazývaném weblisten).</span><span class="sxs-lookup"><span data-stu-id="04b2e-439">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="04b2e-440">Hlavními důvody pro použití technologie přepisování adres URL založené na serveru ve službě IIS, Apache a Nginx jsou:</span><span class="sxs-lookup"><span data-stu-id="04b2e-440">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="04b2e-441">Middleware nepodporuje všechny funkce těchto modulů.</span><span class="sxs-lookup"><span data-stu-id="04b2e-441">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="04b2e-442">Některé funkce v modulech serveru nefungují s ASP.NET Core projekty, jako jsou `IsFile` `IsDirectory` omezení a modulu IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="04b2e-442">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="04b2e-443">V těchto scénářích místo toho použijte middleware.</span><span class="sxs-lookup"><span data-stu-id="04b2e-443">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="04b2e-444">Výkon middleware se pravděpodobně neshoduje s modulem.</span><span class="sxs-lookup"><span data-stu-id="04b2e-444">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="04b2e-445">Srovnávací testy jsou jediným způsobem, jak zjistit, který přístup snižuje výkon, který je v nejvyšší nebo v případě, že je snížený výkon.</span><span class="sxs-lookup"><span data-stu-id="04b2e-445">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="04b2e-446">Balíček</span><span class="sxs-lookup"><span data-stu-id="04b2e-446">Package</span></span>

<span data-ttu-id="04b2e-447">Chcete-li do projektu zahrnout middleware, přidejte odkaz na balíček do souboru [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) v souboru projektu, který obsahuje balíček [Microsoft. AspNetCore. Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) .</span><span class="sxs-lookup"><span data-stu-id="04b2e-447">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="04b2e-448">Pokud nepoužíváte `Microsoft.AspNetCore.App` Metapackage, přidejte do balíčku odkaz na projekt `Microsoft.AspNetCore.Rewrite` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-448">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="04b2e-449">Rozšíření a možnosti</span><span class="sxs-lookup"><span data-stu-id="04b2e-449">Extension and options</span></span>

<span data-ttu-id="04b2e-450">Vytvořte pravidla přepsání a přesměrování adresy URL vytvořením instance třídy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) s metodami rozšíření pro každé pravidlo přepsání.</span><span class="sxs-lookup"><span data-stu-id="04b2e-450">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="04b2e-451">Řetězení více pravidel v pořadí, ve kterém se mají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="04b2e-451">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="04b2e-452">Rozhraní se `RewriteOptions` předává do middleware přepisování adresy URL při jeho přidání do kanálu požadavků pomocí <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :</span><span class="sxs-lookup"><span data-stu-id="04b2e-452">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="04b2e-453">Přesměrovat neexistující na webovou</span><span class="sxs-lookup"><span data-stu-id="04b2e-453">Redirect non-www to www</span></span>

<span data-ttu-id="04b2e-454">Tři možnosti umožňují, aby aplikace přesměrovala `www` požadavky bez požadavků na `www` :</span><span class="sxs-lookup"><span data-stu-id="04b2e-454">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="04b2e-455"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Trvale přesměrujte požadavek na `www` subdoménu, pokud je požadavek jiný než `www` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-455"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="04b2e-456">Přesměruje stavový kód [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) .</span><span class="sxs-lookup"><span data-stu-id="04b2e-456">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="04b2e-457"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Přesměrujte požadavek na `www` subdoménu, pokud je příchozí požadavek jiný než `www` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-457"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="04b2e-458">Přesměruje stavový kód [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) .</span><span class="sxs-lookup"><span data-stu-id="04b2e-458">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="04b2e-459">Přetížení umožňuje poskytnout stavový kód pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="04b2e-459">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="04b2e-460">Použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy pro přiřazení stavového kódu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-460">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="04b2e-461">Přesměrování adresy URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-461">URL redirect</span></span>

<span data-ttu-id="04b2e-462">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> k přesměrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="04b2e-462">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="04b2e-463">První parametr obsahuje váš regulární výraz pro porovnání s cestou příchozí adresy URL.</span><span class="sxs-lookup"><span data-stu-id="04b2e-463">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="04b2e-464">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="04b2e-464">The second parameter is the replacement string.</span></span> <span data-ttu-id="04b2e-465">Třetí parametr, pokud je k dispozici, určuje stavový kód.</span><span class="sxs-lookup"><span data-stu-id="04b2e-465">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="04b2e-466">Pokud nezadáte stavový kód, stavový kód se standardně *302 – Nalezeno*, což znamená, že prostředek je dočasně přesunut nebo nahrazen.</span><span class="sxs-lookup"><span data-stu-id="04b2e-466">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="04b2e-467">V prohlížeči s povolenými vývojářskými nástroji vytvořte žádost o ukázkovou aplikaci s cestou `/redirect-rule/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-467">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="04b2e-468">Regulární výraz odpovídá cestě požadavku na `redirect-rule/(.*)` a cesta je nahrazena řetězcem `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-468">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="04b2e-469">Adresa URL pro přesměrování se pošle zpátky do klienta s kódem stavu 302, který se *našel* .</span><span class="sxs-lookup"><span data-stu-id="04b2e-469">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="04b2e-470">Prohlížeč vytvoří novou žádost na adrese URL pro přesměrování, která se zobrazí v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="04b2e-470">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="04b2e-471">Vzhledem k tomu, že se žádná pravidla v ukázkové aplikaci neshodují s adresou URL pro přesměrování:</span><span class="sxs-lookup"><span data-stu-id="04b2e-471">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="04b2e-472">Druhá žádost obdrží odpověď *200-OK* od aplikace.</span><span class="sxs-lookup"><span data-stu-id="04b2e-472">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="04b2e-473">Tělo odpovědi zobrazuje adresu URL pro přesměrování.</span><span class="sxs-lookup"><span data-stu-id="04b2e-473">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="04b2e-474">Při *přesměrování*adresy URL se na Server provede cyklická výměna.</span><span class="sxs-lookup"><span data-stu-id="04b2e-474">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="04b2e-475">Buďte opatrní při vytváření pravidel přesměrování.</span><span class="sxs-lookup"><span data-stu-id="04b2e-475">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="04b2e-476">Pravidla přesměrování se vyhodnocují při každém požadavku na aplikaci, včetně po přesměrování.</span><span class="sxs-lookup"><span data-stu-id="04b2e-476">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="04b2e-477">Je snadné vytvořit *smyčku nekonečných přesměrování*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-477">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="04b2e-478">Původní požadavek:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="04b2e-478">Original Request: `/redirect-rule/1234/5678`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="04b2e-480">Část výrazu obsažená v závorkách se nazývá *Skupina zachycení*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-480">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="04b2e-481">Tečka ( `.` ) výrazu znamená, že *odpovídá libovolnému znaku*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-481">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="04b2e-482">Hvězdička ( `*` ) značí, *že se předchozí znak rovná nule nebo vícekrát*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-482">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="04b2e-483">Proto jsou poslední dva segmenty cesty adresy URL `1234/5678` zachyceny skupinou zachycení `(.*)` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-483">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="04b2e-484">Jakákoli hodnota, kterou zadáte v adrese URL požadavku, potom, co `redirect-rule/` je tato jediná skupina zachycení zachycena.</span><span class="sxs-lookup"><span data-stu-id="04b2e-484">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="04b2e-485">V řetězci pro nahrazení jsou zachycené skupiny vloženy do řetězce s znakem dolaru ( `$` ) následovaným pořadovým číslem záznamu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-485">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="04b2e-486">První hodnota skupiny zachycení je získána s `$1` , druhým s `$2` a pokračuje v pořadí pro skupiny zachycení ve vašem regulárním výrazu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-486">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="04b2e-487">Regulární výraz pravidla přesměrování v ukázkové aplikaci obsahuje jenom jednu zachycenou skupinu, takže v náhradním řetězci je jenom jedna vložená skupina, která je `$1` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-487">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="04b2e-488">Po použití pravidla se adresa URL bude nacházet `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-488">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="04b2e-489">Přesměrování adresy URL na zabezpečený koncový bod</span><span class="sxs-lookup"><span data-stu-id="04b2e-489">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="04b2e-490">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> k přesměrování požadavků HTTP na stejného hostitele a cestu pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="04b2e-490">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="04b2e-491">Pokud kód stavu není zadaný, použije se ve výchozím nastavení middleware *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-491">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="04b2e-492">Pokud port není dodán:</span><span class="sxs-lookup"><span data-stu-id="04b2e-492">If the port isn't supplied:</span></span>

* <span data-ttu-id="04b2e-493">Ve výchozím nastavení se používá middleware `null` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-493">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="04b2e-494">Schéma se změní na `https` (protokol HTTPS) a klient přistupuje k prostředku na portu 443.</span><span class="sxs-lookup"><span data-stu-id="04b2e-494">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="04b2e-495">Následující příklad ukazuje, jak nastavit stavový kód na *301 – trvale přesunuto* a změnit port na 5001.</span><span class="sxs-lookup"><span data-stu-id="04b2e-495">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="04b2e-496">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> k přesměrování nezabezpečených požadavků na stejného hostitele a cestu s protokolem Secure https na portu 443.</span><span class="sxs-lookup"><span data-stu-id="04b2e-496">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="04b2e-497">Middleware nastaví stavový kód na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-497">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="04b2e-498">Při přesměrování na zabezpečený koncový bod bez požadavku na další pravidla přesměrování doporučujeme použít middleware pro přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="04b2e-498">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="04b2e-499">Další informace najdete v tématu věnovaném [vykonání protokolu HTTPS](xref:security/enforcing-ssl#require-https) .</span><span class="sxs-lookup"><span data-stu-id="04b2e-499">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="04b2e-500">Ukázková aplikace dokáže demonstrovat, jak používat `AddRedirectToHttps` nebo `AddRedirectToHttpsPermanent` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-500">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="04b2e-501">Přidejte metodu rozšíření do `RewriteOptions` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-501">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="04b2e-502">Vytvořte nezabezpečenou žádost o aplikaci na libovolné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="04b2e-502">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="04b2e-503">Zrušte upozornění zabezpečení prohlížeče, že certifikát podepsaný svým držitelem je nedůvěryhodný, nebo vytvořte výjimku pro důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-503">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="04b2e-504">Původní požadavek s použitím `AddRedirectToHttps(301, 5001)` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="04b2e-504">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="04b2e-506">Původní požadavek s použitím `AddRedirectToHttpsPermanent` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="04b2e-506">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="04b2e-508">Přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-508">URL rewrite</span></span>

<span data-ttu-id="04b2e-509">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> k vytvoření pravidla pro přepis adres URL.</span><span class="sxs-lookup"><span data-stu-id="04b2e-509">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="04b2e-510">První parametr obsahuje regulární výraz pro porovnání na cestě příchozích adres URL.</span><span class="sxs-lookup"><span data-stu-id="04b2e-510">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="04b2e-511">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="04b2e-511">The second parameter is the replacement string.</span></span> <span data-ttu-id="04b2e-512">Třetí parametr, `skipRemainingRules: {true|false}` označuje middleware bez ohledu na to, zda má přeskočit další pravidla přepsání, pokud je použito aktuální pravidlo.</span><span class="sxs-lookup"><span data-stu-id="04b2e-512">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="04b2e-513">Původní požadavek:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="04b2e-513">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="04b2e-515">Kosočtverce ( `^` ) na začátku výrazu znamená, že shoda začíná na začátku cesty URL.</span><span class="sxs-lookup"><span data-stu-id="04b2e-515">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="04b2e-516">V předchozím příkladu s pravidlem přesměrování `redirect-rule/(.*)` není `^` na začátku regulárního výrazu k dispozici žádná kosočtverce ().</span><span class="sxs-lookup"><span data-stu-id="04b2e-516">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="04b2e-517">Proto jakékoli znaky mohou předcházet `redirect-rule/` v cestě k úspěšné shodě.</span><span class="sxs-lookup"><span data-stu-id="04b2e-517">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="04b2e-518">Cesta</span><span class="sxs-lookup"><span data-stu-id="04b2e-518">Path</span></span>                               | <span data-ttu-id="04b2e-519">Shoda</span><span class="sxs-lookup"><span data-stu-id="04b2e-519">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="04b2e-520">Ano</span><span class="sxs-lookup"><span data-stu-id="04b2e-520">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="04b2e-521">Ano</span><span class="sxs-lookup"><span data-stu-id="04b2e-521">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="04b2e-522">Ano</span><span class="sxs-lookup"><span data-stu-id="04b2e-522">Yes</span></span>   |

<span data-ttu-id="04b2e-523">Pravidlo přepsaného zápisu `^rewrite-rule/(\d+)/(\d+)` odpovídá pouze cestám, pokud začínají na `rewrite-rule/` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-523">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="04b2e-524">V následující tabulce si všimněte rozdílu v porovnání.</span><span class="sxs-lookup"><span data-stu-id="04b2e-524">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="04b2e-525">Cesta</span><span class="sxs-lookup"><span data-stu-id="04b2e-525">Path</span></span>                              | <span data-ttu-id="04b2e-526">Shoda</span><span class="sxs-lookup"><span data-stu-id="04b2e-526">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="04b2e-527">Yes</span><span class="sxs-lookup"><span data-stu-id="04b2e-527">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="04b2e-528">No</span><span class="sxs-lookup"><span data-stu-id="04b2e-528">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="04b2e-529">No</span><span class="sxs-lookup"><span data-stu-id="04b2e-529">No</span></span>    |

<span data-ttu-id="04b2e-530">Po `^rewrite-rule/` části výrazu jsou k dispozici dvě skupiny zachycení, `(\d+)/(\d+)` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-530">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="04b2e-531">`\d`Značí *shodu s číslicí (číslem)*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-531">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="04b2e-532">Znaménko plus ( `+` ) znamená, že se *shoduje s jedním nebo více předcházejícím znakem*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-532">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="04b2e-533">Proto musí adresa URL obsahovat číslo následované lomítkem, za kterým následuje jiné číslo.</span><span class="sxs-lookup"><span data-stu-id="04b2e-533">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="04b2e-534">Tyto skupiny zachycení jsou vloženy do přepsané adresy URL jako `$1` a `$2` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-534">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="04b2e-535">Náhradní řetězec pravidla přepsaní umístí zachycené skupiny do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-535">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="04b2e-536">Požadovaná cesta k `/rewrite-rule/1234/5678` je přepsána za účelem získání prostředku v `/rewritten?var1=1234&var2=5678` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-536">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="04b2e-537">Pokud se v původním požadavku nachází řetězec dotazu, bude při přepisování adresy URL zachován.</span><span class="sxs-lookup"><span data-stu-id="04b2e-537">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="04b2e-538">K získání prostředku neexistuje žádná Přenosová cesta k serveru.</span><span class="sxs-lookup"><span data-stu-id="04b2e-538">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="04b2e-539">Pokud prostředek existuje, načte se a vrátí do klienta s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="04b2e-539">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="04b2e-540">Vzhledem k tomu, že klient není přesměrován, adresa URL v adresním řádku prohlížeče se nezmění.</span><span class="sxs-lookup"><span data-stu-id="04b2e-540">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="04b2e-541">Klienti nerozpoznají, že na serveru došlo k operaci přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="04b2e-541">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="04b2e-542">`skipRemainingRules: true`Kdykoli je to možné, protože vyhovující pravidla jsou výpočetně náročná a zvyšují dobu odezvy aplikace.</span><span class="sxs-lookup"><span data-stu-id="04b2e-542">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="04b2e-543">Pro nejrychlejší aplikační odpověď:</span><span class="sxs-lookup"><span data-stu-id="04b2e-543">For the fastest app response:</span></span>
>
> * <span data-ttu-id="04b2e-544">Seřazení pravidel přepisu z nejčastěji spárovaného pravidla na nejméně často spárované pravidlo.</span><span class="sxs-lookup"><span data-stu-id="04b2e-544">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="04b2e-545">Pokud dojde ke shodě a není vyžadováno žádné další zpracování pravidla, přeskočte zpracování zbývajících pravidel.</span><span class="sxs-lookup"><span data-stu-id="04b2e-545">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="04b2e-546">Mod_rewrite Apache</span><span class="sxs-lookup"><span data-stu-id="04b2e-546">Apache mod_rewrite</span></span>

<span data-ttu-id="04b2e-547">Použijte pravidla mod_rewrite Apache s <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="04b2e-547">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="04b2e-548">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="04b2e-548">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="04b2e-549">Další informace a příklady pravidel mod_rewrite najdete v tématu [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="04b2e-549">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="04b2e-550"><xref:System.IO.StreamReader>Používá se ke čtení pravidel ze souboru pravidel *ApacheModRewrite.txt* :</span><span class="sxs-lookup"><span data-stu-id="04b2e-550">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="04b2e-551">Ukázková aplikace přesměrovává požadavky od `/apache-mod-rules-redirect/(.\*)` do `/redirected?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-551">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="04b2e-552">Stavový kód odpovědi je *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-552">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="04b2e-553">Původní požadavek:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="04b2e-553">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="04b2e-555">Middleware podporuje následující proměnné serveru Apache mod_rewrite:</span><span class="sxs-lookup"><span data-stu-id="04b2e-555">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="04b2e-556">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="04b2e-556">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="04b2e-557">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="04b2e-557">HTTP_ACCEPT</span></span>
* <span data-ttu-id="04b2e-558">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="04b2e-558">HTTP_CONNECTION</span></span>
* <span data-ttu-id="04b2e-559">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="04b2e-559">HTTP_COOKIE</span></span>
* <span data-ttu-id="04b2e-560">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="04b2e-560">HTTP_FORWARDED</span></span>
* <span data-ttu-id="04b2e-561">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="04b2e-561">HTTP_HOST</span></span>
* <span data-ttu-id="04b2e-562">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="04b2e-562">HTTP_REFERER</span></span>
* <span data-ttu-id="04b2e-563">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="04b2e-563">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="04b2e-564">HTTPS</span><span class="sxs-lookup"><span data-stu-id="04b2e-564">HTTPS</span></span>
* <span data-ttu-id="04b2e-565">PROTOKOLŮ</span><span class="sxs-lookup"><span data-stu-id="04b2e-565">IPV6</span></span>
* <span data-ttu-id="04b2e-566">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="04b2e-566">QUERY_STRING</span></span>
* <span data-ttu-id="04b2e-567">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="04b2e-567">REMOTE_ADDR</span></span>
* <span data-ttu-id="04b2e-568">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="04b2e-568">REMOTE_PORT</span></span>
* <span data-ttu-id="04b2e-569">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="04b2e-569">REQUEST_FILENAME</span></span>
* <span data-ttu-id="04b2e-570">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="04b2e-570">REQUEST_METHOD</span></span>
* <span data-ttu-id="04b2e-571">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="04b2e-571">REQUEST_SCHEME</span></span>
* <span data-ttu-id="04b2e-572">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="04b2e-572">REQUEST_URI</span></span>
* <span data-ttu-id="04b2e-573">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="04b2e-573">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="04b2e-574">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="04b2e-574">SERVER_ADDR</span></span>
* <span data-ttu-id="04b2e-575">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="04b2e-575">SERVER_PORT</span></span>
* <span data-ttu-id="04b2e-576">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="04b2e-576">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="04b2e-577">ČAS</span><span class="sxs-lookup"><span data-stu-id="04b2e-577">TIME</span></span>
* <span data-ttu-id="04b2e-578">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="04b2e-578">TIME_DAY</span></span>
* <span data-ttu-id="04b2e-579">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="04b2e-579">TIME_HOUR</span></span>
* <span data-ttu-id="04b2e-580">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="04b2e-580">TIME_MIN</span></span>
* <span data-ttu-id="04b2e-581">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="04b2e-581">TIME_MON</span></span>
* <span data-ttu-id="04b2e-582">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="04b2e-582">TIME_SEC</span></span>
* <span data-ttu-id="04b2e-583">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="04b2e-583">TIME_WDAY</span></span>
* <span data-ttu-id="04b2e-584">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="04b2e-584">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="04b2e-585">Pravidla pro přepsání adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="04b2e-585">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="04b2e-586">Pokud chcete použít stejnou sadu pravidel, která se vztahuje na modul IIS URL Rewrite, použijte <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="04b2e-586">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="04b2e-587">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="04b2e-587">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="04b2e-588">Nesměrujte middleware na použití *web.config* souboru aplikace při spuštění na Windows serveru IIS.</span><span class="sxs-lookup"><span data-stu-id="04b2e-588">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="04b2e-589">V případě služby IIS by tato pravidla měla být uložena mimo soubor *web.config* aplikace, aby nedocházelo ke konfliktům s modulem PŘEPISU služby IIS.</span><span class="sxs-lookup"><span data-stu-id="04b2e-589">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="04b2e-590">Další informace a příklady pravidel pro přepis adres URL služby IIS najdete v tématu použití odkazu na modul pro [přepis adres url 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [odkaz na konfiguraci modulu přepisu adresy](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)URL.</span><span class="sxs-lookup"><span data-stu-id="04b2e-590">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="04b2e-591"><xref:System.IO.StreamReader>Používá se ke čtení pravidel ze souboru pravidel *IISUrlRewrite.xml* :</span><span class="sxs-lookup"><span data-stu-id="04b2e-591">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="04b2e-592">Ukázková aplikace přepíše požadavky od `/iis-rules-rewrite/(.*)` do `/rewritten?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-592">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="04b2e-593">Odpověď se pošle klientovi s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="04b2e-593">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="04b2e-594">Původní požadavek:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="04b2e-594">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="04b2e-596">Pokud máte aktivní modul pro přepis služby IIS s nakonfigurovanými pravidly na úrovni serveru, který by ovlivnil vaši aplikaci nežádoucím způsobem, můžete pro aplikaci zakázat modul IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="04b2e-596">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="04b2e-597">Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="04b2e-597">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="04b2e-598">Nepodporované funkce</span><span class="sxs-lookup"><span data-stu-id="04b2e-598">Unsupported features</span></span>

<span data-ttu-id="04b2e-599">Middleware vydaná ASP.NET Core 2. x nepodporuje následující funkce modulu pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="04b2e-599">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="04b2e-600">Pravidla pro odchozí provoz</span><span class="sxs-lookup"><span data-stu-id="04b2e-600">Outbound Rules</span></span>
* <span data-ttu-id="04b2e-601">Vlastní proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="04b2e-601">Custom Server Variables</span></span>
* <span data-ttu-id="04b2e-602">Zástupné znaky</span><span class="sxs-lookup"><span data-stu-id="04b2e-602">Wildcards</span></span>
* <span data-ttu-id="04b2e-603">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="04b2e-603">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="04b2e-604">Podporované proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="04b2e-604">Supported server variables</span></span>

<span data-ttu-id="04b2e-605">Middleware podporuje následující proměnné serveru pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="04b2e-605">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="04b2e-606">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="04b2e-606">CONTENT_LENGTH</span></span>
* <span data-ttu-id="04b2e-607">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="04b2e-607">CONTENT_TYPE</span></span>
* <span data-ttu-id="04b2e-608">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="04b2e-608">HTTP_ACCEPT</span></span>
* <span data-ttu-id="04b2e-609">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="04b2e-609">HTTP_CONNECTION</span></span>
* <span data-ttu-id="04b2e-610">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="04b2e-610">HTTP_COOKIE</span></span>
* <span data-ttu-id="04b2e-611">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="04b2e-611">HTTP_HOST</span></span>
* <span data-ttu-id="04b2e-612">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="04b2e-612">HTTP_REFERER</span></span>
* <span data-ttu-id="04b2e-613">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-613">HTTP_URL</span></span>
* <span data-ttu-id="04b2e-614">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="04b2e-614">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="04b2e-615">HTTPS</span><span class="sxs-lookup"><span data-stu-id="04b2e-615">HTTPS</span></span>
* <span data-ttu-id="04b2e-616">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="04b2e-616">LOCAL_ADDR</span></span>
* <span data-ttu-id="04b2e-617">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="04b2e-617">QUERY_STRING</span></span>
* <span data-ttu-id="04b2e-618">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="04b2e-618">REMOTE_ADDR</span></span>
* <span data-ttu-id="04b2e-619">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="04b2e-619">REMOTE_PORT</span></span>
* <span data-ttu-id="04b2e-620">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="04b2e-620">REQUEST_FILENAME</span></span>
* <span data-ttu-id="04b2e-621">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="04b2e-621">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="04b2e-622">Můžete také získat <xref:Microsoft.Extensions.FileProviders.IFileProvider> prostřednictvím <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="04b2e-622">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="04b2e-623">Tento přístup může poskytovat větší flexibilitu pro umístění souborů pravidel přepisu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-623">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="04b2e-624">Ujistěte se, že jsou v cestě, kterou zadáte, nasazené soubory pravidel přepisu na server.</span><span class="sxs-lookup"><span data-stu-id="04b2e-624">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="04b2e-625">Pravidlo založené na metodách</span><span class="sxs-lookup"><span data-stu-id="04b2e-625">Method-based rule</span></span>

<span data-ttu-id="04b2e-626">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k implementaci logiky vlastního pravidla v metodě.</span><span class="sxs-lookup"><span data-stu-id="04b2e-626">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="04b2e-627">`Add`zpřístupňuje <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> , který zpřístupňuje <xref:Microsoft.AspNetCore.Http.HttpContext> metodu pro použití v metodě.</span><span class="sxs-lookup"><span data-stu-id="04b2e-627">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="04b2e-628">[RewriteContext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) určuje, jak se zpracovává další zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-628">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="04b2e-629">Nastavte hodnotu na jedno z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> polí popsaných v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="04b2e-629">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| <span data-ttu-id="04b2e-630">Výsledek kontextu přepsání</span><span class="sxs-lookup"><span data-stu-id="04b2e-630">Rewrite context result</span></span>               | <span data-ttu-id="04b2e-631">Akce</span><span class="sxs-lookup"><span data-stu-id="04b2e-631">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="04b2e-632">`RuleResult.ContinueRules`výchozí</span><span class="sxs-lookup"><span data-stu-id="04b2e-632">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="04b2e-633">Pokračovat v používání pravidel.</span><span class="sxs-lookup"><span data-stu-id="04b2e-633">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="04b2e-634">Zastavení použití pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="04b2e-634">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="04b2e-635">Zastavení použití pravidel a odeslání kontextu do dalšího middlewaru.</span><span class="sxs-lookup"><span data-stu-id="04b2e-635">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="04b2e-636">Ukázková aplikace ukazuje metodu, která přesměrovává požadavky na cesty, které končí na *. XML*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-636">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="04b2e-637">Pokud je žádost určena pro `/file.xml` , je požadavek přesměrován na `/xmlfiles/file.xml` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-637">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="04b2e-638">Stavový kód je nastaven na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-638">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="04b2e-639">Když prohlížeč vytvoří nový požadavek na */xmlfiles/file.xml*, soubor middleware statických souborů obsluhuje soubor do klienta ze složky *wwwroot/xmlfiles* .</span><span class="sxs-lookup"><span data-stu-id="04b2e-639">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="04b2e-640">Pro přesměrování explicitně nastavte stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="04b2e-640">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="04b2e-641">V opačném případě se vrátí stavový kód *200-OK* a na klientovi se neobjeví přesměrování.</span><span class="sxs-lookup"><span data-stu-id="04b2e-641">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="04b2e-642">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="04b2e-642">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="04b2e-643">Tento přístup může také přezapisovat požadavky.</span><span class="sxs-lookup"><span data-stu-id="04b2e-643">This approach can also rewrite requests.</span></span> <span data-ttu-id="04b2e-644">Ukázková aplikace ukazuje přepis cesty k libovolné žádosti o textový soubor, aby sloužila *file.txt* textový soubor ze složky *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="04b2e-644">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="04b2e-645">Middleware statických souborů zachovává soubor na základě aktualizované cesty požadavku:</span><span class="sxs-lookup"><span data-stu-id="04b2e-645">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="04b2e-646">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="04b2e-646">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="04b2e-647">Pravidlo založené na IRule</span><span class="sxs-lookup"><span data-stu-id="04b2e-647">IRule-based rule</span></span>

<span data-ttu-id="04b2e-648">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k použití logiky pravidla ve třídě, která implementuje <xref:Microsoft.AspNetCore.Rewrite.IRule> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="04b2e-648">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="04b2e-649">`IRule`poskytuje větší flexibilitu při použití pravidla založeného na metodách.</span><span class="sxs-lookup"><span data-stu-id="04b2e-649">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="04b2e-650">Vaše třída implementace může obsahovat konstruktor, který umožňuje předat parametry pro <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> metodu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-650">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="04b2e-651">Hodnoty parametrů v ukázkové aplikaci pro `extension` a `newPath` jsou zkontrolovány, aby splňovaly několik podmínek.</span><span class="sxs-lookup"><span data-stu-id="04b2e-651">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="04b2e-652">`extension`Musí obsahovat hodnotu a hodnota musí být *. png*, *. jpg*nebo *. gif*.</span><span class="sxs-lookup"><span data-stu-id="04b2e-652">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="04b2e-653">Pokud `newPath` není platná, <xref:System.ArgumentException> je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="04b2e-653">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="04b2e-654">Pokud se pro *image.png*požadavek provede, požadavek se přesměruje na `/png-images/image.png` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-654">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="04b2e-655">Pokud se pro *image.jpg*požadavek provede, požadavek se přesměruje na `/jpg-images/image.jpg` .</span><span class="sxs-lookup"><span data-stu-id="04b2e-655">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="04b2e-656">Stavový kód je nastaven na *301 – trvale přesunuto*a `context.Result` je nastavené na zastavení zpracování pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="04b2e-656">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="04b2e-657">Původní požadavek:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="04b2e-657">Original Request: `/image.png`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="04b2e-659">Původní požadavek:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="04b2e-659">Original Request: `/image.jpg`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="04b2e-661">Příklady Regex</span><span class="sxs-lookup"><span data-stu-id="04b2e-661">Regex examples</span></span>

| <span data-ttu-id="04b2e-662">Cíl</span><span class="sxs-lookup"><span data-stu-id="04b2e-662">Goal</span></span> | <span data-ttu-id="04b2e-663">& řetězce regulárního výrazu</span><span class="sxs-lookup"><span data-stu-id="04b2e-663">Regex String &</span></span><br><span data-ttu-id="04b2e-664">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="04b2e-664">Match Example</span></span> | <span data-ttu-id="04b2e-665">& řetězce nahrazení</span><span class="sxs-lookup"><span data-stu-id="04b2e-665">Replacement String &</span></span><br><span data-ttu-id="04b2e-666">Příklad výstupu</span><span class="sxs-lookup"><span data-stu-id="04b2e-666">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="04b2e-667">Přepište cestu do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="04b2e-667">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="04b2e-668">Koncové lomítko pruhu</span><span class="sxs-lookup"><span data-stu-id="04b2e-668">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="04b2e-669">Vymáhat koncové lomítko</span><span class="sxs-lookup"><span data-stu-id="04b2e-669">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="04b2e-670">Vyhnout se přepisu konkrétních požadavků</span><span class="sxs-lookup"><span data-stu-id="04b2e-670">Avoid rewriting specific requests</span></span> | <span data-ttu-id="04b2e-671">`^(.*)(?<!\.axd)$` nebo `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="04b2e-671">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="04b2e-672">Ano:`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="04b2e-672">Yes: `/resource.htm`</span></span><br><span data-ttu-id="04b2e-673">Ne:`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="04b2e-673">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="04b2e-674">Změna uspořádání segmentů adresy URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-674">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="04b2e-675">Nahradit segment adresy URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-675">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="04b2e-676">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="04b2e-676">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="04b2e-677">Regulární výrazy v rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="04b2e-677">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="04b2e-678">Jazyk regulárních výrazů – stručná referenční dokumentace</span><span class="sxs-lookup"><span data-stu-id="04b2e-678">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="04b2e-679">Mod_rewrite Apache</span><span class="sxs-lookup"><span data-stu-id="04b2e-679">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="04b2e-680">Použití modulu URL pro přepis 2,0 (pro IIS)</span><span class="sxs-lookup"><span data-stu-id="04b2e-680">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="04b2e-681">Odkaz na konfiguraci modulu pro přepis adresy URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-681">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="04b2e-682">Fórum modulu pro přepsání adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="04b2e-682">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="04b2e-683">Zachování jednoduché struktury URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-683">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="04b2e-684">10 tipů a triky pro přepis adres URL</span><span class="sxs-lookup"><span data-stu-id="04b2e-684">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="04b2e-685">Na lomítko nebo ne na lomítko</span><span class="sxs-lookup"><span data-stu-id="04b2e-685">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
