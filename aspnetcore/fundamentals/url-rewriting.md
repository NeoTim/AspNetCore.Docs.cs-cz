---
title: Middleware pro přepis adres URL v ASP.NET Core
author: guardrex
description: Přečtěte si informace o přepsání adresy URL a přesměrování pomocí middlewaru pro přepis adres URL v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
uid: fundamentals/url-rewriting
ms.openlocfilehash: e284d2172af723bb80a7be9f6e6f1a87ebe5208e
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886497"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="53f8c-103">Middleware pro přepis adres URL v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="53f8c-103">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="53f8c-104">Od [Luke Latham](https://github.com/guardrex) a [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="53f8c-104">By [Luke Latham](https://github.com/guardrex) and [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="53f8c-105">Tento dokument zavádí přepis adres URL s pokyny k použití middlewaru přepisu adresy URL v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="53f8c-105">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="53f8c-106">Přepsání adresy URL je to, že se jedná o úpravu adres URL žádostí na základě jednoho nebo více předdefinovaných pravidel.</span><span class="sxs-lookup"><span data-stu-id="53f8c-106">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="53f8c-107">Přepsání adresy URL vytvoří abstrakci mezi umístěními prostředků a jejich adresami tak, aby umístění a adresy nebyly pevně propojené.</span><span class="sxs-lookup"><span data-stu-id="53f8c-107">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="53f8c-108">Přepsání adresy URL je v několika scénářích užitečné pro:</span><span class="sxs-lookup"><span data-stu-id="53f8c-108">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="53f8c-109">Dočasné nebo trvalé přesunutí nebo nahrazení prostředků serveru a udržování stabilních lokátorů pro tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="53f8c-109">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="53f8c-110">Rozdělte zpracování požadavků napříč různými aplikacemi nebo napříč oblastmi jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="53f8c-110">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="53f8c-111">Umožňuje odebrat, přidat nebo změnit uspořádání segmentů adresy URL příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="53f8c-111">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="53f8c-112">Optimalizujte veřejné adresy URL pro optimalizaci vyhledávače (SEO).</span><span class="sxs-lookup"><span data-stu-id="53f8c-112">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="53f8c-113">Povolí použití přívětivých veřejných adres URL, které návštěvníkům pomůžou odhadnout obsah vrácený požadavkem na prostředek.</span><span class="sxs-lookup"><span data-stu-id="53f8c-113">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="53f8c-114">Přesměrovat nezabezpečené požadavky na zabezpečené koncové body.</span><span class="sxs-lookup"><span data-stu-id="53f8c-114">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="53f8c-115">Zabránit hotlinking, kde externí lokalita používá hostovaný statický prostředek na jiné lokalitě propojením prostředku s vlastním obsahem.</span><span class="sxs-lookup"><span data-stu-id="53f8c-115">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="53f8c-116">Přepsání adresy URL může snížit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="53f8c-116">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="53f8c-117">Pokud je to možné, omezte počet a složitost pravidel.</span><span class="sxs-lookup"><span data-stu-id="53f8c-117">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="53f8c-118">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="53f8c-118">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="53f8c-119">Přesměrování adresy URL a přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-119">URL redirect and URL rewrite</span></span>

<span data-ttu-id="53f8c-120">Rozdíl mezi přesměrováním *URL* a přepsáním *adresy URL* je malý, ale má důležitý dopad na poskytování prostředků klientům.</span><span class="sxs-lookup"><span data-stu-id="53f8c-120">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="53f8c-121">Rozhraní middleware pro přepis adres URL ASP.NET Core je schopné splnit nutnost obou.</span><span class="sxs-lookup"><span data-stu-id="53f8c-121">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="53f8c-122">*Přesměrování adresy URL* zahrnuje i operaci na straně klienta, kde klient má pokyn k přístupu k prostředku na jiné adrese, než jakou požadoval klient původně.</span><span class="sxs-lookup"><span data-stu-id="53f8c-122">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="53f8c-123">To vyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="53f8c-123">This requires a round trip to the server.</span></span> <span data-ttu-id="53f8c-124">Adresa URL pro přesměrování vracená klientovi se zobrazí v adresním řádku prohlížeče, když klient vytvoří nový požadavek na prostředek.</span><span class="sxs-lookup"><span data-stu-id="53f8c-124">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="53f8c-125">Pokud `/resource` je *přesměrován* na `/different-resource`, server odpoví, že by měl klient získat prostředek na `/different-resource` stavovém kódu, což značí, že přesměrování je buď dočasné, nebo trvalé.</span><span class="sxs-lookup"><span data-stu-id="53f8c-125">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Koncový bod služby WebAPI se dočasně změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="53f8c-131">Při přesměrování požadavků na jinou adresu URL určete, zda je přesměrování trvalé nebo dočasné, zadáním stavového kódu s odpovědí:</span><span class="sxs-lookup"><span data-stu-id="53f8c-131">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="53f8c-132">V případě, že má prostředek novou, trvalou adresu URL a chcete dát klientovi pokyn, aby všechny budoucí požadavky na prostředek používaly novou adresu URL, se použije kód *trvale přesunutý stav 301* .</span><span class="sxs-lookup"><span data-stu-id="53f8c-132">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="53f8c-133">*Klient může ukládat odpověď do mezipaměti a znovu ji použít, když se přijme stavový kód 301.*</span><span class="sxs-lookup"><span data-stu-id="53f8c-133">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="53f8c-134">V případě, že je přesměrování dočasné nebo obecně se může změnit, je použit stavový kód *302* .</span><span class="sxs-lookup"><span data-stu-id="53f8c-134">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="53f8c-135">Stavový kód 302 indikuje klientovi, aby neukládal adresu URL a použil ho v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-135">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="53f8c-136">Další informace o stavových kódech najdete v [dokumentu RFC 2616: Definice](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)stavových kódů.</span><span class="sxs-lookup"><span data-stu-id="53f8c-136">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="53f8c-137">*Přepsání adresy URL* je operace na straně serveru, která poskytuje prostředek z jiné adresy prostředku, než jakou požaduje klient.</span><span class="sxs-lookup"><span data-stu-id="53f8c-137">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="53f8c-138">Přepsání adresy URL nevyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="53f8c-138">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="53f8c-139">Přepsaná adresa URL se nevrátí klientovi a nezobrazí se v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="53f8c-139">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="53f8c-140">Pokud `/resource` je přepsáno `/different-resource`na, server *interně* načte a vrátí prostředek na `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="53f8c-140">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="53f8c-141">I když klient může být schopný načíst prostředek na adrese URL přepsané adresy, klient nebude informovat o tom, že prostředek existuje na přepsané adrese URL, když odešle svůj požadavek a obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="53f8c-141">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Koncový bod služby WebAPI se změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="53f8c-146">Ukázková aplikace pro přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-146">URL rewriting sample app</span></span>

<span data-ttu-id="53f8c-147">Pomocí [ukázkové aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)můžete prozkoumat funkce middlewaru přepsaného adresou URL.</span><span class="sxs-lookup"><span data-stu-id="53f8c-147">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="53f8c-148">Aplikace použije pravidla přesměrování a přepisu a zobrazí přesměrovanou nebo přepsanou adresu URL pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="53f8c-148">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="53f8c-149">Kdy použít middleware přepisu adresy URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-149">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="53f8c-150">Pokud nemůžete použít následující přístupy, použijte middleware přepisující adresu URL:</span><span class="sxs-lookup"><span data-stu-id="53f8c-150">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="53f8c-151">Modul pro přepsání adresy URL ve službě IIS v systému Windows Server</span><span class="sxs-lookup"><span data-stu-id="53f8c-151">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="53f8c-152">Modul Apache mod_rewrite na serveru Apache</span><span class="sxs-lookup"><span data-stu-id="53f8c-152">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="53f8c-153">Přepsání adresy URL na Nginx</span><span class="sxs-lookup"><span data-stu-id="53f8c-153">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="53f8c-154">Použijte taky middleware, pokud je aplikace hostována na [serveru http. sys](xref:fundamentals/servers/httpsys) (dříve nazývaném weblisten).</span><span class="sxs-lookup"><span data-stu-id="53f8c-154">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="53f8c-155">Hlavními důvody pro použití technologie přepisování adres URL založené na serveru ve službě IIS, Apache a Nginx jsou:</span><span class="sxs-lookup"><span data-stu-id="53f8c-155">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="53f8c-156">Middleware nepodporuje všechny funkce těchto modulů.</span><span class="sxs-lookup"><span data-stu-id="53f8c-156">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="53f8c-157">Některé funkce v modulech serveru nefungují s ASP.NET Core projekty, jako jsou `IsFile` omezení a `IsDirectory` modulu IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="53f8c-157">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="53f8c-158">V těchto scénářích místo toho použijte middleware.</span><span class="sxs-lookup"><span data-stu-id="53f8c-158">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="53f8c-159">Výkon middleware se pravděpodobně neshoduje s modulem.</span><span class="sxs-lookup"><span data-stu-id="53f8c-159">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="53f8c-160">Srovnávací testy jsou jediným způsobem, jak zjistit, který přístup snižuje výkon, který je v nejvyšší nebo v případě, že je snížený výkon.</span><span class="sxs-lookup"><span data-stu-id="53f8c-160">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="53f8c-161">Balíček</span><span class="sxs-lookup"><span data-stu-id="53f8c-161">Package</span></span>

<span data-ttu-id="53f8c-162">Middleware pro přepis adres URL poskytuje balíček [Microsoft. AspNetCore. Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) , který je implicitně zahrnutý v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="53f8c-162">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="53f8c-163">Rozšíření a možnosti</span><span class="sxs-lookup"><span data-stu-id="53f8c-163">Extension and options</span></span>

<span data-ttu-id="53f8c-164">Vytvořte pravidla přepsání a přesměrování adresy URL vytvořením instance třídy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) s metodami rozšíření pro každé pravidlo přepsání.</span><span class="sxs-lookup"><span data-stu-id="53f8c-164">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="53f8c-165">Řetězení více pravidel v pořadí, ve kterém se mají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="53f8c-165">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="53f8c-166">Rozhraní `RewriteOptions` se předává do middleware přepisování adresy URL při jeho přidání do kanálu požadavků pomocí <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span><span class="sxs-lookup"><span data-stu-id="53f8c-166">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="53f8c-167">Přesměrovat neexistující na webovou</span><span class="sxs-lookup"><span data-stu-id="53f8c-167">Redirect non-www to www</span></span>

<span data-ttu-id="53f8c-168">Tři možnosti umožňují, aby aplikace přesměrovala`www` požadavky bez `www`požadavků na:</span><span class="sxs-lookup"><span data-stu-id="53f8c-168">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="53f8c-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>Pokud je žádost jiná než`www`, `www` trvale přesměrujte požadavek na subdoménu. &ndash;</span><span class="sxs-lookup"><span data-stu-id="53f8c-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="53f8c-170">Přesměruje stavový kód [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) .</span><span class="sxs-lookup"><span data-stu-id="53f8c-170">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="53f8c-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>Přesměruje požadavek `www` do subdomény, pokud je`www`příchozí požadavek jiný než. &ndash;</span><span class="sxs-lookup"><span data-stu-id="53f8c-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="53f8c-172">Přesměruje stavový kód [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) .</span><span class="sxs-lookup"><span data-stu-id="53f8c-172">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="53f8c-173">Přetížení umožňuje poskytnout stavový kód pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="53f8c-173">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="53f8c-174">Použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy pro přiřazení stavového kódu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-174">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="53f8c-175">Přesměrování adresy URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-175">URL redirect</span></span>

<span data-ttu-id="53f8c-176">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> k přesměrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="53f8c-176">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="53f8c-177">První parametr obsahuje váš regulární výraz pro porovnání s cestou příchozí adresy URL.</span><span class="sxs-lookup"><span data-stu-id="53f8c-177">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="53f8c-178">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="53f8c-178">The second parameter is the replacement string.</span></span> <span data-ttu-id="53f8c-179">Třetí parametr, pokud je k dispozici, určuje stavový kód.</span><span class="sxs-lookup"><span data-stu-id="53f8c-179">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="53f8c-180">Pokud nezadáte stavový kód, stavový kód se standardně *302 – Nalezeno*, což znamená, že prostředek je dočasně přesunut nebo nahrazen.</span><span class="sxs-lookup"><span data-stu-id="53f8c-180">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="53f8c-181">V prohlížeči s povolenými vývojářskými nástroji vytvořte žádost o ukázkovou aplikaci s cestou `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="53f8c-181">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="53f8c-182">Regulární výraz odpovídá cestě požadavku na `redirect-rule/(.*)`a cesta je nahrazena řetězcem. `/redirected/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="53f8c-182">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="53f8c-183">Adresa URL pro přesměrování se pošle zpátky do klienta s kódem stavu 302, který se *našel* .</span><span class="sxs-lookup"><span data-stu-id="53f8c-183">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="53f8c-184">Prohlížeč vytvoří novou žádost na adrese URL pro přesměrování, která se zobrazí v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="53f8c-184">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="53f8c-185">Vzhledem k tomu, že se žádná pravidla v ukázkové aplikaci neshodují s adresou URL pro přesměrování:</span><span class="sxs-lookup"><span data-stu-id="53f8c-185">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="53f8c-186">Druhá žádost obdrží odpověď *200-OK* od aplikace.</span><span class="sxs-lookup"><span data-stu-id="53f8c-186">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="53f8c-187">Tělo odpovědi zobrazuje adresu URL pro přesměrování.</span><span class="sxs-lookup"><span data-stu-id="53f8c-187">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="53f8c-188">Při *přesměrování*adresy URL se na Server provede cyklická výměna.</span><span class="sxs-lookup"><span data-stu-id="53f8c-188">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="53f8c-189">Buďte opatrní při vytváření pravidel přesměrování.</span><span class="sxs-lookup"><span data-stu-id="53f8c-189">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="53f8c-190">Pravidla přesměrování se vyhodnocují při každém požadavku na aplikaci, včetně po přesměrování.</span><span class="sxs-lookup"><span data-stu-id="53f8c-190">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="53f8c-191">Je snadné vytvořit smyčku nekonečných *přesměrování*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-191">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="53f8c-192">Původní požadavek:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="53f8c-192">Original Request: `/redirect-rule/1234/5678`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="53f8c-194">Část výrazu obsažená v závorkách se nazývá *Skupina zachycení*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-194">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="53f8c-195">Tečka (`.`) výrazu znamená, že *odpovídá libovolnému znaku*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-195">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="53f8c-196">Hvězdička (`*`) značí, *že se předchozí znak rovná nule nebo vícekrát*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-196">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="53f8c-197">Proto jsou poslední dva segmenty cesty adresy URL `1234/5678`zachyceny skupinou `(.*)`zachycení.</span><span class="sxs-lookup"><span data-stu-id="53f8c-197">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="53f8c-198">Jakákoli hodnota, kterou zadáte v adrese URL `redirect-rule/` požadavku, potom, co je tato jediná skupina zachycení zachycena.</span><span class="sxs-lookup"><span data-stu-id="53f8c-198">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="53f8c-199">V řetězci pro nahrazení jsou zachycené skupiny vloženy do řetězce s znakem dolaru (`$`) následovaným pořadovým číslem záznamu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-199">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="53f8c-200">První hodnota skupiny zachycení je získána s `$1`, druhým s `$2`a pokračuje v pořadí pro skupiny zachycení ve vašem regulárním výrazu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-200">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="53f8c-201">Regulární výraz pravidla přesměrování v ukázkové aplikaci obsahuje jenom jednu zachycenou skupinu, takže v náhradním řetězci je jenom jedna vložená skupina, která je `$1`.</span><span class="sxs-lookup"><span data-stu-id="53f8c-201">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="53f8c-202">Po použití pravidla se adresa URL bude `/redirected/1234/5678`nacházet.</span><span class="sxs-lookup"><span data-stu-id="53f8c-202">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="53f8c-203">Přesměrování adresy URL na zabezpečený koncový bod</span><span class="sxs-lookup"><span data-stu-id="53f8c-203">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="53f8c-204">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> k přesměrování požadavků HTTP na stejného hostitele a cestu pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="53f8c-204">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="53f8c-205">Pokud kód stavu není zadaný, použije se ve výchozím nastavení middleware *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-205">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="53f8c-206">Pokud port není dodán:</span><span class="sxs-lookup"><span data-stu-id="53f8c-206">If the port isn't supplied:</span></span>

* <span data-ttu-id="53f8c-207">Ve výchozím nastavení `null`se používá middleware.</span><span class="sxs-lookup"><span data-stu-id="53f8c-207">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="53f8c-208">Schéma se změní na `https` (protokol HTTPS) a klient přistupuje k prostředku na portu 443.</span><span class="sxs-lookup"><span data-stu-id="53f8c-208">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="53f8c-209">Následující příklad ukazuje, jak nastavit stavový kód na *301 – trvale přesunuto* a změnit port na 5001.</span><span class="sxs-lookup"><span data-stu-id="53f8c-209">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="53f8c-210">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> k přesměrování nezabezpečených požadavků na stejného hostitele a cestu s protokolem Secure https na portu 443.</span><span class="sxs-lookup"><span data-stu-id="53f8c-210">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="53f8c-211">Middleware nastaví stavový kód na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-211">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="53f8c-212">Při přesměrování na zabezpečený koncový bod bez požadavku na další pravidla přesměrování doporučujeme použít middleware pro přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="53f8c-212">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="53f8c-213">Další informace najdete v tématu věnovaném vykonání [protokolu HTTPS](xref:security/enforcing-ssl#require-https) .</span><span class="sxs-lookup"><span data-stu-id="53f8c-213">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="53f8c-214">Ukázková aplikace dokáže demonstrovat, jak používat `AddRedirectToHttps` nebo. `AddRedirectToHttpsPermanent`</span><span class="sxs-lookup"><span data-stu-id="53f8c-214">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="53f8c-215">Přidejte metodu rozšíření do `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="53f8c-215">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="53f8c-216">Vytvořte nezabezpečenou žádost o aplikaci na libovolné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="53f8c-216">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="53f8c-217">Zrušte upozornění zabezpečení prohlížeče, že certifikát podepsaný svým držitelem je nedůvěryhodný, nebo vytvořte výjimku pro důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-217">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="53f8c-218">Původní požadavek s `AddRedirectToHttps(301, 5001)`použitím:`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="53f8c-218">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="53f8c-220">Původní požadavek s `AddRedirectToHttpsPermanent`použitím:`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="53f8c-220">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="53f8c-222">Přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-222">URL rewrite</span></span>

<span data-ttu-id="53f8c-223">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> k vytvoření pravidla pro přepis adres URL.</span><span class="sxs-lookup"><span data-stu-id="53f8c-223">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="53f8c-224">První parametr obsahuje regulární výraz pro porovnání na cestě příchozích adres URL.</span><span class="sxs-lookup"><span data-stu-id="53f8c-224">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="53f8c-225">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="53f8c-225">The second parameter is the replacement string.</span></span> <span data-ttu-id="53f8c-226">Třetí parametr, `skipRemainingRules: {true|false}`označuje middleware bez ohledu na to, zda má přeskočit další pravidla přepsání, pokud je použito aktuální pravidlo.</span><span class="sxs-lookup"><span data-stu-id="53f8c-226">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="53f8c-227">Původní požadavek:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="53f8c-227">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="53f8c-229">Kosočtverce (`^`) na začátku výrazu znamená, že shoda začíná na začátku cesty URL.</span><span class="sxs-lookup"><span data-stu-id="53f8c-229">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="53f8c-230">V předchozím příkladu s pravidlem `redirect-rule/(.*)`přesměrování není na začátku regulárního výrazu k dispozici žádná kosočtverce (`^`).</span><span class="sxs-lookup"><span data-stu-id="53f8c-230">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="53f8c-231">Proto jakékoli znaky mohou předcházet `redirect-rule/` v cestě k úspěšné shodě.</span><span class="sxs-lookup"><span data-stu-id="53f8c-231">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="53f8c-232">Cesta</span><span class="sxs-lookup"><span data-stu-id="53f8c-232">Path</span></span>                               | <span data-ttu-id="53f8c-233">Shoda</span><span class="sxs-lookup"><span data-stu-id="53f8c-233">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="53f8c-234">Ano</span><span class="sxs-lookup"><span data-stu-id="53f8c-234">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="53f8c-235">Ano</span><span class="sxs-lookup"><span data-stu-id="53f8c-235">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="53f8c-236">Ano</span><span class="sxs-lookup"><span data-stu-id="53f8c-236">Yes</span></span>   |

<span data-ttu-id="53f8c-237">Pravidlo `^rewrite-rule/(\d+)/(\d+)`přepsaného zápisu odpovídá pouze cestám, pokud `rewrite-rule/`začínají na.</span><span class="sxs-lookup"><span data-stu-id="53f8c-237">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="53f8c-238">V následující tabulce si všimněte rozdílu v porovnání.</span><span class="sxs-lookup"><span data-stu-id="53f8c-238">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="53f8c-239">Cesta</span><span class="sxs-lookup"><span data-stu-id="53f8c-239">Path</span></span>                              | <span data-ttu-id="53f8c-240">Shoda</span><span class="sxs-lookup"><span data-stu-id="53f8c-240">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="53f8c-241">Ano</span><span class="sxs-lookup"><span data-stu-id="53f8c-241">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="53f8c-242">Ne</span><span class="sxs-lookup"><span data-stu-id="53f8c-242">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="53f8c-243">Ne</span><span class="sxs-lookup"><span data-stu-id="53f8c-243">No</span></span>    |

<span data-ttu-id="53f8c-244">Po části výrazu jsou k dispozici dvě skupiny zachycení, `(\d+)/(\d+)`. `^rewrite-rule/`</span><span class="sxs-lookup"><span data-stu-id="53f8c-244">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="53f8c-245">Značí shodu *s číslicí (číslem).* `\d`</span><span class="sxs-lookup"><span data-stu-id="53f8c-245">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="53f8c-246">Znaménko plus (`+`) znamená, že se *shoduje s jedním nebo více předcházejícím znakem*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-246">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="53f8c-247">Proto musí adresa URL obsahovat číslo následované lomítkem, za kterým následuje jiné číslo.</span><span class="sxs-lookup"><span data-stu-id="53f8c-247">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="53f8c-248">Tyto skupiny zachycení jsou vloženy do přepsané adresy URL jako `$1` a `$2`.</span><span class="sxs-lookup"><span data-stu-id="53f8c-248">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="53f8c-249">Náhradní řetězec pravidla přepsaní umístí zachycené skupiny do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-249">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="53f8c-250">Požadovaná cesta `/rewrite-rule/1234/5678` k je přepsána za účelem získání prostředku v `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="53f8c-250">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="53f8c-251">Pokud se v původním požadavku nachází řetězec dotazu, bude při přepisování adresy URL zachován.</span><span class="sxs-lookup"><span data-stu-id="53f8c-251">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="53f8c-252">K získání prostředku neexistuje žádná Přenosová cesta k serveru.</span><span class="sxs-lookup"><span data-stu-id="53f8c-252">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="53f8c-253">Pokud prostředek existuje, načte se a vrátí do klienta s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="53f8c-253">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="53f8c-254">Vzhledem k tomu, že klient není přesměrován, adresa URL v adresním řádku prohlížeče se nezmění.</span><span class="sxs-lookup"><span data-stu-id="53f8c-254">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="53f8c-255">Klienti nerozpoznají, že na serveru došlo k operaci přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="53f8c-255">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="53f8c-256">`skipRemainingRules: true` Kdykoli je to možné, protože vyhovující pravidla jsou výpočetně náročná a zvyšují dobu odezvy aplikace.</span><span class="sxs-lookup"><span data-stu-id="53f8c-256">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="53f8c-257">Pro nejrychlejší aplikační odpověď:</span><span class="sxs-lookup"><span data-stu-id="53f8c-257">For the fastest app response:</span></span>
>
> * <span data-ttu-id="53f8c-258">Seřazení pravidel přepisu z nejčastěji spárovaného pravidla na nejméně často spárované pravidlo.</span><span class="sxs-lookup"><span data-stu-id="53f8c-258">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="53f8c-259">Pokud dojde ke shodě a není vyžadováno žádné další zpracování pravidla, přeskočte zpracování zbývajících pravidel.</span><span class="sxs-lookup"><span data-stu-id="53f8c-259">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="53f8c-260">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="53f8c-260">Apache mod_rewrite</span></span>

<span data-ttu-id="53f8c-261">Použijte pravidla Apache mod_rewrite s <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>nástrojem.</span><span class="sxs-lookup"><span data-stu-id="53f8c-261">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="53f8c-262">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="53f8c-262">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="53f8c-263">Další informace a příklady pravidel mod_rewrite najdete v článku [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="53f8c-263">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="53f8c-264">Používá se ke čtení pravidel ze souboru pravidel *ApacheModRewrite. txt:* <xref:System.IO.StreamReader></span><span class="sxs-lookup"><span data-stu-id="53f8c-264">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="53f8c-265">Ukázková aplikace přesměrovává požadavky od `/apache-mod-rules-redirect/(.\*)` do. `/redirected?id=$1`</span><span class="sxs-lookup"><span data-stu-id="53f8c-265">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="53f8c-266">Stavový kód odpovědi je *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-266">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="53f8c-267">Původní požadavek:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="53f8c-267">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="53f8c-269">Middleware podporuje následující proměnné serveru Apache mod_rewrite:</span><span class="sxs-lookup"><span data-stu-id="53f8c-269">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="53f8c-270">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="53f8c-270">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="53f8c-271">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="53f8c-271">HTTP_ACCEPT</span></span>
* <span data-ttu-id="53f8c-272">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="53f8c-272">HTTP_CONNECTION</span></span>
* <span data-ttu-id="53f8c-273">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="53f8c-273">HTTP_COOKIE</span></span>
* <span data-ttu-id="53f8c-274">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="53f8c-274">HTTP_FORWARDED</span></span>
* <span data-ttu-id="53f8c-275">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="53f8c-275">HTTP_HOST</span></span>
* <span data-ttu-id="53f8c-276">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="53f8c-276">HTTP_REFERER</span></span>
* <span data-ttu-id="53f8c-277">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="53f8c-277">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="53f8c-278">HTTPS</span><span class="sxs-lookup"><span data-stu-id="53f8c-278">HTTPS</span></span>
* <span data-ttu-id="53f8c-279">IPV6</span><span class="sxs-lookup"><span data-stu-id="53f8c-279">IPV6</span></span>
* <span data-ttu-id="53f8c-280">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="53f8c-280">QUERY_STRING</span></span>
* <span data-ttu-id="53f8c-281">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="53f8c-281">REMOTE_ADDR</span></span>
* <span data-ttu-id="53f8c-282">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="53f8c-282">REMOTE_PORT</span></span>
* <span data-ttu-id="53f8c-283">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="53f8c-283">REQUEST_FILENAME</span></span>
* <span data-ttu-id="53f8c-284">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="53f8c-284">REQUEST_METHOD</span></span>
* <span data-ttu-id="53f8c-285">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="53f8c-285">REQUEST_SCHEME</span></span>
* <span data-ttu-id="53f8c-286">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="53f8c-286">REQUEST_URI</span></span>
* <span data-ttu-id="53f8c-287">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="53f8c-287">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="53f8c-288">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="53f8c-288">SERVER_ADDR</span></span>
* <span data-ttu-id="53f8c-289">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="53f8c-289">SERVER_PORT</span></span>
* <span data-ttu-id="53f8c-290">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="53f8c-290">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="53f8c-291">TIME</span><span class="sxs-lookup"><span data-stu-id="53f8c-291">TIME</span></span>
* <span data-ttu-id="53f8c-292">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="53f8c-292">TIME_DAY</span></span>
* <span data-ttu-id="53f8c-293">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="53f8c-293">TIME_HOUR</span></span>
* <span data-ttu-id="53f8c-294">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="53f8c-294">TIME_MIN</span></span>
* <span data-ttu-id="53f8c-295">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="53f8c-295">TIME_MON</span></span>
* <span data-ttu-id="53f8c-296">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="53f8c-296">TIME_SEC</span></span>
* <span data-ttu-id="53f8c-297">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="53f8c-297">TIME_WDAY</span></span>
* <span data-ttu-id="53f8c-298">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="53f8c-298">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="53f8c-299">Pravidla pro přepsání adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="53f8c-299">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="53f8c-300">Pokud chcete použít stejnou sadu pravidel, která se vztahuje na modul IIS URL Rewrite, <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>použijte.</span><span class="sxs-lookup"><span data-stu-id="53f8c-300">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="53f8c-301">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="53f8c-301">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="53f8c-302">Nesměrujte middleware na použití souboru *Web. config* aplikace při spuštění v systému Windows Server IIS.</span><span class="sxs-lookup"><span data-stu-id="53f8c-302">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="53f8c-303">V případě služby IIS by tato pravidla měla být uložena mimo soubor *Web. config* aplikace, aby nedocházelo ke konfliktům s modulem pro přepis služby IIS.</span><span class="sxs-lookup"><span data-stu-id="53f8c-303">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="53f8c-304">Další informace a příklady pravidel pro přepis adres URL služby IIS najdete v tématu použití odkazu na modul pro [přepis adres url 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [odkaz na konfiguraci modulu přepisu adresy](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)URL.</span><span class="sxs-lookup"><span data-stu-id="53f8c-304">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="53f8c-305">Používá se ke čtení pravidel ze souboru pravidel *IISUrlRewrite. XML:* <xref:System.IO.StreamReader></span><span class="sxs-lookup"><span data-stu-id="53f8c-305">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="53f8c-306">Ukázková aplikace přepíše požadavky od `/iis-rules-rewrite/(.*)` do. `/rewritten?id=$1`</span><span class="sxs-lookup"><span data-stu-id="53f8c-306">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="53f8c-307">Odpověď se pošle klientovi s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="53f8c-307">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="53f8c-308">Původní požadavek:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="53f8c-308">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="53f8c-310">Pokud máte aktivní modul pro přepis služby IIS s nakonfigurovanými pravidly na úrovni serveru, který by ovlivnil vaši aplikaci nežádoucím způsobem, můžete pro aplikaci zakázat modul IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="53f8c-310">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="53f8c-311">Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="53f8c-311">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="53f8c-312">Nepodporované funkce</span><span class="sxs-lookup"><span data-stu-id="53f8c-312">Unsupported features</span></span>

<span data-ttu-id="53f8c-313">Middleware vydaná ASP.NET Core 2. x nepodporuje následující funkce modulu pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="53f8c-313">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="53f8c-314">Odchozí pravidla</span><span class="sxs-lookup"><span data-stu-id="53f8c-314">Outbound Rules</span></span>
* <span data-ttu-id="53f8c-315">Vlastní proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="53f8c-315">Custom Server Variables</span></span>
* <span data-ttu-id="53f8c-316">Zástupné znaky</span><span class="sxs-lookup"><span data-stu-id="53f8c-316">Wildcards</span></span>
* <span data-ttu-id="53f8c-317">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="53f8c-317">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="53f8c-318">Podporované proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="53f8c-318">Supported server variables</span></span>

<span data-ttu-id="53f8c-319">Middleware podporuje následující proměnné serveru pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="53f8c-319">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="53f8c-320">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="53f8c-320">CONTENT_LENGTH</span></span>
* <span data-ttu-id="53f8c-321">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="53f8c-321">CONTENT_TYPE</span></span>
* <span data-ttu-id="53f8c-322">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="53f8c-322">HTTP_ACCEPT</span></span>
* <span data-ttu-id="53f8c-323">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="53f8c-323">HTTP_CONNECTION</span></span>
* <span data-ttu-id="53f8c-324">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="53f8c-324">HTTP_COOKIE</span></span>
* <span data-ttu-id="53f8c-325">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="53f8c-325">HTTP_HOST</span></span>
* <span data-ttu-id="53f8c-326">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="53f8c-326">HTTP_REFERER</span></span>
* <span data-ttu-id="53f8c-327">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-327">HTTP_URL</span></span>
* <span data-ttu-id="53f8c-328">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="53f8c-328">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="53f8c-329">HTTPS</span><span class="sxs-lookup"><span data-stu-id="53f8c-329">HTTPS</span></span>
* <span data-ttu-id="53f8c-330">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="53f8c-330">LOCAL_ADDR</span></span>
* <span data-ttu-id="53f8c-331">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="53f8c-331">QUERY_STRING</span></span>
* <span data-ttu-id="53f8c-332">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="53f8c-332">REMOTE_ADDR</span></span>
* <span data-ttu-id="53f8c-333">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="53f8c-333">REMOTE_PORT</span></span>
* <span data-ttu-id="53f8c-334">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="53f8c-334">REQUEST_FILENAME</span></span>
* <span data-ttu-id="53f8c-335">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="53f8c-335">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="53f8c-336">Můžete také získat <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>prostřednictvím.</span><span class="sxs-lookup"><span data-stu-id="53f8c-336">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="53f8c-337">Tento přístup může poskytovat větší flexibilitu pro umístění souborů pravidel přepisu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-337">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="53f8c-338">Ujistěte se, že jsou v cestě, kterou zadáte, nasazené soubory pravidel přepisu na server.</span><span class="sxs-lookup"><span data-stu-id="53f8c-338">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="53f8c-339">Pravidlo založené na metodách</span><span class="sxs-lookup"><span data-stu-id="53f8c-339">Method-based rule</span></span>

<span data-ttu-id="53f8c-340">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k implementaci logiky vlastního pravidla v metodě.</span><span class="sxs-lookup"><span data-stu-id="53f8c-340">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="53f8c-341">`Add`zpřístupňuje <xref:Microsoft.AspNetCore.Http.HttpContext> , který zpřístupňuje metodu pro použití v metodě. <xref:Microsoft.AspNetCore.Rewrite.RewriteContext></span><span class="sxs-lookup"><span data-stu-id="53f8c-341">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="53f8c-342">[RewriteContext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) určuje, jak se zpracovává další zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-342">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="53f8c-343">Nastavte hodnotu na jedno z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> polí popsaných v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="53f8c-343">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="53f8c-344">Akce</span><span class="sxs-lookup"><span data-stu-id="53f8c-344">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="53f8c-345">`RuleResult.ContinueRules`výchozí</span><span class="sxs-lookup"><span data-stu-id="53f8c-345">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="53f8c-346">Pokračovat v používání pravidel.</span><span class="sxs-lookup"><span data-stu-id="53f8c-346">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="53f8c-347">Zastavení použití pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="53f8c-347">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="53f8c-348">Zastavení použití pravidel a odeslání kontextu do dalšího middlewaru.</span><span class="sxs-lookup"><span data-stu-id="53f8c-348">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="53f8c-349">Ukázková aplikace ukazuje metodu, která přesměrovává požadavky na cesty, které končí na *. XML*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-349">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="53f8c-350">Pokud je žádost určena pro `/file.xml`, je požadavek přesměrován na. `/xmlfiles/file.xml`</span><span class="sxs-lookup"><span data-stu-id="53f8c-350">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="53f8c-351">Stavový kód je nastaven na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-351">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="53f8c-352">Když prohlížeč vytvoří nový požadavek na */xmlfiles/File.XML*, soubor middleware statických souborů zachová soubor klientovi ze složky *wwwroot/xmlfiles* .</span><span class="sxs-lookup"><span data-stu-id="53f8c-352">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="53f8c-353">Pro přesměrování explicitně nastavte stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="53f8c-353">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="53f8c-354">V opačném případě se vrátí stavový kód *200-OK* a na klientovi se neobjeví přesměrování.</span><span class="sxs-lookup"><span data-stu-id="53f8c-354">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="53f8c-355">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="53f8c-355">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="53f8c-356">Tento přístup může také přezapisovat požadavky.</span><span class="sxs-lookup"><span data-stu-id="53f8c-356">This approach can also rewrite requests.</span></span> <span data-ttu-id="53f8c-357">Ukázková aplikace ukazuje přepis cesty k libovolné žádosti o textový soubor, aby sloužil textový soubor *. txt* ze složky *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="53f8c-357">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="53f8c-358">Middleware statických souborů zachovává soubor na základě aktualizované cesty požadavku:</span><span class="sxs-lookup"><span data-stu-id="53f8c-358">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="53f8c-359">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="53f8c-359">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="53f8c-360">Pravidlo založené na IRule</span><span class="sxs-lookup"><span data-stu-id="53f8c-360">IRule-based rule</span></span>

<span data-ttu-id="53f8c-361">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k použití logiky pravidla ve třídě, která <xref:Microsoft.AspNetCore.Rewrite.IRule> implementuje rozhraní.</span><span class="sxs-lookup"><span data-stu-id="53f8c-361">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="53f8c-362">`IRule`poskytuje větší flexibilitu při použití pravidla založeného na metodách.</span><span class="sxs-lookup"><span data-stu-id="53f8c-362">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="53f8c-363">Vaše třída implementace může obsahovat konstruktor, který umožňuje předat parametry pro <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> metodu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-363">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="53f8c-364">Hodnoty parametrů v ukázkové aplikaci pro `extension` `newPath` a jsou zkontrolovány, aby splňovaly několik podmínek.</span><span class="sxs-lookup"><span data-stu-id="53f8c-364">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="53f8c-365">Musí obsahovat hodnotu a hodnota musí být *. png*, *. jpg*nebo *. gif.* `extension`</span><span class="sxs-lookup"><span data-stu-id="53f8c-365">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="53f8c-366">Pokud není platná <xref:System.ArgumentException> , je vyvolána výjimka. `newPath`</span><span class="sxs-lookup"><span data-stu-id="53f8c-366">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="53f8c-367">Pokud je pro *image. png*vytvořen požadavek, je požadavek přesměrován na `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="53f8c-367">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="53f8c-368">Pokud je pro *image. jpg*vytvořen požadavek, je požadavek přesměrován na `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="53f8c-368">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="53f8c-369">Stavový kód je nastaven na *301 – trvale přesunuto*a `context.Result` je nastavené na zastavení zpracování pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="53f8c-369">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="53f8c-370">Původní požadavek:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="53f8c-370">Original Request: `/image.png`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image. png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="53f8c-372">Původní požadavek:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="53f8c-372">Original Request: `/image.jpg`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image. jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="53f8c-374">Příklady Regex</span><span class="sxs-lookup"><span data-stu-id="53f8c-374">Regex examples</span></span>

| <span data-ttu-id="53f8c-375">Cíl</span><span class="sxs-lookup"><span data-stu-id="53f8c-375">Goal</span></span> | <span data-ttu-id="53f8c-376">& Řetězce regulárního výrazu</span><span class="sxs-lookup"><span data-stu-id="53f8c-376">Regex String &</span></span><br><span data-ttu-id="53f8c-377">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="53f8c-377">Match Example</span></span> | <span data-ttu-id="53f8c-378">& Řetězce nahrazení</span><span class="sxs-lookup"><span data-stu-id="53f8c-378">Replacement String &</span></span><br><span data-ttu-id="53f8c-379">Příklad výstupu</span><span class="sxs-lookup"><span data-stu-id="53f8c-379">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="53f8c-380">Přepište cestu do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-380">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="53f8c-381">Koncové lomítko pruhu</span><span class="sxs-lookup"><span data-stu-id="53f8c-381">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="53f8c-382">Vymáhat koncové lomítko</span><span class="sxs-lookup"><span data-stu-id="53f8c-382">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="53f8c-383">Vyhnout se přepisu konkrétních požadavků</span><span class="sxs-lookup"><span data-stu-id="53f8c-383">Avoid rewriting specific requests</span></span> | <span data-ttu-id="53f8c-384">`^(.*)(?<!\.axd)$` Nebo `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="53f8c-384">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="53f8c-385">Ano:`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="53f8c-385">Yes: `/resource.htm`</span></span><br><span data-ttu-id="53f8c-386">Ne:`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="53f8c-386">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="53f8c-387">Změna uspořádání segmentů adresy URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-387">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="53f8c-388">Nahradit segment adresy URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-388">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="53f8c-389">Tento dokument zavádí přepis adres URL s pokyny k použití middlewaru přepisu adresy URL v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="53f8c-389">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="53f8c-390">Přepsání adresy URL je to, že se jedná o úpravu adres URL žádostí na základě jednoho nebo více předdefinovaných pravidel.</span><span class="sxs-lookup"><span data-stu-id="53f8c-390">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="53f8c-391">Přepsání adresy URL vytvoří abstrakci mezi umístěními prostředků a jejich adresami tak, aby umístění a adresy nebyly pevně propojené.</span><span class="sxs-lookup"><span data-stu-id="53f8c-391">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="53f8c-392">Přepsání adresy URL je v několika scénářích užitečné pro:</span><span class="sxs-lookup"><span data-stu-id="53f8c-392">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="53f8c-393">Dočasné nebo trvalé přesunutí nebo nahrazení prostředků serveru a udržování stabilních lokátorů pro tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="53f8c-393">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="53f8c-394">Rozdělte zpracování požadavků napříč různými aplikacemi nebo napříč oblastmi jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="53f8c-394">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="53f8c-395">Umožňuje odebrat, přidat nebo změnit uspořádání segmentů adresy URL příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="53f8c-395">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="53f8c-396">Optimalizujte veřejné adresy URL pro optimalizaci vyhledávače (SEO).</span><span class="sxs-lookup"><span data-stu-id="53f8c-396">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="53f8c-397">Povolí použití přívětivých veřejných adres URL, které návštěvníkům pomůžou odhadnout obsah vrácený požadavkem na prostředek.</span><span class="sxs-lookup"><span data-stu-id="53f8c-397">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="53f8c-398">Přesměrovat nezabezpečené požadavky na zabezpečené koncové body.</span><span class="sxs-lookup"><span data-stu-id="53f8c-398">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="53f8c-399">Zabránit hotlinking, kde externí lokalita používá hostovaný statický prostředek na jiné lokalitě propojením prostředku s vlastním obsahem.</span><span class="sxs-lookup"><span data-stu-id="53f8c-399">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="53f8c-400">Přepsání adresy URL může snížit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="53f8c-400">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="53f8c-401">Pokud je to možné, omezte počet a složitost pravidel.</span><span class="sxs-lookup"><span data-stu-id="53f8c-401">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="53f8c-402">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="53f8c-402">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="53f8c-403">Přesměrování adresy URL a přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-403">URL redirect and URL rewrite</span></span>

<span data-ttu-id="53f8c-404">Rozdíl mezi přesměrováním *URL* a přepsáním *adresy URL* je malý, ale má důležitý dopad na poskytování prostředků klientům.</span><span class="sxs-lookup"><span data-stu-id="53f8c-404">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="53f8c-405">Rozhraní middleware pro přepis adres URL ASP.NET Core je schopné splnit nutnost obou.</span><span class="sxs-lookup"><span data-stu-id="53f8c-405">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="53f8c-406">*Přesměrování adresy URL* zahrnuje i operaci na straně klienta, kde klient má pokyn k přístupu k prostředku na jiné adrese, než jakou požadoval klient původně.</span><span class="sxs-lookup"><span data-stu-id="53f8c-406">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="53f8c-407">To vyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="53f8c-407">This requires a round trip to the server.</span></span> <span data-ttu-id="53f8c-408">Adresa URL pro přesměrování vracená klientovi se zobrazí v adresním řádku prohlížeče, když klient vytvoří nový požadavek na prostředek.</span><span class="sxs-lookup"><span data-stu-id="53f8c-408">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="53f8c-409">Pokud `/resource` je *přesměrován* na `/different-resource`, server odpoví, že by měl klient získat prostředek na `/different-resource` stavovém kódu, což značí, že přesměrování je buď dočasné, nebo trvalé.</span><span class="sxs-lookup"><span data-stu-id="53f8c-409">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Koncový bod služby WebAPI se dočasně změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="53f8c-415">Při přesměrování požadavků na jinou adresu URL určete, zda je přesměrování trvalé nebo dočasné, zadáním stavového kódu s odpovědí:</span><span class="sxs-lookup"><span data-stu-id="53f8c-415">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="53f8c-416">V případě, že má prostředek novou, trvalou adresu URL a chcete dát klientovi pokyn, aby všechny budoucí požadavky na prostředek používaly novou adresu URL, se použije kód *trvale přesunutý stav 301* .</span><span class="sxs-lookup"><span data-stu-id="53f8c-416">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="53f8c-417">*Klient může ukládat odpověď do mezipaměti a znovu ji použít, když se přijme stavový kód 301.*</span><span class="sxs-lookup"><span data-stu-id="53f8c-417">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="53f8c-418">V případě, že je přesměrování dočasné nebo obecně se může změnit, je použit stavový kód *302* .</span><span class="sxs-lookup"><span data-stu-id="53f8c-418">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="53f8c-419">Stavový kód 302 indikuje klientovi, aby neukládal adresu URL a použil ho v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-419">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="53f8c-420">Další informace o stavových kódech najdete v [dokumentu RFC 2616: Definice](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)stavových kódů.</span><span class="sxs-lookup"><span data-stu-id="53f8c-420">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="53f8c-421">*Přepsání adresy URL* je operace na straně serveru, která poskytuje prostředek z jiné adresy prostředku, než jakou požaduje klient.</span><span class="sxs-lookup"><span data-stu-id="53f8c-421">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="53f8c-422">Přepsání adresy URL nevyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="53f8c-422">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="53f8c-423">Přepsaná adresa URL se nevrátí klientovi a nezobrazí se v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="53f8c-423">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="53f8c-424">Pokud `/resource` je přepsáno `/different-resource`na, server *interně* načte a vrátí prostředek na `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="53f8c-424">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="53f8c-425">I když klient může být schopný načíst prostředek na adrese URL přepsané adresy, klient nebude informovat o tom, že prostředek existuje na přepsané adrese URL, když odešle svůj požadavek a obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="53f8c-425">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Koncový bod služby WebAPI se změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="53f8c-430">Ukázková aplikace pro přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-430">URL rewriting sample app</span></span>

<span data-ttu-id="53f8c-431">Pomocí [ukázkové aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)můžete prozkoumat funkce middlewaru přepsaného adresou URL.</span><span class="sxs-lookup"><span data-stu-id="53f8c-431">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="53f8c-432">Aplikace použije pravidla přesměrování a přepisu a zobrazí přesměrovanou nebo přepsanou adresu URL pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="53f8c-432">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="53f8c-433">Kdy použít middleware přepisu adresy URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-433">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="53f8c-434">Pokud nemůžete použít následující přístupy, použijte middleware přepisující adresu URL:</span><span class="sxs-lookup"><span data-stu-id="53f8c-434">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="53f8c-435">Modul pro přepsání adresy URL ve službě IIS v systému Windows Server</span><span class="sxs-lookup"><span data-stu-id="53f8c-435">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="53f8c-436">Modul Apache mod_rewrite na serveru Apache</span><span class="sxs-lookup"><span data-stu-id="53f8c-436">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="53f8c-437">Přepsání adresy URL na Nginx</span><span class="sxs-lookup"><span data-stu-id="53f8c-437">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="53f8c-438">Použijte taky middleware, pokud je aplikace hostována na [serveru http. sys](xref:fundamentals/servers/httpsys) (dříve nazývaném weblisten).</span><span class="sxs-lookup"><span data-stu-id="53f8c-438">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="53f8c-439">Hlavními důvody pro použití technologie přepisování adres URL založené na serveru ve službě IIS, Apache a Nginx jsou:</span><span class="sxs-lookup"><span data-stu-id="53f8c-439">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="53f8c-440">Middleware nepodporuje všechny funkce těchto modulů.</span><span class="sxs-lookup"><span data-stu-id="53f8c-440">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="53f8c-441">Některé funkce v modulech serveru nefungují s ASP.NET Core projekty, jako jsou `IsFile` omezení a `IsDirectory` modulu IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="53f8c-441">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="53f8c-442">V těchto scénářích místo toho použijte middleware.</span><span class="sxs-lookup"><span data-stu-id="53f8c-442">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="53f8c-443">Výkon middleware se pravděpodobně neshoduje s modulem.</span><span class="sxs-lookup"><span data-stu-id="53f8c-443">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="53f8c-444">Srovnávací testy jsou jediným způsobem, jak zjistit, který přístup snižuje výkon, který je v nejvyšší nebo v případě, že je snížený výkon.</span><span class="sxs-lookup"><span data-stu-id="53f8c-444">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="53f8c-445">Balíček</span><span class="sxs-lookup"><span data-stu-id="53f8c-445">Package</span></span>

<span data-ttu-id="53f8c-446">Chcete-li do projektu zahrnout middleware, přidejte odkaz na balíček do souboru [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) v souboru projektu, který obsahuje balíček [Microsoft. AspNetCore. Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) .</span><span class="sxs-lookup"><span data-stu-id="53f8c-446">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="53f8c-447">Pokud nepoužíváte `Microsoft.AspNetCore.App` Metapackage, přidejte `Microsoft.AspNetCore.Rewrite` do balíčku odkaz na projekt.</span><span class="sxs-lookup"><span data-stu-id="53f8c-447">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="53f8c-448">Rozšíření a možnosti</span><span class="sxs-lookup"><span data-stu-id="53f8c-448">Extension and options</span></span>

<span data-ttu-id="53f8c-449">Vytvořte pravidla přepsání a přesměrování adresy URL vytvořením instance třídy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) s metodami rozšíření pro každé pravidlo přepsání.</span><span class="sxs-lookup"><span data-stu-id="53f8c-449">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="53f8c-450">Řetězení více pravidel v pořadí, ve kterém se mají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="53f8c-450">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="53f8c-451">Rozhraní `RewriteOptions` se předává do middleware přepisování adresy URL při jeho přidání do kanálu požadavků pomocí <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span><span class="sxs-lookup"><span data-stu-id="53f8c-451">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="53f8c-452">Přesměrovat neexistující na webovou</span><span class="sxs-lookup"><span data-stu-id="53f8c-452">Redirect non-www to www</span></span>

<span data-ttu-id="53f8c-453">Tři možnosti umožňují, aby aplikace přesměrovala`www` požadavky bez `www`požadavků na:</span><span class="sxs-lookup"><span data-stu-id="53f8c-453">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="53f8c-454"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>Pokud je žádost jiná než`www`, `www` trvale přesměrujte požadavek na subdoménu. &ndash;</span><span class="sxs-lookup"><span data-stu-id="53f8c-454"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="53f8c-455">Přesměruje stavový kód [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) .</span><span class="sxs-lookup"><span data-stu-id="53f8c-455">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="53f8c-456"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>Přesměruje požadavek `www` do subdomény, pokud je`www`příchozí požadavek jiný než. &ndash;</span><span class="sxs-lookup"><span data-stu-id="53f8c-456"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="53f8c-457">Přesměruje stavový kód [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) .</span><span class="sxs-lookup"><span data-stu-id="53f8c-457">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="53f8c-458">Přetížení umožňuje poskytnout stavový kód pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="53f8c-458">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="53f8c-459">Použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy pro přiřazení stavového kódu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-459">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="53f8c-460">Přesměrování adresy URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-460">URL redirect</span></span>

<span data-ttu-id="53f8c-461">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> k přesměrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="53f8c-461">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="53f8c-462">První parametr obsahuje váš regulární výraz pro porovnání s cestou příchozí adresy URL.</span><span class="sxs-lookup"><span data-stu-id="53f8c-462">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="53f8c-463">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="53f8c-463">The second parameter is the replacement string.</span></span> <span data-ttu-id="53f8c-464">Třetí parametr, pokud je k dispozici, určuje stavový kód.</span><span class="sxs-lookup"><span data-stu-id="53f8c-464">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="53f8c-465">Pokud nezadáte stavový kód, stavový kód se standardně *302 – Nalezeno*, což znamená, že prostředek je dočasně přesunut nebo nahrazen.</span><span class="sxs-lookup"><span data-stu-id="53f8c-465">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="53f8c-466">V prohlížeči s povolenými vývojářskými nástroji vytvořte žádost o ukázkovou aplikaci s cestou `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="53f8c-466">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="53f8c-467">Regulární výraz odpovídá cestě požadavku na `redirect-rule/(.*)`a cesta je nahrazena řetězcem. `/redirected/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="53f8c-467">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="53f8c-468">Adresa URL pro přesměrování se pošle zpátky do klienta s kódem stavu 302, který se *našel* .</span><span class="sxs-lookup"><span data-stu-id="53f8c-468">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="53f8c-469">Prohlížeč vytvoří novou žádost na adrese URL pro přesměrování, která se zobrazí v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="53f8c-469">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="53f8c-470">Vzhledem k tomu, že se žádná pravidla v ukázkové aplikaci neshodují s adresou URL pro přesměrování:</span><span class="sxs-lookup"><span data-stu-id="53f8c-470">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="53f8c-471">Druhá žádost obdrží odpověď *200-OK* od aplikace.</span><span class="sxs-lookup"><span data-stu-id="53f8c-471">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="53f8c-472">Tělo odpovědi zobrazuje adresu URL pro přesměrování.</span><span class="sxs-lookup"><span data-stu-id="53f8c-472">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="53f8c-473">Při *přesměrování*adresy URL se na Server provede cyklická výměna.</span><span class="sxs-lookup"><span data-stu-id="53f8c-473">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="53f8c-474">Buďte opatrní při vytváření pravidel přesměrování.</span><span class="sxs-lookup"><span data-stu-id="53f8c-474">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="53f8c-475">Pravidla přesměrování se vyhodnocují při každém požadavku na aplikaci, včetně po přesměrování.</span><span class="sxs-lookup"><span data-stu-id="53f8c-475">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="53f8c-476">Je snadné vytvořit smyčku nekonečných *přesměrování*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-476">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="53f8c-477">Původní požadavek:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="53f8c-477">Original Request: `/redirect-rule/1234/5678`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="53f8c-479">Část výrazu obsažená v závorkách se nazývá *Skupina zachycení*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-479">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="53f8c-480">Tečka (`.`) výrazu znamená, že *odpovídá libovolnému znaku*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-480">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="53f8c-481">Hvězdička (`*`) značí, *že se předchozí znak rovná nule nebo vícekrát*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-481">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="53f8c-482">Proto jsou poslední dva segmenty cesty adresy URL `1234/5678`zachyceny skupinou `(.*)`zachycení.</span><span class="sxs-lookup"><span data-stu-id="53f8c-482">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="53f8c-483">Jakákoli hodnota, kterou zadáte v adrese URL `redirect-rule/` požadavku, potom, co je tato jediná skupina zachycení zachycena.</span><span class="sxs-lookup"><span data-stu-id="53f8c-483">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="53f8c-484">V řetězci pro nahrazení jsou zachycené skupiny vloženy do řetězce s znakem dolaru (`$`) následovaným pořadovým číslem záznamu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-484">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="53f8c-485">První hodnota skupiny zachycení je získána s `$1`, druhým s `$2`a pokračuje v pořadí pro skupiny zachycení ve vašem regulárním výrazu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-485">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="53f8c-486">Regulární výraz pravidla přesměrování v ukázkové aplikaci obsahuje jenom jednu zachycenou skupinu, takže v náhradním řetězci je jenom jedna vložená skupina, která je `$1`.</span><span class="sxs-lookup"><span data-stu-id="53f8c-486">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="53f8c-487">Po použití pravidla se adresa URL bude `/redirected/1234/5678`nacházet.</span><span class="sxs-lookup"><span data-stu-id="53f8c-487">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="53f8c-488">Přesměrování adresy URL na zabezpečený koncový bod</span><span class="sxs-lookup"><span data-stu-id="53f8c-488">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="53f8c-489">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> k přesměrování požadavků HTTP na stejného hostitele a cestu pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="53f8c-489">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="53f8c-490">Pokud kód stavu není zadaný, použije se ve výchozím nastavení middleware *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-490">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="53f8c-491">Pokud port není dodán:</span><span class="sxs-lookup"><span data-stu-id="53f8c-491">If the port isn't supplied:</span></span>

* <span data-ttu-id="53f8c-492">Ve výchozím nastavení `null`se používá middleware.</span><span class="sxs-lookup"><span data-stu-id="53f8c-492">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="53f8c-493">Schéma se změní na `https` (protokol HTTPS) a klient přistupuje k prostředku na portu 443.</span><span class="sxs-lookup"><span data-stu-id="53f8c-493">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="53f8c-494">Následující příklad ukazuje, jak nastavit stavový kód na *301 – trvale přesunuto* a změnit port na 5001.</span><span class="sxs-lookup"><span data-stu-id="53f8c-494">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="53f8c-495">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> k přesměrování nezabezpečených požadavků na stejného hostitele a cestu s protokolem Secure https na portu 443.</span><span class="sxs-lookup"><span data-stu-id="53f8c-495">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="53f8c-496">Middleware nastaví stavový kód na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-496">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="53f8c-497">Při přesměrování na zabezpečený koncový bod bez požadavku na další pravidla přesměrování doporučujeme použít middleware pro přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="53f8c-497">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="53f8c-498">Další informace najdete v tématu věnovaném vykonání [protokolu HTTPS](xref:security/enforcing-ssl#require-https) .</span><span class="sxs-lookup"><span data-stu-id="53f8c-498">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="53f8c-499">Ukázková aplikace dokáže demonstrovat, jak používat `AddRedirectToHttps` nebo. `AddRedirectToHttpsPermanent`</span><span class="sxs-lookup"><span data-stu-id="53f8c-499">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="53f8c-500">Přidejte metodu rozšíření do `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="53f8c-500">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="53f8c-501">Vytvořte nezabezpečenou žádost o aplikaci na libovolné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="53f8c-501">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="53f8c-502">Zrušte upozornění zabezpečení prohlížeče, že certifikát podepsaný svým držitelem je nedůvěryhodný, nebo vytvořte výjimku pro důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-502">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="53f8c-503">Původní požadavek s `AddRedirectToHttps(301, 5001)`použitím:`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="53f8c-503">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="53f8c-505">Původní požadavek s `AddRedirectToHttpsPermanent`použitím:`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="53f8c-505">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="53f8c-507">Přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-507">URL rewrite</span></span>

<span data-ttu-id="53f8c-508">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> k vytvoření pravidla pro přepis adres URL.</span><span class="sxs-lookup"><span data-stu-id="53f8c-508">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="53f8c-509">První parametr obsahuje regulární výraz pro porovnání na cestě příchozích adres URL.</span><span class="sxs-lookup"><span data-stu-id="53f8c-509">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="53f8c-510">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="53f8c-510">The second parameter is the replacement string.</span></span> <span data-ttu-id="53f8c-511">Třetí parametr, `skipRemainingRules: {true|false}`označuje middleware bez ohledu na to, zda má přeskočit další pravidla přepsání, pokud je použito aktuální pravidlo.</span><span class="sxs-lookup"><span data-stu-id="53f8c-511">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="53f8c-512">Původní požadavek:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="53f8c-512">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="53f8c-514">Kosočtverce (`^`) na začátku výrazu znamená, že shoda začíná na začátku cesty URL.</span><span class="sxs-lookup"><span data-stu-id="53f8c-514">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="53f8c-515">V předchozím příkladu s pravidlem `redirect-rule/(.*)`přesměrování není na začátku regulárního výrazu k dispozici žádná kosočtverce (`^`).</span><span class="sxs-lookup"><span data-stu-id="53f8c-515">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="53f8c-516">Proto jakékoli znaky mohou předcházet `redirect-rule/` v cestě k úspěšné shodě.</span><span class="sxs-lookup"><span data-stu-id="53f8c-516">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="53f8c-517">Cesta</span><span class="sxs-lookup"><span data-stu-id="53f8c-517">Path</span></span>                               | <span data-ttu-id="53f8c-518">Shoda</span><span class="sxs-lookup"><span data-stu-id="53f8c-518">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="53f8c-519">Ano</span><span class="sxs-lookup"><span data-stu-id="53f8c-519">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="53f8c-520">Ano</span><span class="sxs-lookup"><span data-stu-id="53f8c-520">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="53f8c-521">Ano</span><span class="sxs-lookup"><span data-stu-id="53f8c-521">Yes</span></span>   |

<span data-ttu-id="53f8c-522">Pravidlo `^rewrite-rule/(\d+)/(\d+)`přepsaného zápisu odpovídá pouze cestám, pokud `rewrite-rule/`začínají na.</span><span class="sxs-lookup"><span data-stu-id="53f8c-522">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="53f8c-523">V následující tabulce si všimněte rozdílu v porovnání.</span><span class="sxs-lookup"><span data-stu-id="53f8c-523">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="53f8c-524">Cesta</span><span class="sxs-lookup"><span data-stu-id="53f8c-524">Path</span></span>                              | <span data-ttu-id="53f8c-525">Shoda</span><span class="sxs-lookup"><span data-stu-id="53f8c-525">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="53f8c-526">Ano</span><span class="sxs-lookup"><span data-stu-id="53f8c-526">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="53f8c-527">Ne</span><span class="sxs-lookup"><span data-stu-id="53f8c-527">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="53f8c-528">Ne</span><span class="sxs-lookup"><span data-stu-id="53f8c-528">No</span></span>    |

<span data-ttu-id="53f8c-529">Po části výrazu jsou k dispozici dvě skupiny zachycení, `(\d+)/(\d+)`. `^rewrite-rule/`</span><span class="sxs-lookup"><span data-stu-id="53f8c-529">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="53f8c-530">Značí shodu *s číslicí (číslem).* `\d`</span><span class="sxs-lookup"><span data-stu-id="53f8c-530">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="53f8c-531">Znaménko plus (`+`) znamená, že se *shoduje s jedním nebo více předcházejícím znakem*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-531">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="53f8c-532">Proto musí adresa URL obsahovat číslo následované lomítkem, za kterým následuje jiné číslo.</span><span class="sxs-lookup"><span data-stu-id="53f8c-532">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="53f8c-533">Tyto skupiny zachycení jsou vloženy do přepsané adresy URL jako `$1` a `$2`.</span><span class="sxs-lookup"><span data-stu-id="53f8c-533">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="53f8c-534">Náhradní řetězec pravidla přepsaní umístí zachycené skupiny do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-534">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="53f8c-535">Požadovaná cesta `/rewrite-rule/1234/5678` k je přepsána za účelem získání prostředku v `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="53f8c-535">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="53f8c-536">Pokud se v původním požadavku nachází řetězec dotazu, bude při přepisování adresy URL zachován.</span><span class="sxs-lookup"><span data-stu-id="53f8c-536">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="53f8c-537">K získání prostředku neexistuje žádná Přenosová cesta k serveru.</span><span class="sxs-lookup"><span data-stu-id="53f8c-537">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="53f8c-538">Pokud prostředek existuje, načte se a vrátí do klienta s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="53f8c-538">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="53f8c-539">Vzhledem k tomu, že klient není přesměrován, adresa URL v adresním řádku prohlížeče se nezmění.</span><span class="sxs-lookup"><span data-stu-id="53f8c-539">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="53f8c-540">Klienti nerozpoznají, že na serveru došlo k operaci přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="53f8c-540">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="53f8c-541">`skipRemainingRules: true` Kdykoli je to možné, protože vyhovující pravidla jsou výpočetně náročná a zvyšují dobu odezvy aplikace.</span><span class="sxs-lookup"><span data-stu-id="53f8c-541">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="53f8c-542">Pro nejrychlejší aplikační odpověď:</span><span class="sxs-lookup"><span data-stu-id="53f8c-542">For the fastest app response:</span></span>
>
> * <span data-ttu-id="53f8c-543">Seřazení pravidel přepisu z nejčastěji spárovaného pravidla na nejméně často spárované pravidlo.</span><span class="sxs-lookup"><span data-stu-id="53f8c-543">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="53f8c-544">Pokud dojde ke shodě a není vyžadováno žádné další zpracování pravidla, přeskočte zpracování zbývajících pravidel.</span><span class="sxs-lookup"><span data-stu-id="53f8c-544">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="53f8c-545">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="53f8c-545">Apache mod_rewrite</span></span>

<span data-ttu-id="53f8c-546">Použijte pravidla Apache mod_rewrite s <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>nástrojem.</span><span class="sxs-lookup"><span data-stu-id="53f8c-546">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="53f8c-547">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="53f8c-547">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="53f8c-548">Další informace a příklady pravidel mod_rewrite najdete v článku [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="53f8c-548">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="53f8c-549">Používá se ke čtení pravidel ze souboru pravidel *ApacheModRewrite. txt:* <xref:System.IO.StreamReader></span><span class="sxs-lookup"><span data-stu-id="53f8c-549">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="53f8c-550">Ukázková aplikace přesměrovává požadavky od `/apache-mod-rules-redirect/(.\*)` do. `/redirected?id=$1`</span><span class="sxs-lookup"><span data-stu-id="53f8c-550">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="53f8c-551">Stavový kód odpovědi je *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-551">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="53f8c-552">Původní požadavek:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="53f8c-552">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="53f8c-554">Middleware podporuje následující proměnné serveru Apache mod_rewrite:</span><span class="sxs-lookup"><span data-stu-id="53f8c-554">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="53f8c-555">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="53f8c-555">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="53f8c-556">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="53f8c-556">HTTP_ACCEPT</span></span>
* <span data-ttu-id="53f8c-557">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="53f8c-557">HTTP_CONNECTION</span></span>
* <span data-ttu-id="53f8c-558">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="53f8c-558">HTTP_COOKIE</span></span>
* <span data-ttu-id="53f8c-559">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="53f8c-559">HTTP_FORWARDED</span></span>
* <span data-ttu-id="53f8c-560">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="53f8c-560">HTTP_HOST</span></span>
* <span data-ttu-id="53f8c-561">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="53f8c-561">HTTP_REFERER</span></span>
* <span data-ttu-id="53f8c-562">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="53f8c-562">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="53f8c-563">HTTPS</span><span class="sxs-lookup"><span data-stu-id="53f8c-563">HTTPS</span></span>
* <span data-ttu-id="53f8c-564">IPV6</span><span class="sxs-lookup"><span data-stu-id="53f8c-564">IPV6</span></span>
* <span data-ttu-id="53f8c-565">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="53f8c-565">QUERY_STRING</span></span>
* <span data-ttu-id="53f8c-566">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="53f8c-566">REMOTE_ADDR</span></span>
* <span data-ttu-id="53f8c-567">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="53f8c-567">REMOTE_PORT</span></span>
* <span data-ttu-id="53f8c-568">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="53f8c-568">REQUEST_FILENAME</span></span>
* <span data-ttu-id="53f8c-569">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="53f8c-569">REQUEST_METHOD</span></span>
* <span data-ttu-id="53f8c-570">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="53f8c-570">REQUEST_SCHEME</span></span>
* <span data-ttu-id="53f8c-571">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="53f8c-571">REQUEST_URI</span></span>
* <span data-ttu-id="53f8c-572">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="53f8c-572">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="53f8c-573">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="53f8c-573">SERVER_ADDR</span></span>
* <span data-ttu-id="53f8c-574">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="53f8c-574">SERVER_PORT</span></span>
* <span data-ttu-id="53f8c-575">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="53f8c-575">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="53f8c-576">TIME</span><span class="sxs-lookup"><span data-stu-id="53f8c-576">TIME</span></span>
* <span data-ttu-id="53f8c-577">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="53f8c-577">TIME_DAY</span></span>
* <span data-ttu-id="53f8c-578">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="53f8c-578">TIME_HOUR</span></span>
* <span data-ttu-id="53f8c-579">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="53f8c-579">TIME_MIN</span></span>
* <span data-ttu-id="53f8c-580">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="53f8c-580">TIME_MON</span></span>
* <span data-ttu-id="53f8c-581">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="53f8c-581">TIME_SEC</span></span>
* <span data-ttu-id="53f8c-582">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="53f8c-582">TIME_WDAY</span></span>
* <span data-ttu-id="53f8c-583">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="53f8c-583">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="53f8c-584">Pravidla pro přepsání adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="53f8c-584">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="53f8c-585">Pokud chcete použít stejnou sadu pravidel, která se vztahuje na modul IIS URL Rewrite, <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>použijte.</span><span class="sxs-lookup"><span data-stu-id="53f8c-585">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="53f8c-586">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="53f8c-586">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="53f8c-587">Nesměrujte middleware na použití souboru *Web. config* aplikace při spuštění v systému Windows Server IIS.</span><span class="sxs-lookup"><span data-stu-id="53f8c-587">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="53f8c-588">V případě služby IIS by tato pravidla měla být uložena mimo soubor *Web. config* aplikace, aby nedocházelo ke konfliktům s modulem pro přepis služby IIS.</span><span class="sxs-lookup"><span data-stu-id="53f8c-588">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="53f8c-589">Další informace a příklady pravidel pro přepis adres URL služby IIS najdete v tématu použití odkazu na modul pro [přepis adres url 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [odkaz na konfiguraci modulu přepisu adresy](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)URL.</span><span class="sxs-lookup"><span data-stu-id="53f8c-589">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="53f8c-590">Používá se ke čtení pravidel ze souboru pravidel *IISUrlRewrite. XML:* <xref:System.IO.StreamReader></span><span class="sxs-lookup"><span data-stu-id="53f8c-590">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="53f8c-591">Ukázková aplikace přepíše požadavky od `/iis-rules-rewrite/(.*)` do. `/rewritten?id=$1`</span><span class="sxs-lookup"><span data-stu-id="53f8c-591">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="53f8c-592">Odpověď se pošle klientovi s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="53f8c-592">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="53f8c-593">Původní požadavek:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="53f8c-593">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="53f8c-595">Pokud máte aktivní modul pro přepis služby IIS s nakonfigurovanými pravidly na úrovni serveru, který by ovlivnil vaši aplikaci nežádoucím způsobem, můžete pro aplikaci zakázat modul IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="53f8c-595">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="53f8c-596">Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="53f8c-596">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="53f8c-597">Nepodporované funkce</span><span class="sxs-lookup"><span data-stu-id="53f8c-597">Unsupported features</span></span>

<span data-ttu-id="53f8c-598">Middleware vydaná ASP.NET Core 2. x nepodporuje následující funkce modulu pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="53f8c-598">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="53f8c-599">Odchozí pravidla</span><span class="sxs-lookup"><span data-stu-id="53f8c-599">Outbound Rules</span></span>
* <span data-ttu-id="53f8c-600">Vlastní proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="53f8c-600">Custom Server Variables</span></span>
* <span data-ttu-id="53f8c-601">Zástupné znaky</span><span class="sxs-lookup"><span data-stu-id="53f8c-601">Wildcards</span></span>
* <span data-ttu-id="53f8c-602">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="53f8c-602">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="53f8c-603">Podporované proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="53f8c-603">Supported server variables</span></span>

<span data-ttu-id="53f8c-604">Middleware podporuje následující proměnné serveru pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="53f8c-604">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="53f8c-605">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="53f8c-605">CONTENT_LENGTH</span></span>
* <span data-ttu-id="53f8c-606">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="53f8c-606">CONTENT_TYPE</span></span>
* <span data-ttu-id="53f8c-607">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="53f8c-607">HTTP_ACCEPT</span></span>
* <span data-ttu-id="53f8c-608">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="53f8c-608">HTTP_CONNECTION</span></span>
* <span data-ttu-id="53f8c-609">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="53f8c-609">HTTP_COOKIE</span></span>
* <span data-ttu-id="53f8c-610">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="53f8c-610">HTTP_HOST</span></span>
* <span data-ttu-id="53f8c-611">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="53f8c-611">HTTP_REFERER</span></span>
* <span data-ttu-id="53f8c-612">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-612">HTTP_URL</span></span>
* <span data-ttu-id="53f8c-613">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="53f8c-613">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="53f8c-614">HTTPS</span><span class="sxs-lookup"><span data-stu-id="53f8c-614">HTTPS</span></span>
* <span data-ttu-id="53f8c-615">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="53f8c-615">LOCAL_ADDR</span></span>
* <span data-ttu-id="53f8c-616">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="53f8c-616">QUERY_STRING</span></span>
* <span data-ttu-id="53f8c-617">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="53f8c-617">REMOTE_ADDR</span></span>
* <span data-ttu-id="53f8c-618">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="53f8c-618">REMOTE_PORT</span></span>
* <span data-ttu-id="53f8c-619">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="53f8c-619">REQUEST_FILENAME</span></span>
* <span data-ttu-id="53f8c-620">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="53f8c-620">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="53f8c-621">Můžete také získat <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>prostřednictvím.</span><span class="sxs-lookup"><span data-stu-id="53f8c-621">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="53f8c-622">Tento přístup může poskytovat větší flexibilitu pro umístění souborů pravidel přepisu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-622">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="53f8c-623">Ujistěte se, že jsou v cestě, kterou zadáte, nasazené soubory pravidel přepisu na server.</span><span class="sxs-lookup"><span data-stu-id="53f8c-623">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="53f8c-624">Pravidlo založené na metodách</span><span class="sxs-lookup"><span data-stu-id="53f8c-624">Method-based rule</span></span>

<span data-ttu-id="53f8c-625">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k implementaci logiky vlastního pravidla v metodě.</span><span class="sxs-lookup"><span data-stu-id="53f8c-625">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="53f8c-626">`Add`zpřístupňuje <xref:Microsoft.AspNetCore.Http.HttpContext> , který zpřístupňuje metodu pro použití v metodě. <xref:Microsoft.AspNetCore.Rewrite.RewriteContext></span><span class="sxs-lookup"><span data-stu-id="53f8c-626">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="53f8c-627">[RewriteContext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) určuje, jak se zpracovává další zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-627">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="53f8c-628">Nastavte hodnotu na jedno z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> polí popsaných v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="53f8c-628">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="53f8c-629">Akce</span><span class="sxs-lookup"><span data-stu-id="53f8c-629">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="53f8c-630">`RuleResult.ContinueRules`výchozí</span><span class="sxs-lookup"><span data-stu-id="53f8c-630">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="53f8c-631">Pokračovat v používání pravidel.</span><span class="sxs-lookup"><span data-stu-id="53f8c-631">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="53f8c-632">Zastavení použití pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="53f8c-632">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="53f8c-633">Zastavení použití pravidel a odeslání kontextu do dalšího middlewaru.</span><span class="sxs-lookup"><span data-stu-id="53f8c-633">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="53f8c-634">Ukázková aplikace ukazuje metodu, která přesměrovává požadavky na cesty, které končí na *. XML*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-634">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="53f8c-635">Pokud je žádost určena pro `/file.xml`, je požadavek přesměrován na. `/xmlfiles/file.xml`</span><span class="sxs-lookup"><span data-stu-id="53f8c-635">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="53f8c-636">Stavový kód je nastaven na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="53f8c-636">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="53f8c-637">Když prohlížeč vytvoří nový požadavek na */xmlfiles/File.XML*, soubor middleware statických souborů zachová soubor klientovi ze složky *wwwroot/xmlfiles* .</span><span class="sxs-lookup"><span data-stu-id="53f8c-637">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="53f8c-638">Pro přesměrování explicitně nastavte stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="53f8c-638">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="53f8c-639">V opačném případě se vrátí stavový kód *200-OK* a na klientovi se neobjeví přesměrování.</span><span class="sxs-lookup"><span data-stu-id="53f8c-639">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="53f8c-640">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="53f8c-640">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="53f8c-641">Tento přístup může také přezapisovat požadavky.</span><span class="sxs-lookup"><span data-stu-id="53f8c-641">This approach can also rewrite requests.</span></span> <span data-ttu-id="53f8c-642">Ukázková aplikace ukazuje přepis cesty k libovolné žádosti o textový soubor, aby sloužil textový soubor *. txt* ze složky *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="53f8c-642">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="53f8c-643">Middleware statických souborů zachovává soubor na základě aktualizované cesty požadavku:</span><span class="sxs-lookup"><span data-stu-id="53f8c-643">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="53f8c-644">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="53f8c-644">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="53f8c-645">Pravidlo založené na IRule</span><span class="sxs-lookup"><span data-stu-id="53f8c-645">IRule-based rule</span></span>

<span data-ttu-id="53f8c-646">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k použití logiky pravidla ve třídě, která <xref:Microsoft.AspNetCore.Rewrite.IRule> implementuje rozhraní.</span><span class="sxs-lookup"><span data-stu-id="53f8c-646">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="53f8c-647">`IRule`poskytuje větší flexibilitu při použití pravidla založeného na metodách.</span><span class="sxs-lookup"><span data-stu-id="53f8c-647">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="53f8c-648">Vaše třída implementace může obsahovat konstruktor, který umožňuje předat parametry pro <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> metodu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-648">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="53f8c-649">Hodnoty parametrů v ukázkové aplikaci pro `extension` `newPath` a jsou zkontrolovány, aby splňovaly několik podmínek.</span><span class="sxs-lookup"><span data-stu-id="53f8c-649">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="53f8c-650">Musí obsahovat hodnotu a hodnota musí být *. png*, *. jpg*nebo *. gif.* `extension`</span><span class="sxs-lookup"><span data-stu-id="53f8c-650">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="53f8c-651">Pokud není platná <xref:System.ArgumentException> , je vyvolána výjimka. `newPath`</span><span class="sxs-lookup"><span data-stu-id="53f8c-651">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="53f8c-652">Pokud je pro *image. png*vytvořen požadavek, je požadavek přesměrován na `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="53f8c-652">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="53f8c-653">Pokud je pro *image. jpg*vytvořen požadavek, je požadavek přesměrován na `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="53f8c-653">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="53f8c-654">Stavový kód je nastaven na *301 – trvale přesunuto*a `context.Result` je nastavené na zastavení zpracování pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="53f8c-654">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="53f8c-655">Původní požadavek:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="53f8c-655">Original Request: `/image.png`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image. png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="53f8c-657">Původní požadavek:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="53f8c-657">Original Request: `/image.jpg`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image. jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="53f8c-659">Příklady Regex</span><span class="sxs-lookup"><span data-stu-id="53f8c-659">Regex examples</span></span>

| <span data-ttu-id="53f8c-660">Cíl</span><span class="sxs-lookup"><span data-stu-id="53f8c-660">Goal</span></span> | <span data-ttu-id="53f8c-661">& Řetězce regulárního výrazu</span><span class="sxs-lookup"><span data-stu-id="53f8c-661">Regex String &</span></span><br><span data-ttu-id="53f8c-662">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="53f8c-662">Match Example</span></span> | <span data-ttu-id="53f8c-663">& Řetězce nahrazení</span><span class="sxs-lookup"><span data-stu-id="53f8c-663">Replacement String &</span></span><br><span data-ttu-id="53f8c-664">Příklad výstupu</span><span class="sxs-lookup"><span data-stu-id="53f8c-664">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="53f8c-665">Přepište cestu do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="53f8c-665">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="53f8c-666">Koncové lomítko pruhu</span><span class="sxs-lookup"><span data-stu-id="53f8c-666">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="53f8c-667">Vymáhat koncové lomítko</span><span class="sxs-lookup"><span data-stu-id="53f8c-667">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="53f8c-668">Vyhnout se přepisu konkrétních požadavků</span><span class="sxs-lookup"><span data-stu-id="53f8c-668">Avoid rewriting specific requests</span></span> | <span data-ttu-id="53f8c-669">`^(.*)(?<!\.axd)$` Nebo `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="53f8c-669">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="53f8c-670">Ano:`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="53f8c-670">Yes: `/resource.htm`</span></span><br><span data-ttu-id="53f8c-671">Ne:`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="53f8c-671">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="53f8c-672">Změna uspořádání segmentů adresy URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-672">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="53f8c-673">Nahradit segment adresy URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-673">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="53f8c-674">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="53f8c-674">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="53f8c-675">Regulární výrazy v .NET</span><span class="sxs-lookup"><span data-stu-id="53f8c-675">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="53f8c-676">Jazyk regulárních výrazů – stručná referenční dokumentace</span><span class="sxs-lookup"><span data-stu-id="53f8c-676">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="53f8c-677">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="53f8c-677">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="53f8c-678">Použití modulu URL pro přepis 2,0 (pro IIS)</span><span class="sxs-lookup"><span data-stu-id="53f8c-678">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="53f8c-679">Odkaz na konfiguraci modulu pro přepis adresy URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-679">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="53f8c-680">Fórum modulu pro přepsání adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="53f8c-680">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="53f8c-681">Zachování jednoduché struktury URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-681">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="53f8c-682">10 tipů a triky pro přepis adres URL</span><span class="sxs-lookup"><span data-stu-id="53f8c-682">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="53f8c-683">Na lomítko nebo ne na lomítko</span><span class="sxs-lookup"><span data-stu-id="53f8c-683">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
