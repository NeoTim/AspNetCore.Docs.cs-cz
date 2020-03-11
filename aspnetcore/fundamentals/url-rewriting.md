---
title: Middleware pro přepis adres URL v ASP.NET Core
author: rick-anderson
description: Přečtěte si informace o přepsání adresy URL a přesměrování pomocí middlewaru pro přepis adres URL v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
uid: fundamentals/url-rewriting
ms.openlocfilehash: 7d63cf381f1d8a19ed4fb789348e36f94304ad63
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666465"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="a2283-103">Middleware pro přepis adres URL v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2283-103">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="a2283-104">Od [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="a2283-104">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a2283-105">Tento dokument zavádí přepis adres URL s pokyny k použití middlewaru přepisu adresy URL v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a2283-105">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="a2283-106">Přepsání adresy URL je to, že se jedná o úpravu adres URL žádostí na základě jednoho nebo více předdefinovaných pravidel.</span><span class="sxs-lookup"><span data-stu-id="a2283-106">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="a2283-107">Přepsání adresy URL vytvoří abstrakci mezi umístěními prostředků a jejich adresami tak, aby umístění a adresy nebyly pevně propojené.</span><span class="sxs-lookup"><span data-stu-id="a2283-107">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="a2283-108">Přepsání adresy URL je v několika scénářích užitečné pro:</span><span class="sxs-lookup"><span data-stu-id="a2283-108">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="a2283-109">Dočasné nebo trvalé přesunutí nebo nahrazení prostředků serveru a udržování stabilních lokátorů pro tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="a2283-109">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="a2283-110">Rozdělte zpracování požadavků napříč různými aplikacemi nebo napříč oblastmi jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2283-110">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="a2283-111">Umožňuje odebrat, přidat nebo změnit uspořádání segmentů adresy URL příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="a2283-111">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="a2283-112">Optimalizujte veřejné adresy URL pro optimalizaci vyhledávače (SEO).</span><span class="sxs-lookup"><span data-stu-id="a2283-112">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="a2283-113">Povolí použití přívětivých veřejných adres URL, které návštěvníkům pomůžou odhadnout obsah vrácený požadavkem na prostředek.</span><span class="sxs-lookup"><span data-stu-id="a2283-113">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="a2283-114">Přesměrovat nezabezpečené požadavky na zabezpečené koncové body.</span><span class="sxs-lookup"><span data-stu-id="a2283-114">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="a2283-115">Zabránit hotlinking, kde externí lokalita používá hostovaný statický prostředek na jiné lokalitě propojením prostředku s vlastním obsahem.</span><span class="sxs-lookup"><span data-stu-id="a2283-115">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="a2283-116">Přepsání adresy URL může snížit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2283-116">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="a2283-117">Pokud je to možné, omezte počet a složitost pravidel.</span><span class="sxs-lookup"><span data-stu-id="a2283-117">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="a2283-118">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a2283-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="a2283-119">Přesměrování adresy URL a přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="a2283-119">URL redirect and URL rewrite</span></span>

<span data-ttu-id="a2283-120">Rozdíl mezi přesměrováním *URL* a *přepsáním adresy URL* je malý, ale má důležitý dopad na poskytování prostředků klientům.</span><span class="sxs-lookup"><span data-stu-id="a2283-120">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="a2283-121">Rozhraní middleware pro přepis adres URL ASP.NET Core je schopné splnit nutnost obou.</span><span class="sxs-lookup"><span data-stu-id="a2283-121">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="a2283-122">*Přesměrování adresy URL* zahrnuje i operaci na straně klienta, kde klient má pokyn k přístupu k prostředku na jiné adrese, než jakou požadoval klient původně.</span><span class="sxs-lookup"><span data-stu-id="a2283-122">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="a2283-123">To vyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="a2283-123">This requires a round trip to the server.</span></span> <span data-ttu-id="a2283-124">Adresa URL pro přesměrování vracená klientovi se zobrazí v adresním řádku prohlížeče, když klient vytvoří nový požadavek na prostředek.</span><span class="sxs-lookup"><span data-stu-id="a2283-124">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="a2283-125">Pokud je `/resource` *přesměrované* na `/different-resource`, server odpoví na to, že klient získá prostředek na `/different-resource` se stavovým kódem označujícím, že přesměrování je buď dočasné, nebo trvalé.</span><span class="sxs-lookup"><span data-stu-id="a2283-125">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Koncový bod služby WebAPI se dočasně změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="a2283-131">Při přesměrování požadavků na jinou adresu URL určete, zda je přesměrování trvalé nebo dočasné, zadáním stavového kódu s odpovědí:</span><span class="sxs-lookup"><span data-stu-id="a2283-131">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="a2283-132">V případě, že má prostředek novou, trvalou adresu URL a chcete dát klientovi pokyn, aby všechny budoucí požadavky na prostředek používaly novou adresu URL, se použije kód *trvale přesunutý stav 301* .</span><span class="sxs-lookup"><span data-stu-id="a2283-132">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="a2283-133">*Klient může ukládat odpověď do mezipaměti a znovu ji použít, když se přijme stavový kód 301.*</span><span class="sxs-lookup"><span data-stu-id="a2283-133">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="a2283-134">V případě, že je přesměrování dočasné nebo obecně se může změnit, je použit stavový kód *302* .</span><span class="sxs-lookup"><span data-stu-id="a2283-134">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="a2283-135">Stavový kód 302 indikuje klientovi, aby neukládal adresu URL a použil ho v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="a2283-135">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="a2283-136">Další informace o stavových kódech najdete v [dokumentu RFC 2616: definice stavového kódu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="a2283-136">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="a2283-137">*Přepsání adresy URL* je operace na straně serveru, která poskytuje prostředek z jiné adresy prostředku, než jakou požaduje klient.</span><span class="sxs-lookup"><span data-stu-id="a2283-137">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="a2283-138">Přepsání adresy URL nevyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="a2283-138">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="a2283-139">Přepsaná adresa URL se nevrátí klientovi a nezobrazí se v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a2283-139">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="a2283-140">Pokud *přepíšete* `/resource` do `/different-resource`, server *interně* načte a vrátí prostředek na `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="a2283-140">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="a2283-141">I když klient může být schopný načíst prostředek na adrese URL přepsané adresy, klient nebude informovat o tom, že prostředek existuje na přepsané adrese URL, když odešle svůj požadavek a obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="a2283-141">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Koncový bod služby WebAPI se změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="a2283-146">Ukázková aplikace pro přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="a2283-146">URL rewriting sample app</span></span>

<span data-ttu-id="a2283-147">Pomocí [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)můžete prozkoumat funkce middlewaru přepsaného adresou URL.</span><span class="sxs-lookup"><span data-stu-id="a2283-147">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="a2283-148">Aplikace použije pravidla přesměrování a přepisu a zobrazí přesměrovanou nebo přepsanou adresu URL pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="a2283-148">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="a2283-149">Kdy použít middleware přepisu adresy URL</span><span class="sxs-lookup"><span data-stu-id="a2283-149">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="a2283-150">Pokud nemůžete použít následující přístupy, použijte middleware přepisující adresu URL:</span><span class="sxs-lookup"><span data-stu-id="a2283-150">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="a2283-151">Modul pro přepsání adresy URL ve službě IIS v systému Windows Server</span><span class="sxs-lookup"><span data-stu-id="a2283-151">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="a2283-152">Apache mod_rewrite modul na serveru Apache</span><span class="sxs-lookup"><span data-stu-id="a2283-152">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="a2283-153">Přepsání adresy URL na Nginx</span><span class="sxs-lookup"><span data-stu-id="a2283-153">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="a2283-154">Použijte taky middleware, pokud je aplikace hostována na [serveru http. sys](xref:fundamentals/servers/httpsys) (dříve nazývaném weblisten).</span><span class="sxs-lookup"><span data-stu-id="a2283-154">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="a2283-155">Hlavními důvody pro použití technologie přepisování adres URL založené na serveru ve službě IIS, Apache a Nginx jsou:</span><span class="sxs-lookup"><span data-stu-id="a2283-155">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="a2283-156">Middleware nepodporuje všechny funkce těchto modulů.</span><span class="sxs-lookup"><span data-stu-id="a2283-156">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="a2283-157">Některé funkce v modulech serveru nefungují s ASP.NET Core projekty, jako jsou `IsFile` a `IsDirectory` omezení modulu IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="a2283-157">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="a2283-158">V těchto scénářích místo toho použijte middleware.</span><span class="sxs-lookup"><span data-stu-id="a2283-158">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="a2283-159">Výkon middleware se pravděpodobně neshoduje s modulem.</span><span class="sxs-lookup"><span data-stu-id="a2283-159">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="a2283-160">Srovnávací testy jsou jediným způsobem, jak zjistit, který přístup snižuje výkon, který je v nejvyšší nebo v případě, že je snížený výkon.</span><span class="sxs-lookup"><span data-stu-id="a2283-160">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="a2283-161">Balíček</span><span class="sxs-lookup"><span data-stu-id="a2283-161">Package</span></span>

<span data-ttu-id="a2283-162">Middleware pro přepis adres URL poskytuje balíček [Microsoft. AspNetCore. Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) , který je implicitně zahrnutý v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a2283-162">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="a2283-163">Rozšíření a možnosti</span><span class="sxs-lookup"><span data-stu-id="a2283-163">Extension and options</span></span>

<span data-ttu-id="a2283-164">Vytvořte pravidla přepsání a přesměrování adresy URL vytvořením instance třídy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) s metodami rozšíření pro každé pravidlo přepsání.</span><span class="sxs-lookup"><span data-stu-id="a2283-164">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="a2283-165">Řetězení více pravidel v pořadí, ve kterém se mají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="a2283-165">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="a2283-166">`RewriteOptions` jsou předávány do middleware přepisu adresy URL, který je přidán do kanálu požadavků pomocí <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span><span class="sxs-lookup"><span data-stu-id="a2283-166">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="a2283-167">Přesměrovat neexistující na webovou</span><span class="sxs-lookup"><span data-stu-id="a2283-167">Redirect non-www to www</span></span>

<span data-ttu-id="a2283-168">Tři možnosti umožňují, aby aplikace přesměrovala požadavky bez`www` na `www`:</span><span class="sxs-lookup"><span data-stu-id="a2283-168">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="a2283-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; trvale přesměrovat požadavek do subdomény `www`, pokud je požadavek jiný než`www`.</span><span class="sxs-lookup"><span data-stu-id="a2283-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="a2283-170">Přesměruje stavový kód [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) .</span><span class="sxs-lookup"><span data-stu-id="a2283-170">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="a2283-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; přesměrovat požadavek do subdomény `www`, pokud je příchozí požadavek jiný než`www`.</span><span class="sxs-lookup"><span data-stu-id="a2283-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="a2283-172">Přesměruje stavový kód [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) .</span><span class="sxs-lookup"><span data-stu-id="a2283-172">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="a2283-173">Přetížení umožňuje poskytnout stavový kód pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="a2283-173">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="a2283-174">Pro přiřazení stavového kódu použijte pole třídy <xref:Microsoft.AspNetCore.Http.StatusCodes>.</span><span class="sxs-lookup"><span data-stu-id="a2283-174">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="a2283-175">Přesměrování adresy URL</span><span class="sxs-lookup"><span data-stu-id="a2283-175">URL redirect</span></span>

<span data-ttu-id="a2283-176">Přesměrování požadavků pomocí <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>.</span><span class="sxs-lookup"><span data-stu-id="a2283-176">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="a2283-177">První parametr obsahuje váš regulární výraz pro porovnání s cestou příchozí adresy URL.</span><span class="sxs-lookup"><span data-stu-id="a2283-177">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="a2283-178">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="a2283-178">The second parameter is the replacement string.</span></span> <span data-ttu-id="a2283-179">Třetí parametr, pokud je k dispozici, určuje stavový kód.</span><span class="sxs-lookup"><span data-stu-id="a2283-179">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="a2283-180">Pokud nezadáte stavový kód, stavový kód se standardně *302 – Nalezeno*, což znamená, že prostředek je dočasně přesunut nebo nahrazen.</span><span class="sxs-lookup"><span data-stu-id="a2283-180">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="a2283-181">V prohlížeči s povolenými vývojářskými nástroji si vyžádejte ukázkovou aplikaci s cestou `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="a2283-181">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="a2283-182">Regulární výraz odpovídá cestě požadavku na `redirect-rule/(.*)`a cesta je nahrazena `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="a2283-182">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="a2283-183">Adresa URL pro přesměrování se pošle zpátky do klienta s kódem stavu 302, který se *našel* .</span><span class="sxs-lookup"><span data-stu-id="a2283-183">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="a2283-184">Prohlížeč vytvoří novou žádost na adrese URL pro přesměrování, která se zobrazí v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a2283-184">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="a2283-185">Vzhledem k tomu, že se žádná pravidla v ukázkové aplikaci neshodují s adresou URL pro přesměrování:</span><span class="sxs-lookup"><span data-stu-id="a2283-185">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="a2283-186">Druhá žádost obdrží odpověď *200-OK* od aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2283-186">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="a2283-187">Tělo odpovědi zobrazuje adresu URL pro přesměrování.</span><span class="sxs-lookup"><span data-stu-id="a2283-187">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="a2283-188">Při *přesměrování*adresy URL se na Server provede cyklická výměna.</span><span class="sxs-lookup"><span data-stu-id="a2283-188">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="a2283-189">Buďte opatrní při vytváření pravidel přesměrování.</span><span class="sxs-lookup"><span data-stu-id="a2283-189">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="a2283-190">Pravidla přesměrování se vyhodnocují při každém požadavku na aplikaci, včetně po přesměrování.</span><span class="sxs-lookup"><span data-stu-id="a2283-190">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="a2283-191">Je snadné vytvořit *smyčku nekonečných přesměrování*.</span><span class="sxs-lookup"><span data-stu-id="a2283-191">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="a2283-192">Původní požadavek: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="a2283-192">Original Request: `/redirect-rule/1234/5678`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="a2283-194">Část výrazu obsažená v závorkách se nazývá *Skupina zachycení*.</span><span class="sxs-lookup"><span data-stu-id="a2283-194">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="a2283-195">Tečka (`.`) výrazu znamená, že *odpovídá libovolnému znaku*.</span><span class="sxs-lookup"><span data-stu-id="a2283-195">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="a2283-196">Hvězdička (`*`) značí, *že se předchozí znak rovná nule nebo vícekrát*.</span><span class="sxs-lookup"><span data-stu-id="a2283-196">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="a2283-197">Proto jsou poslední dva segmenty cesty adresy URL `1234/5678`zachyceny `(.*)`skupiny zachycení.</span><span class="sxs-lookup"><span data-stu-id="a2283-197">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="a2283-198">Jakákoli hodnota, kterou zadáte v adrese URL požadavku po `redirect-rule/`, je zachycena touto jednou skupinou zachycení.</span><span class="sxs-lookup"><span data-stu-id="a2283-198">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="a2283-199">V řetězci pro nahrazení jsou zachycené skupiny vloženy do řetězce s znakem dolaru (`$`) následovaným pořadovým číslem záznamu.</span><span class="sxs-lookup"><span data-stu-id="a2283-199">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="a2283-200">První hodnota skupiny zachycení se získá s `$1`, druhým s `$2`a v posloupnosti pro skupiny zachycení ve vašem regulárním výrazu pokračuje.</span><span class="sxs-lookup"><span data-stu-id="a2283-200">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="a2283-201">Regulární výraz pravidla přesměrování v ukázkové aplikaci obsahuje jenom jednu zachycenou skupinu, takže v náhradním řetězci je jenom jedna vložená skupina, která je `$1`.</span><span class="sxs-lookup"><span data-stu-id="a2283-201">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="a2283-202">Po použití pravidla se adresa URL bude `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="a2283-202">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="a2283-203">Přesměrování adresy URL na zabezpečený koncový bod</span><span class="sxs-lookup"><span data-stu-id="a2283-203">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="a2283-204">Pomocí <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> přesměrovat požadavky HTTP na stejného hostitele a cestu pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a2283-204">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="a2283-205">Pokud kód stavu není zadaný, použije se ve výchozím nastavení middleware *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="a2283-205">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="a2283-206">Pokud port není dodán:</span><span class="sxs-lookup"><span data-stu-id="a2283-206">If the port isn't supplied:</span></span>

* <span data-ttu-id="a2283-207">Výchozí middleware je `null`.</span><span class="sxs-lookup"><span data-stu-id="a2283-207">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="a2283-208">Schéma se změní na `https` (protokol HTTPS) a klient přistupuje k prostředku na portu 443.</span><span class="sxs-lookup"><span data-stu-id="a2283-208">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="a2283-209">Následující příklad ukazuje, jak nastavit stavový kód na *301 – trvale přesunuto* a změnit port na 5001.</span><span class="sxs-lookup"><span data-stu-id="a2283-209">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="a2283-210">Pomocí <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> přesměrovat nezabezpečené požadavky na stejného hostitele a cestu se zabezpečeným protokolem HTTPS na portu 443.</span><span class="sxs-lookup"><span data-stu-id="a2283-210">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="a2283-211">Middleware nastaví stavový kód na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="a2283-211">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="a2283-212">Při přesměrování na zabezpečený koncový bod bez požadavku na další pravidla přesměrování doporučujeme použít middleware pro přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a2283-212">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="a2283-213">Další informace najdete v tématu věnovaném [vykonání protokolu HTTPS](xref:security/enforcing-ssl#require-https) .</span><span class="sxs-lookup"><span data-stu-id="a2283-213">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="a2283-214">Ukázková aplikace dokáže demonstrovat použití `AddRedirectToHttps` nebo `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="a2283-214">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="a2283-215">Přidejte metodu rozšíření do `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="a2283-215">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="a2283-216">Vytvořte nezabezpečenou žádost o aplikaci na libovolné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="a2283-216">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="a2283-217">Zrušte upozornění zabezpečení prohlížeče, že certifikát podepsaný svým držitelem je nedůvěryhodný, nebo vytvořte výjimku pro důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="a2283-217">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="a2283-218">Původní požadavek pomocí `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="a2283-218">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="a2283-220">Původní požadavek pomocí `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="a2283-220">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="a2283-222">Přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="a2283-222">URL rewrite</span></span>

<span data-ttu-id="a2283-223">Pomocí <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> vytvořit pravidlo pro přepis adres URL.</span><span class="sxs-lookup"><span data-stu-id="a2283-223">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="a2283-224">První parametr obsahuje regulární výraz pro porovnání na cestě příchozích adres URL.</span><span class="sxs-lookup"><span data-stu-id="a2283-224">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="a2283-225">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="a2283-225">The second parameter is the replacement string.</span></span> <span data-ttu-id="a2283-226">Třetí parametr, `skipRemainingRules: {true|false}`, označuje middleware bez ohledu na to, jestli se má použít aktuální pravidlo, nebo jestli se mají přeskočit další pravidla přepsání.</span><span class="sxs-lookup"><span data-stu-id="a2283-226">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="a2283-227">Původní požadavek: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="a2283-227">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="a2283-229">Kosočtverce (`^`) na začátku výrazu znamená, že shoda začíná na začátku cesty URL.</span><span class="sxs-lookup"><span data-stu-id="a2283-229">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="a2283-230">V předchozím příkladu s pravidlem přesměrování `redirect-rule/(.*)`neexistuje kosočtverce (`^`) na začátku regulárního výrazu.</span><span class="sxs-lookup"><span data-stu-id="a2283-230">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="a2283-231">Proto libovolné znaky mohou předcházet `redirect-rule/` v cestě pro úspěšnou shodu.</span><span class="sxs-lookup"><span data-stu-id="a2283-231">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="a2283-232">Cesta</span><span class="sxs-lookup"><span data-stu-id="a2283-232">Path</span></span>                               | <span data-ttu-id="a2283-233">Shoda</span><span class="sxs-lookup"><span data-stu-id="a2283-233">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="a2283-234">Ano</span><span class="sxs-lookup"><span data-stu-id="a2283-234">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="a2283-235">Ano</span><span class="sxs-lookup"><span data-stu-id="a2283-235">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="a2283-236">Ano</span><span class="sxs-lookup"><span data-stu-id="a2283-236">Yes</span></span>   |

<span data-ttu-id="a2283-237">Pravidlo přepisu, `^rewrite-rule/(\d+)/(\d+)`, odpovídá pouze cestám, pokud začínají na `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="a2283-237">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="a2283-238">V následující tabulce si všimněte rozdílu v porovnání.</span><span class="sxs-lookup"><span data-stu-id="a2283-238">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="a2283-239">Cesta</span><span class="sxs-lookup"><span data-stu-id="a2283-239">Path</span></span>                              | <span data-ttu-id="a2283-240">Shoda</span><span class="sxs-lookup"><span data-stu-id="a2283-240">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="a2283-241">Ano</span><span class="sxs-lookup"><span data-stu-id="a2283-241">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="a2283-242">Ne</span><span class="sxs-lookup"><span data-stu-id="a2283-242">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="a2283-243">Ne</span><span class="sxs-lookup"><span data-stu-id="a2283-243">No</span></span>    |

<span data-ttu-id="a2283-244">Po `^rewrite-rule/` části výrazu jsou k dispozici dvě skupiny zachycení `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="a2283-244">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="a2283-245">`\d` značí *shodu s číslicí (číslem)* .</span><span class="sxs-lookup"><span data-stu-id="a2283-245">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="a2283-246">Znaménko plus (`+`) znamená, že se *shoduje s jedním nebo více předcházejícím znakem*.</span><span class="sxs-lookup"><span data-stu-id="a2283-246">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="a2283-247">Proto musí adresa URL obsahovat číslo následované lomítkem, za kterým následuje jiné číslo.</span><span class="sxs-lookup"><span data-stu-id="a2283-247">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="a2283-248">Tyto skupiny zachycení jsou vloženy do přepsané adresy URL jako `$1` a `$2`.</span><span class="sxs-lookup"><span data-stu-id="a2283-248">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="a2283-249">Náhradní řetězec pravidla přepsaní umístí zachycené skupiny do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="a2283-249">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="a2283-250">Požadovaná cesta `/rewrite-rule/1234/5678` je přepsána za účelem získání prostředku v `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="a2283-250">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="a2283-251">Pokud se v původním požadavku nachází řetězec dotazu, bude při přepisování adresy URL zachován.</span><span class="sxs-lookup"><span data-stu-id="a2283-251">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="a2283-252">K získání prostředku neexistuje žádná Přenosová cesta k serveru.</span><span class="sxs-lookup"><span data-stu-id="a2283-252">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="a2283-253">Pokud prostředek existuje, načte se a vrátí do klienta s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="a2283-253">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="a2283-254">Vzhledem k tomu, že klient není přesměrován, adresa URL v adresním řádku prohlížeče se nezmění.</span><span class="sxs-lookup"><span data-stu-id="a2283-254">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="a2283-255">Klienti nerozpoznají, že na serveru došlo k operaci přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="a2283-255">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="a2283-256">Použijte `skipRemainingRules: true` kdykoli je to možné, protože vyhovující pravidla jsou výpočetně náročná a zvyšují dobu odezvy aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2283-256">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="a2283-257">Pro nejrychlejší aplikační odpověď:</span><span class="sxs-lookup"><span data-stu-id="a2283-257">For the fastest app response:</span></span>
>
> * <span data-ttu-id="a2283-258">Seřazení pravidel přepisu z nejčastěji spárovaného pravidla na nejméně často spárované pravidlo.</span><span class="sxs-lookup"><span data-stu-id="a2283-258">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="a2283-259">Pokud dojde ke shodě a není vyžadováno žádné další zpracování pravidla, přeskočte zpracování zbývajících pravidel.</span><span class="sxs-lookup"><span data-stu-id="a2283-259">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="a2283-260">Mod_rewrite Apache</span><span class="sxs-lookup"><span data-stu-id="a2283-260">Apache mod_rewrite</span></span>

<span data-ttu-id="a2283-261">Použijte pravidla mod_rewrite Apache s <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="a2283-261">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="a2283-262">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="a2283-262">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="a2283-263">Další informace a příklady pravidel mod_rewrite najdete v tématu [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="a2283-263">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="a2283-264"><xref:System.IO.StreamReader> se používá ke čtení pravidel ze souboru pravidel *ApacheModRewrite. txt* :</span><span class="sxs-lookup"><span data-stu-id="a2283-264">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="a2283-265">Ukázková aplikace přesměrovává požadavky od `/apache-mod-rules-redirect/(.\*)` do `/redirected?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="a2283-265">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="a2283-266">Stavový kód odpovědi je *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="a2283-266">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="a2283-267">Původní požadavek: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="a2283-267">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="a2283-269">Middleware podporuje následující proměnné serveru Apache mod_rewrite:</span><span class="sxs-lookup"><span data-stu-id="a2283-269">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="a2283-270">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="a2283-270">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="a2283-271">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="a2283-271">HTTP_ACCEPT</span></span>
* <span data-ttu-id="a2283-272">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="a2283-272">HTTP_CONNECTION</span></span>
* <span data-ttu-id="a2283-273">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="a2283-273">HTTP_COOKIE</span></span>
* <span data-ttu-id="a2283-274">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="a2283-274">HTTP_FORWARDED</span></span>
* <span data-ttu-id="a2283-275">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="a2283-275">HTTP_HOST</span></span>
* <span data-ttu-id="a2283-276">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="a2283-276">HTTP_REFERER</span></span>
* <span data-ttu-id="a2283-277">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="a2283-277">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="a2283-278">HTTPS</span><span class="sxs-lookup"><span data-stu-id="a2283-278">HTTPS</span></span>
* <span data-ttu-id="a2283-279">IPV6</span><span class="sxs-lookup"><span data-stu-id="a2283-279">IPV6</span></span>
* <span data-ttu-id="a2283-280">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="a2283-280">QUERY_STRING</span></span>
* <span data-ttu-id="a2283-281">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="a2283-281">REMOTE_ADDR</span></span>
* <span data-ttu-id="a2283-282">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="a2283-282">REMOTE_PORT</span></span>
* <span data-ttu-id="a2283-283">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="a2283-283">REQUEST_FILENAME</span></span>
* <span data-ttu-id="a2283-284">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="a2283-284">REQUEST_METHOD</span></span>
* <span data-ttu-id="a2283-285">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="a2283-285">REQUEST_SCHEME</span></span>
* <span data-ttu-id="a2283-286">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="a2283-286">REQUEST_URI</span></span>
* <span data-ttu-id="a2283-287">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="a2283-287">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="a2283-288">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="a2283-288">SERVER_ADDR</span></span>
* <span data-ttu-id="a2283-289">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="a2283-289">SERVER_PORT</span></span>
* <span data-ttu-id="a2283-290">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="a2283-290">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="a2283-291">ČAS</span><span class="sxs-lookup"><span data-stu-id="a2283-291">TIME</span></span>
* <span data-ttu-id="a2283-292">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="a2283-292">TIME_DAY</span></span>
* <span data-ttu-id="a2283-293">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="a2283-293">TIME_HOUR</span></span>
* <span data-ttu-id="a2283-294">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="a2283-294">TIME_MIN</span></span>
* <span data-ttu-id="a2283-295">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="a2283-295">TIME_MON</span></span>
* <span data-ttu-id="a2283-296">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="a2283-296">TIME_SEC</span></span>
* <span data-ttu-id="a2283-297">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="a2283-297">TIME_WDAY</span></span>
* <span data-ttu-id="a2283-298">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="a2283-298">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="a2283-299">Pravidla pro přepsání adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="a2283-299">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="a2283-300">Pokud chcete použít stejnou sadu pravidel, která se vztahuje na modul IIS URL Rewrite, použijte <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="a2283-300">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="a2283-301">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="a2283-301">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="a2283-302">Nesměrujte middleware na použití souboru *Web. config* aplikace při spuštění v systému Windows Server IIS.</span><span class="sxs-lookup"><span data-stu-id="a2283-302">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="a2283-303">V případě služby IIS by tato pravidla měla být uložena mimo soubor *Web. config* aplikace, aby nedocházelo ke konfliktům s modulem pro přepis služby IIS.</span><span class="sxs-lookup"><span data-stu-id="a2283-303">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="a2283-304">Další informace a příklady pravidel pro přepis adres URL služby IIS najdete v tématu použití odkazu na modul pro [přepis adres url 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [odkaz na konfiguraci modulu přepisu adresy](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)URL.</span><span class="sxs-lookup"><span data-stu-id="a2283-304">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="a2283-305">Pro čtení pravidel ze souboru pravidel *IISUrlRewrite. XML* se používá <xref:System.IO.StreamReader>:</span><span class="sxs-lookup"><span data-stu-id="a2283-305">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="a2283-306">Ukázková aplikace přepíše požadavky od `/iis-rules-rewrite/(.*)` do `/rewritten?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="a2283-306">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="a2283-307">Odpověď se pošle klientovi s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="a2283-307">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="a2283-308">Původní požadavek: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="a2283-308">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="a2283-310">Pokud máte aktivní modul pro přepis služby IIS s nakonfigurovanými pravidly na úrovni serveru, který by ovlivnil vaši aplikaci nežádoucím způsobem, můžete pro aplikaci zakázat modul IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="a2283-310">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="a2283-311">Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="a2283-311">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="a2283-312">Nepodporované funkce</span><span class="sxs-lookup"><span data-stu-id="a2283-312">Unsupported features</span></span>

<span data-ttu-id="a2283-313">Middleware vydaná ASP.NET Core 2. x nepodporuje následující funkce modulu pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="a2283-313">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="a2283-314">Pravidla pro odchozí provoz</span><span class="sxs-lookup"><span data-stu-id="a2283-314">Outbound Rules</span></span>
* <span data-ttu-id="a2283-315">Vlastní proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="a2283-315">Custom Server Variables</span></span>
* <span data-ttu-id="a2283-316">Zástupné znaky</span><span class="sxs-lookup"><span data-stu-id="a2283-316">Wildcards</span></span>
* <span data-ttu-id="a2283-317">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="a2283-317">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="a2283-318">Podporované proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="a2283-318">Supported server variables</span></span>

<span data-ttu-id="a2283-319">Middleware podporuje následující proměnné serveru pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="a2283-319">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="a2283-320">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="a2283-320">CONTENT_LENGTH</span></span>
* <span data-ttu-id="a2283-321">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="a2283-321">CONTENT_TYPE</span></span>
* <span data-ttu-id="a2283-322">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="a2283-322">HTTP_ACCEPT</span></span>
* <span data-ttu-id="a2283-323">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="a2283-323">HTTP_CONNECTION</span></span>
* <span data-ttu-id="a2283-324">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="a2283-324">HTTP_COOKIE</span></span>
* <span data-ttu-id="a2283-325">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="a2283-325">HTTP_HOST</span></span>
* <span data-ttu-id="a2283-326">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="a2283-326">HTTP_REFERER</span></span>
* <span data-ttu-id="a2283-327">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="a2283-327">HTTP_URL</span></span>
* <span data-ttu-id="a2283-328">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="a2283-328">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="a2283-329">HTTPS</span><span class="sxs-lookup"><span data-stu-id="a2283-329">HTTPS</span></span>
* <span data-ttu-id="a2283-330">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="a2283-330">LOCAL_ADDR</span></span>
* <span data-ttu-id="a2283-331">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="a2283-331">QUERY_STRING</span></span>
* <span data-ttu-id="a2283-332">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="a2283-332">REMOTE_ADDR</span></span>
* <span data-ttu-id="a2283-333">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="a2283-333">REMOTE_PORT</span></span>
* <span data-ttu-id="a2283-334">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="a2283-334">REQUEST_FILENAME</span></span>
* <span data-ttu-id="a2283-335">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="a2283-335">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="a2283-336"><xref:Microsoft.Extensions.FileProviders.IFileProvider> můžete také získat pomocí <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="a2283-336">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="a2283-337">Tento přístup může poskytovat větší flexibilitu pro umístění souborů pravidel přepisu.</span><span class="sxs-lookup"><span data-stu-id="a2283-337">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="a2283-338">Ujistěte se, že jsou v cestě, kterou zadáte, nasazené soubory pravidel přepisu na server.</span><span class="sxs-lookup"><span data-stu-id="a2283-338">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="a2283-339">Pravidlo založené na metodách</span><span class="sxs-lookup"><span data-stu-id="a2283-339">Method-based rule</span></span>

<span data-ttu-id="a2283-340">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k implementaci logiky pravidla v metodě.</span><span class="sxs-lookup"><span data-stu-id="a2283-340">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="a2283-341">`Add` zpřístupňuje <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, která zpřístupňuje <xref:Microsoft.AspNetCore.Http.HttpContext> pro použití ve vaší metodě.</span><span class="sxs-lookup"><span data-stu-id="a2283-341">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="a2283-342">[RewriteContext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) určuje, jak se zpracovává další zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="a2283-342">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="a2283-343">Nastavte hodnotu na jedno z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> polí popsaných v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="a2283-343">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="a2283-344">Akce</span><span class="sxs-lookup"><span data-stu-id="a2283-344">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="a2283-345">`RuleResult.ContinueRules` (výchozí)</span><span class="sxs-lookup"><span data-stu-id="a2283-345">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="a2283-346">Pokračovat v používání pravidel.</span><span class="sxs-lookup"><span data-stu-id="a2283-346">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="a2283-347">Zastavení použití pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a2283-347">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="a2283-348">Zastavení použití pravidel a odeslání kontextu do dalšího middlewaru.</span><span class="sxs-lookup"><span data-stu-id="a2283-348">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="a2283-349">Ukázková aplikace ukazuje metodu, která přesměrovává požadavky na cesty, které končí na *. XML*.</span><span class="sxs-lookup"><span data-stu-id="a2283-349">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="a2283-350">Pokud se pro `/file.xml`požadavek provede, je žádost přesměrována na `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="a2283-350">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="a2283-351">Stavový kód je nastaven na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="a2283-351">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="a2283-352">Když prohlížeč vytvoří nový požadavek na */xmlfiles/File.XML*, soubor middleware statických souborů zachová soubor klientovi ze složky *wwwroot/xmlfiles* .</span><span class="sxs-lookup"><span data-stu-id="a2283-352">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="a2283-353">Pro přesměrování explicitně nastavte stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a2283-353">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="a2283-354">V opačném případě se vrátí stavový kód *200-OK* a na klientovi se neobjeví přesměrování.</span><span class="sxs-lookup"><span data-stu-id="a2283-354">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="a2283-355">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="a2283-355">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="a2283-356">Tento přístup může také přezapisovat požadavky.</span><span class="sxs-lookup"><span data-stu-id="a2283-356">This approach can also rewrite requests.</span></span> <span data-ttu-id="a2283-357">Ukázková aplikace ukazuje přepis cesty k libovolné žádosti o textový soubor, aby sloužil textový soubor *. txt* ze složky *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="a2283-357">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="a2283-358">Middleware statických souborů zachovává soubor na základě aktualizované cesty požadavku:</span><span class="sxs-lookup"><span data-stu-id="a2283-358">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="a2283-359">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="a2283-359">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="a2283-360">Pravidlo založené na IRule</span><span class="sxs-lookup"><span data-stu-id="a2283-360">IRule-based rule</span></span>

<span data-ttu-id="a2283-361">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k použití logiky pravidla ve třídě, která implementuje rozhraní <xref:Microsoft.AspNetCore.Rewrite.IRule>.</span><span class="sxs-lookup"><span data-stu-id="a2283-361">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="a2283-362">`IRule` poskytuje větší flexibilitu při používání přístupu k pravidlům založeným na metodách.</span><span class="sxs-lookup"><span data-stu-id="a2283-362">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="a2283-363">Vaše třída implementace může zahrnovat konstruktor, který umožňuje předat parametry pro metodu <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.</span><span class="sxs-lookup"><span data-stu-id="a2283-363">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="a2283-364">Hodnoty parametrů v ukázkové aplikaci pro `extension` a `newPath` jsou kontrolovány, aby splňovaly několik podmínek.</span><span class="sxs-lookup"><span data-stu-id="a2283-364">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="a2283-365">`extension` musí obsahovat hodnotu a hodnota musí být *. png*, *. jpg*nebo *. gif*.</span><span class="sxs-lookup"><span data-stu-id="a2283-365">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="a2283-366">Pokud `newPath` není platný, je vyvolána <xref:System.ArgumentException>.</span><span class="sxs-lookup"><span data-stu-id="a2283-366">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="a2283-367">Pokud je pro *image. png*vytvořen požadavek, je požadavek přesměrován na `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="a2283-367">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="a2283-368">Pokud je pro *image. jpg*vytvořen požadavek, je požadavek přesměrován na `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="a2283-368">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="a2283-369">Stavový kód je nastaven na *301 – trvale přesunuto*a `context.Result` je nastavené na zastavení zpracování pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a2283-369">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="a2283-370">Původní požadavek: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="a2283-370">Original Request: `/image.png`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image. png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="a2283-372">Původní požadavek: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="a2283-372">Original Request: `/image.jpg`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image. jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="a2283-374">Příklady Regex</span><span class="sxs-lookup"><span data-stu-id="a2283-374">Regex examples</span></span>

| <span data-ttu-id="a2283-375">Cíl</span><span class="sxs-lookup"><span data-stu-id="a2283-375">Goal</span></span> | <span data-ttu-id="a2283-376">& Řetězce regulárního výrazu</span><span class="sxs-lookup"><span data-stu-id="a2283-376">Regex String &</span></span><br><span data-ttu-id="a2283-377">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="a2283-377">Match Example</span></span> | <span data-ttu-id="a2283-378">& Řetězce nahrazení</span><span class="sxs-lookup"><span data-stu-id="a2283-378">Replacement String &</span></span><br><span data-ttu-id="a2283-379">Příklad výstupu</span><span class="sxs-lookup"><span data-stu-id="a2283-379">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="a2283-380">Přepište cestu do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="a2283-380">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="a2283-381">Koncové lomítko pruhu</span><span class="sxs-lookup"><span data-stu-id="a2283-381">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="a2283-382">Vymáhat koncové lomítko</span><span class="sxs-lookup"><span data-stu-id="a2283-382">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="a2283-383">Vyhnout se přepisu konkrétních požadavků</span><span class="sxs-lookup"><span data-stu-id="a2283-383">Avoid rewriting specific requests</span></span> | <span data-ttu-id="a2283-384">`^(.*)(?<!\.axd)$` nebo `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="a2283-384">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="a2283-385">Ano: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="a2283-385">Yes: `/resource.htm`</span></span><br><span data-ttu-id="a2283-386">Ne: `/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="a2283-386">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="a2283-387">Změna uspořádání segmentů adresy URL</span><span class="sxs-lookup"><span data-stu-id="a2283-387">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="a2283-388">Nahradit segment adresy URL</span><span class="sxs-lookup"><span data-stu-id="a2283-388">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a2283-389">Tento dokument zavádí přepis adres URL s pokyny k použití middlewaru přepisu adresy URL v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a2283-389">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="a2283-390">Přepsání adresy URL je to, že se jedná o úpravu adres URL žádostí na základě jednoho nebo více předdefinovaných pravidel.</span><span class="sxs-lookup"><span data-stu-id="a2283-390">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="a2283-391">Přepsání adresy URL vytvoří abstrakci mezi umístěními prostředků a jejich adresami tak, aby umístění a adresy nebyly pevně propojené.</span><span class="sxs-lookup"><span data-stu-id="a2283-391">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="a2283-392">Přepsání adresy URL je v několika scénářích užitečné pro:</span><span class="sxs-lookup"><span data-stu-id="a2283-392">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="a2283-393">Dočasné nebo trvalé přesunutí nebo nahrazení prostředků serveru a udržování stabilních lokátorů pro tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="a2283-393">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="a2283-394">Rozdělte zpracování požadavků napříč různými aplikacemi nebo napříč oblastmi jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2283-394">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="a2283-395">Umožňuje odebrat, přidat nebo změnit uspořádání segmentů adresy URL příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="a2283-395">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="a2283-396">Optimalizujte veřejné adresy URL pro optimalizaci vyhledávače (SEO).</span><span class="sxs-lookup"><span data-stu-id="a2283-396">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="a2283-397">Povolí použití přívětivých veřejných adres URL, které návštěvníkům pomůžou odhadnout obsah vrácený požadavkem na prostředek.</span><span class="sxs-lookup"><span data-stu-id="a2283-397">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="a2283-398">Přesměrovat nezabezpečené požadavky na zabezpečené koncové body.</span><span class="sxs-lookup"><span data-stu-id="a2283-398">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="a2283-399">Zabránit hotlinking, kde externí lokalita používá hostovaný statický prostředek na jiné lokalitě propojením prostředku s vlastním obsahem.</span><span class="sxs-lookup"><span data-stu-id="a2283-399">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="a2283-400">Přepsání adresy URL může snížit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2283-400">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="a2283-401">Pokud je to možné, omezte počet a složitost pravidel.</span><span class="sxs-lookup"><span data-stu-id="a2283-401">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="a2283-402">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a2283-402">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="a2283-403">Přesměrování adresy URL a přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="a2283-403">URL redirect and URL rewrite</span></span>

<span data-ttu-id="a2283-404">Rozdíl mezi přesměrováním *URL* a *přepsáním adresy URL* je malý, ale má důležitý dopad na poskytování prostředků klientům.</span><span class="sxs-lookup"><span data-stu-id="a2283-404">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="a2283-405">Rozhraní middleware pro přepis adres URL ASP.NET Core je schopné splnit nutnost obou.</span><span class="sxs-lookup"><span data-stu-id="a2283-405">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="a2283-406">*Přesměrování adresy URL* zahrnuje i operaci na straně klienta, kde klient má pokyn k přístupu k prostředku na jiné adrese, než jakou požadoval klient původně.</span><span class="sxs-lookup"><span data-stu-id="a2283-406">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="a2283-407">To vyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="a2283-407">This requires a round trip to the server.</span></span> <span data-ttu-id="a2283-408">Adresa URL pro přesměrování vracená klientovi se zobrazí v adresním řádku prohlížeče, když klient vytvoří nový požadavek na prostředek.</span><span class="sxs-lookup"><span data-stu-id="a2283-408">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="a2283-409">Pokud je `/resource` *přesměrované* na `/different-resource`, server odpoví na to, že klient získá prostředek na `/different-resource` se stavovým kódem označujícím, že přesměrování je buď dočasné, nebo trvalé.</span><span class="sxs-lookup"><span data-stu-id="a2283-409">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Koncový bod služby WebAPI se dočasně změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="a2283-415">Při přesměrování požadavků na jinou adresu URL určete, zda je přesměrování trvalé nebo dočasné, zadáním stavového kódu s odpovědí:</span><span class="sxs-lookup"><span data-stu-id="a2283-415">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="a2283-416">V případě, že má prostředek novou, trvalou adresu URL a chcete dát klientovi pokyn, aby všechny budoucí požadavky na prostředek používaly novou adresu URL, se použije kód *trvale přesunutý stav 301* .</span><span class="sxs-lookup"><span data-stu-id="a2283-416">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="a2283-417">*Klient může ukládat odpověď do mezipaměti a znovu ji použít, když se přijme stavový kód 301.*</span><span class="sxs-lookup"><span data-stu-id="a2283-417">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="a2283-418">V případě, že je přesměrování dočasné nebo obecně se může změnit, je použit stavový kód *302* .</span><span class="sxs-lookup"><span data-stu-id="a2283-418">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="a2283-419">Stavový kód 302 indikuje klientovi, aby neukládal adresu URL a použil ho v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="a2283-419">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="a2283-420">Další informace o stavových kódech najdete v [dokumentu RFC 2616: definice stavového kódu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="a2283-420">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="a2283-421">*Přepsání adresy URL* je operace na straně serveru, která poskytuje prostředek z jiné adresy prostředku, než jakou požaduje klient.</span><span class="sxs-lookup"><span data-stu-id="a2283-421">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="a2283-422">Přepsání adresy URL nevyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="a2283-422">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="a2283-423">Přepsaná adresa URL se nevrátí klientovi a nezobrazí se v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a2283-423">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="a2283-424">Pokud *přepíšete* `/resource` do `/different-resource`, server *interně* načte a vrátí prostředek na `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="a2283-424">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="a2283-425">I když klient může být schopný načíst prostředek na adrese URL přepsané adresy, klient nebude informovat o tom, že prostředek existuje na přepsané adrese URL, když odešle svůj požadavek a obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="a2283-425">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Koncový bod služby WebAPI se změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="a2283-430">Ukázková aplikace pro přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="a2283-430">URL rewriting sample app</span></span>

<span data-ttu-id="a2283-431">Pomocí [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)můžete prozkoumat funkce middlewaru přepsaného adresou URL.</span><span class="sxs-lookup"><span data-stu-id="a2283-431">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="a2283-432">Aplikace použije pravidla přesměrování a přepisu a zobrazí přesměrovanou nebo přepsanou adresu URL pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="a2283-432">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="a2283-433">Kdy použít middleware přepisu adresy URL</span><span class="sxs-lookup"><span data-stu-id="a2283-433">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="a2283-434">Pokud nemůžete použít následující přístupy, použijte middleware přepisující adresu URL:</span><span class="sxs-lookup"><span data-stu-id="a2283-434">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="a2283-435">Modul pro přepsání adresy URL ve službě IIS v systému Windows Server</span><span class="sxs-lookup"><span data-stu-id="a2283-435">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="a2283-436">Apache mod_rewrite modul na serveru Apache</span><span class="sxs-lookup"><span data-stu-id="a2283-436">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="a2283-437">Přepsání adresy URL na Nginx</span><span class="sxs-lookup"><span data-stu-id="a2283-437">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="a2283-438">Použijte taky middleware, pokud je aplikace hostována na [serveru http. sys](xref:fundamentals/servers/httpsys) (dříve nazývaném weblisten).</span><span class="sxs-lookup"><span data-stu-id="a2283-438">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="a2283-439">Hlavními důvody pro použití technologie přepisování adres URL založené na serveru ve službě IIS, Apache a Nginx jsou:</span><span class="sxs-lookup"><span data-stu-id="a2283-439">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="a2283-440">Middleware nepodporuje všechny funkce těchto modulů.</span><span class="sxs-lookup"><span data-stu-id="a2283-440">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="a2283-441">Některé funkce v modulech serveru nefungují s ASP.NET Core projekty, jako jsou `IsFile` a `IsDirectory` omezení modulu IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="a2283-441">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="a2283-442">V těchto scénářích místo toho použijte middleware.</span><span class="sxs-lookup"><span data-stu-id="a2283-442">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="a2283-443">Výkon middleware se pravděpodobně neshoduje s modulem.</span><span class="sxs-lookup"><span data-stu-id="a2283-443">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="a2283-444">Srovnávací testy jsou jediným způsobem, jak zjistit, který přístup snižuje výkon, který je v nejvyšší nebo v případě, že je snížený výkon.</span><span class="sxs-lookup"><span data-stu-id="a2283-444">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="a2283-445">Balíček</span><span class="sxs-lookup"><span data-stu-id="a2283-445">Package</span></span>

<span data-ttu-id="a2283-446">Chcete-li do projektu zahrnout middleware, přidejte odkaz na balíček do souboru [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) v souboru projektu, který obsahuje balíček [Microsoft. AspNetCore. Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) .</span><span class="sxs-lookup"><span data-stu-id="a2283-446">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="a2283-447">Pokud nepoužíváte `Microsoft.AspNetCore.App` Metapackage, přidejte do balíčku `Microsoft.AspNetCore.Rewrite` odkaz na projekt.</span><span class="sxs-lookup"><span data-stu-id="a2283-447">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="a2283-448">Rozšíření a možnosti</span><span class="sxs-lookup"><span data-stu-id="a2283-448">Extension and options</span></span>

<span data-ttu-id="a2283-449">Vytvořte pravidla přepsání a přesměrování adresy URL vytvořením instance třídy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) s metodami rozšíření pro každé pravidlo přepsání.</span><span class="sxs-lookup"><span data-stu-id="a2283-449">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="a2283-450">Řetězení více pravidel v pořadí, ve kterém se mají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="a2283-450">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="a2283-451">`RewriteOptions` jsou předávány do middleware přepisu adresy URL, který je přidán do kanálu požadavků pomocí <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span><span class="sxs-lookup"><span data-stu-id="a2283-451">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="a2283-452">Přesměrovat neexistující na webovou</span><span class="sxs-lookup"><span data-stu-id="a2283-452">Redirect non-www to www</span></span>

<span data-ttu-id="a2283-453">Tři možnosti umožňují, aby aplikace přesměrovala požadavky bez`www` na `www`:</span><span class="sxs-lookup"><span data-stu-id="a2283-453">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="a2283-454"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; trvale přesměrovat požadavek do subdomény `www`, pokud je požadavek jiný než`www`.</span><span class="sxs-lookup"><span data-stu-id="a2283-454"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="a2283-455">Přesměruje stavový kód [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) .</span><span class="sxs-lookup"><span data-stu-id="a2283-455">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="a2283-456"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; přesměrovat požadavek do subdomény `www`, pokud je příchozí požadavek jiný než`www`.</span><span class="sxs-lookup"><span data-stu-id="a2283-456"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="a2283-457">Přesměruje stavový kód [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) .</span><span class="sxs-lookup"><span data-stu-id="a2283-457">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="a2283-458">Přetížení umožňuje poskytnout stavový kód pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="a2283-458">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="a2283-459">Pro přiřazení stavového kódu použijte pole třídy <xref:Microsoft.AspNetCore.Http.StatusCodes>.</span><span class="sxs-lookup"><span data-stu-id="a2283-459">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="a2283-460">Přesměrování adresy URL</span><span class="sxs-lookup"><span data-stu-id="a2283-460">URL redirect</span></span>

<span data-ttu-id="a2283-461">Přesměrování požadavků pomocí <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>.</span><span class="sxs-lookup"><span data-stu-id="a2283-461">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="a2283-462">První parametr obsahuje váš regulární výraz pro porovnání s cestou příchozí adresy URL.</span><span class="sxs-lookup"><span data-stu-id="a2283-462">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="a2283-463">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="a2283-463">The second parameter is the replacement string.</span></span> <span data-ttu-id="a2283-464">Třetí parametr, pokud je k dispozici, určuje stavový kód.</span><span class="sxs-lookup"><span data-stu-id="a2283-464">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="a2283-465">Pokud nezadáte stavový kód, stavový kód se standardně *302 – Nalezeno*, což znamená, že prostředek je dočasně přesunut nebo nahrazen.</span><span class="sxs-lookup"><span data-stu-id="a2283-465">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="a2283-466">V prohlížeči s povolenými vývojářskými nástroji si vyžádejte ukázkovou aplikaci s cestou `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="a2283-466">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="a2283-467">Regulární výraz odpovídá cestě požadavku na `redirect-rule/(.*)`a cesta je nahrazena `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="a2283-467">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="a2283-468">Adresa URL pro přesměrování se pošle zpátky do klienta s kódem stavu 302, který se *našel* .</span><span class="sxs-lookup"><span data-stu-id="a2283-468">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="a2283-469">Prohlížeč vytvoří novou žádost na adrese URL pro přesměrování, která se zobrazí v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a2283-469">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="a2283-470">Vzhledem k tomu, že se žádná pravidla v ukázkové aplikaci neshodují s adresou URL pro přesměrování:</span><span class="sxs-lookup"><span data-stu-id="a2283-470">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="a2283-471">Druhá žádost obdrží odpověď *200-OK* od aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2283-471">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="a2283-472">Tělo odpovědi zobrazuje adresu URL pro přesměrování.</span><span class="sxs-lookup"><span data-stu-id="a2283-472">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="a2283-473">Při *přesměrování*adresy URL se na Server provede cyklická výměna.</span><span class="sxs-lookup"><span data-stu-id="a2283-473">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="a2283-474">Buďte opatrní při vytváření pravidel přesměrování.</span><span class="sxs-lookup"><span data-stu-id="a2283-474">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="a2283-475">Pravidla přesměrování se vyhodnocují při každém požadavku na aplikaci, včetně po přesměrování.</span><span class="sxs-lookup"><span data-stu-id="a2283-475">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="a2283-476">Je snadné vytvořit *smyčku nekonečných přesměrování*.</span><span class="sxs-lookup"><span data-stu-id="a2283-476">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="a2283-477">Původní požadavek: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="a2283-477">Original Request: `/redirect-rule/1234/5678`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="a2283-479">Část výrazu obsažená v závorkách se nazývá *Skupina zachycení*.</span><span class="sxs-lookup"><span data-stu-id="a2283-479">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="a2283-480">Tečka (`.`) výrazu znamená, že *odpovídá libovolnému znaku*.</span><span class="sxs-lookup"><span data-stu-id="a2283-480">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="a2283-481">Hvězdička (`*`) značí, *že se předchozí znak rovná nule nebo vícekrát*.</span><span class="sxs-lookup"><span data-stu-id="a2283-481">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="a2283-482">Proto jsou poslední dva segmenty cesty adresy URL `1234/5678`zachyceny `(.*)`skupiny zachycení.</span><span class="sxs-lookup"><span data-stu-id="a2283-482">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="a2283-483">Jakákoli hodnota, kterou zadáte v adrese URL požadavku po `redirect-rule/`, je zachycena touto jednou skupinou zachycení.</span><span class="sxs-lookup"><span data-stu-id="a2283-483">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="a2283-484">V řetězci pro nahrazení jsou zachycené skupiny vloženy do řetězce s znakem dolaru (`$`) následovaným pořadovým číslem záznamu.</span><span class="sxs-lookup"><span data-stu-id="a2283-484">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="a2283-485">První hodnota skupiny zachycení se získá s `$1`, druhým s `$2`a v posloupnosti pro skupiny zachycení ve vašem regulárním výrazu pokračuje.</span><span class="sxs-lookup"><span data-stu-id="a2283-485">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="a2283-486">Regulární výraz pravidla přesměrování v ukázkové aplikaci obsahuje jenom jednu zachycenou skupinu, takže v náhradním řetězci je jenom jedna vložená skupina, která je `$1`.</span><span class="sxs-lookup"><span data-stu-id="a2283-486">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="a2283-487">Po použití pravidla se adresa URL bude `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="a2283-487">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="a2283-488">Přesměrování adresy URL na zabezpečený koncový bod</span><span class="sxs-lookup"><span data-stu-id="a2283-488">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="a2283-489">Pomocí <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> přesměrovat požadavky HTTP na stejného hostitele a cestu pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a2283-489">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="a2283-490">Pokud kód stavu není zadaný, použije se ve výchozím nastavení middleware *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="a2283-490">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="a2283-491">Pokud port není dodán:</span><span class="sxs-lookup"><span data-stu-id="a2283-491">If the port isn't supplied:</span></span>

* <span data-ttu-id="a2283-492">Výchozí middleware je `null`.</span><span class="sxs-lookup"><span data-stu-id="a2283-492">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="a2283-493">Schéma se změní na `https` (protokol HTTPS) a klient přistupuje k prostředku na portu 443.</span><span class="sxs-lookup"><span data-stu-id="a2283-493">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="a2283-494">Následující příklad ukazuje, jak nastavit stavový kód na *301 – trvale přesunuto* a změnit port na 5001.</span><span class="sxs-lookup"><span data-stu-id="a2283-494">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="a2283-495">Pomocí <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> přesměrovat nezabezpečené požadavky na stejného hostitele a cestu se zabezpečeným protokolem HTTPS na portu 443.</span><span class="sxs-lookup"><span data-stu-id="a2283-495">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="a2283-496">Middleware nastaví stavový kód na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="a2283-496">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="a2283-497">Při přesměrování na zabezpečený koncový bod bez požadavku na další pravidla přesměrování doporučujeme použít middleware pro přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a2283-497">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="a2283-498">Další informace najdete v tématu věnovaném [vykonání protokolu HTTPS](xref:security/enforcing-ssl#require-https) .</span><span class="sxs-lookup"><span data-stu-id="a2283-498">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="a2283-499">Ukázková aplikace dokáže demonstrovat použití `AddRedirectToHttps` nebo `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="a2283-499">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="a2283-500">Přidejte metodu rozšíření do `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="a2283-500">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="a2283-501">Vytvořte nezabezpečenou žádost o aplikaci na libovolné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="a2283-501">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="a2283-502">Zrušte upozornění zabezpečení prohlížeče, že certifikát podepsaný svým držitelem je nedůvěryhodný, nebo vytvořte výjimku pro důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="a2283-502">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="a2283-503">Původní požadavek pomocí `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="a2283-503">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="a2283-505">Původní požadavek pomocí `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="a2283-505">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="a2283-507">Přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="a2283-507">URL rewrite</span></span>

<span data-ttu-id="a2283-508">Pomocí <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> vytvořit pravidlo pro přepis adres URL.</span><span class="sxs-lookup"><span data-stu-id="a2283-508">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="a2283-509">První parametr obsahuje regulární výraz pro porovnání na cestě příchozích adres URL.</span><span class="sxs-lookup"><span data-stu-id="a2283-509">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="a2283-510">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="a2283-510">The second parameter is the replacement string.</span></span> <span data-ttu-id="a2283-511">Třetí parametr, `skipRemainingRules: {true|false}`, označuje middleware bez ohledu na to, jestli se má použít aktuální pravidlo, nebo jestli se mají přeskočit další pravidla přepsání.</span><span class="sxs-lookup"><span data-stu-id="a2283-511">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="a2283-512">Původní požadavek: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="a2283-512">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="a2283-514">Kosočtverce (`^`) na začátku výrazu znamená, že shoda začíná na začátku cesty URL.</span><span class="sxs-lookup"><span data-stu-id="a2283-514">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="a2283-515">V předchozím příkladu s pravidlem přesměrování `redirect-rule/(.*)`neexistuje kosočtverce (`^`) na začátku regulárního výrazu.</span><span class="sxs-lookup"><span data-stu-id="a2283-515">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="a2283-516">Proto libovolné znaky mohou předcházet `redirect-rule/` v cestě pro úspěšnou shodu.</span><span class="sxs-lookup"><span data-stu-id="a2283-516">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="a2283-517">Cesta</span><span class="sxs-lookup"><span data-stu-id="a2283-517">Path</span></span>                               | <span data-ttu-id="a2283-518">Shoda</span><span class="sxs-lookup"><span data-stu-id="a2283-518">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="a2283-519">Ano</span><span class="sxs-lookup"><span data-stu-id="a2283-519">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="a2283-520">Ano</span><span class="sxs-lookup"><span data-stu-id="a2283-520">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="a2283-521">Ano</span><span class="sxs-lookup"><span data-stu-id="a2283-521">Yes</span></span>   |

<span data-ttu-id="a2283-522">Pravidlo přepisu, `^rewrite-rule/(\d+)/(\d+)`, odpovídá pouze cestám, pokud začínají na `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="a2283-522">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="a2283-523">V následující tabulce si všimněte rozdílu v porovnání.</span><span class="sxs-lookup"><span data-stu-id="a2283-523">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="a2283-524">Cesta</span><span class="sxs-lookup"><span data-stu-id="a2283-524">Path</span></span>                              | <span data-ttu-id="a2283-525">Shoda</span><span class="sxs-lookup"><span data-stu-id="a2283-525">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="a2283-526">Ano</span><span class="sxs-lookup"><span data-stu-id="a2283-526">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="a2283-527">Ne</span><span class="sxs-lookup"><span data-stu-id="a2283-527">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="a2283-528">Ne</span><span class="sxs-lookup"><span data-stu-id="a2283-528">No</span></span>    |

<span data-ttu-id="a2283-529">Po `^rewrite-rule/` části výrazu jsou k dispozici dvě skupiny zachycení `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="a2283-529">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="a2283-530">`\d` značí *shodu s číslicí (číslem)* .</span><span class="sxs-lookup"><span data-stu-id="a2283-530">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="a2283-531">Znaménko plus (`+`) znamená, že se *shoduje s jedním nebo více předcházejícím znakem*.</span><span class="sxs-lookup"><span data-stu-id="a2283-531">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="a2283-532">Proto musí adresa URL obsahovat číslo následované lomítkem, za kterým následuje jiné číslo.</span><span class="sxs-lookup"><span data-stu-id="a2283-532">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="a2283-533">Tyto skupiny zachycení jsou vloženy do přepsané adresy URL jako `$1` a `$2`.</span><span class="sxs-lookup"><span data-stu-id="a2283-533">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="a2283-534">Náhradní řetězec pravidla přepsaní umístí zachycené skupiny do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="a2283-534">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="a2283-535">Požadovaná cesta `/rewrite-rule/1234/5678` je přepsána za účelem získání prostředku v `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="a2283-535">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="a2283-536">Pokud se v původním požadavku nachází řetězec dotazu, bude při přepisování adresy URL zachován.</span><span class="sxs-lookup"><span data-stu-id="a2283-536">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="a2283-537">K získání prostředku neexistuje žádná Přenosová cesta k serveru.</span><span class="sxs-lookup"><span data-stu-id="a2283-537">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="a2283-538">Pokud prostředek existuje, načte se a vrátí do klienta s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="a2283-538">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="a2283-539">Vzhledem k tomu, že klient není přesměrován, adresa URL v adresním řádku prohlížeče se nezmění.</span><span class="sxs-lookup"><span data-stu-id="a2283-539">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="a2283-540">Klienti nerozpoznají, že na serveru došlo k operaci přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="a2283-540">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="a2283-541">Použijte `skipRemainingRules: true` kdykoli je to možné, protože vyhovující pravidla jsou výpočetně náročná a zvyšují dobu odezvy aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2283-541">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="a2283-542">Pro nejrychlejší aplikační odpověď:</span><span class="sxs-lookup"><span data-stu-id="a2283-542">For the fastest app response:</span></span>
>
> * <span data-ttu-id="a2283-543">Seřazení pravidel přepisu z nejčastěji spárovaného pravidla na nejméně často spárované pravidlo.</span><span class="sxs-lookup"><span data-stu-id="a2283-543">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="a2283-544">Pokud dojde ke shodě a není vyžadováno žádné další zpracování pravidla, přeskočte zpracování zbývajících pravidel.</span><span class="sxs-lookup"><span data-stu-id="a2283-544">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="a2283-545">Mod_rewrite Apache</span><span class="sxs-lookup"><span data-stu-id="a2283-545">Apache mod_rewrite</span></span>

<span data-ttu-id="a2283-546">Použijte pravidla mod_rewrite Apache s <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="a2283-546">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="a2283-547">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="a2283-547">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="a2283-548">Další informace a příklady pravidel mod_rewrite najdete v tématu [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="a2283-548">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="a2283-549"><xref:System.IO.StreamReader> se používá ke čtení pravidel ze souboru pravidel *ApacheModRewrite. txt* :</span><span class="sxs-lookup"><span data-stu-id="a2283-549">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="a2283-550">Ukázková aplikace přesměrovává požadavky od `/apache-mod-rules-redirect/(.\*)` do `/redirected?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="a2283-550">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="a2283-551">Stavový kód odpovědi je *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="a2283-551">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="a2283-552">Původní požadavek: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="a2283-552">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="a2283-554">Middleware podporuje následující proměnné serveru Apache mod_rewrite:</span><span class="sxs-lookup"><span data-stu-id="a2283-554">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="a2283-555">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="a2283-555">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="a2283-556">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="a2283-556">HTTP_ACCEPT</span></span>
* <span data-ttu-id="a2283-557">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="a2283-557">HTTP_CONNECTION</span></span>
* <span data-ttu-id="a2283-558">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="a2283-558">HTTP_COOKIE</span></span>
* <span data-ttu-id="a2283-559">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="a2283-559">HTTP_FORWARDED</span></span>
* <span data-ttu-id="a2283-560">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="a2283-560">HTTP_HOST</span></span>
* <span data-ttu-id="a2283-561">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="a2283-561">HTTP_REFERER</span></span>
* <span data-ttu-id="a2283-562">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="a2283-562">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="a2283-563">HTTPS</span><span class="sxs-lookup"><span data-stu-id="a2283-563">HTTPS</span></span>
* <span data-ttu-id="a2283-564">IPV6</span><span class="sxs-lookup"><span data-stu-id="a2283-564">IPV6</span></span>
* <span data-ttu-id="a2283-565">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="a2283-565">QUERY_STRING</span></span>
* <span data-ttu-id="a2283-566">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="a2283-566">REMOTE_ADDR</span></span>
* <span data-ttu-id="a2283-567">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="a2283-567">REMOTE_PORT</span></span>
* <span data-ttu-id="a2283-568">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="a2283-568">REQUEST_FILENAME</span></span>
* <span data-ttu-id="a2283-569">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="a2283-569">REQUEST_METHOD</span></span>
* <span data-ttu-id="a2283-570">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="a2283-570">REQUEST_SCHEME</span></span>
* <span data-ttu-id="a2283-571">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="a2283-571">REQUEST_URI</span></span>
* <span data-ttu-id="a2283-572">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="a2283-572">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="a2283-573">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="a2283-573">SERVER_ADDR</span></span>
* <span data-ttu-id="a2283-574">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="a2283-574">SERVER_PORT</span></span>
* <span data-ttu-id="a2283-575">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="a2283-575">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="a2283-576">ČAS</span><span class="sxs-lookup"><span data-stu-id="a2283-576">TIME</span></span>
* <span data-ttu-id="a2283-577">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="a2283-577">TIME_DAY</span></span>
* <span data-ttu-id="a2283-578">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="a2283-578">TIME_HOUR</span></span>
* <span data-ttu-id="a2283-579">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="a2283-579">TIME_MIN</span></span>
* <span data-ttu-id="a2283-580">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="a2283-580">TIME_MON</span></span>
* <span data-ttu-id="a2283-581">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="a2283-581">TIME_SEC</span></span>
* <span data-ttu-id="a2283-582">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="a2283-582">TIME_WDAY</span></span>
* <span data-ttu-id="a2283-583">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="a2283-583">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="a2283-584">Pravidla pro přepsání adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="a2283-584">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="a2283-585">Pokud chcete použít stejnou sadu pravidel, která se vztahuje na modul IIS URL Rewrite, použijte <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="a2283-585">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="a2283-586">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="a2283-586">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="a2283-587">Nesměrujte middleware na použití souboru *Web. config* aplikace při spuštění v systému Windows Server IIS.</span><span class="sxs-lookup"><span data-stu-id="a2283-587">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="a2283-588">V případě služby IIS by tato pravidla měla být uložena mimo soubor *Web. config* aplikace, aby nedocházelo ke konfliktům s modulem pro přepis služby IIS.</span><span class="sxs-lookup"><span data-stu-id="a2283-588">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="a2283-589">Další informace a příklady pravidel pro přepis adres URL služby IIS najdete v tématu použití odkazu na modul pro [přepis adres url 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [odkaz na konfiguraci modulu přepisu adresy](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)URL.</span><span class="sxs-lookup"><span data-stu-id="a2283-589">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="a2283-590">Pro čtení pravidel ze souboru pravidel *IISUrlRewrite. XML* se používá <xref:System.IO.StreamReader>:</span><span class="sxs-lookup"><span data-stu-id="a2283-590">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="a2283-591">Ukázková aplikace přepíše požadavky od `/iis-rules-rewrite/(.*)` do `/rewritten?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="a2283-591">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="a2283-592">Odpověď se pošle klientovi s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="a2283-592">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="a2283-593">Původní požadavek: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="a2283-593">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="a2283-595">Pokud máte aktivní modul pro přepis služby IIS s nakonfigurovanými pravidly na úrovni serveru, který by ovlivnil vaši aplikaci nežádoucím způsobem, můžete pro aplikaci zakázat modul IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="a2283-595">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="a2283-596">Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="a2283-596">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="a2283-597">Nepodporované funkce</span><span class="sxs-lookup"><span data-stu-id="a2283-597">Unsupported features</span></span>

<span data-ttu-id="a2283-598">Middleware vydaná ASP.NET Core 2. x nepodporuje následující funkce modulu pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="a2283-598">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="a2283-599">Pravidla pro odchozí provoz</span><span class="sxs-lookup"><span data-stu-id="a2283-599">Outbound Rules</span></span>
* <span data-ttu-id="a2283-600">Vlastní proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="a2283-600">Custom Server Variables</span></span>
* <span data-ttu-id="a2283-601">Zástupné znaky</span><span class="sxs-lookup"><span data-stu-id="a2283-601">Wildcards</span></span>
* <span data-ttu-id="a2283-602">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="a2283-602">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="a2283-603">Podporované proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="a2283-603">Supported server variables</span></span>

<span data-ttu-id="a2283-604">Middleware podporuje následující proměnné serveru pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="a2283-604">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="a2283-605">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="a2283-605">CONTENT_LENGTH</span></span>
* <span data-ttu-id="a2283-606">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="a2283-606">CONTENT_TYPE</span></span>
* <span data-ttu-id="a2283-607">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="a2283-607">HTTP_ACCEPT</span></span>
* <span data-ttu-id="a2283-608">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="a2283-608">HTTP_CONNECTION</span></span>
* <span data-ttu-id="a2283-609">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="a2283-609">HTTP_COOKIE</span></span>
* <span data-ttu-id="a2283-610">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="a2283-610">HTTP_HOST</span></span>
* <span data-ttu-id="a2283-611">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="a2283-611">HTTP_REFERER</span></span>
* <span data-ttu-id="a2283-612">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="a2283-612">HTTP_URL</span></span>
* <span data-ttu-id="a2283-613">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="a2283-613">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="a2283-614">HTTPS</span><span class="sxs-lookup"><span data-stu-id="a2283-614">HTTPS</span></span>
* <span data-ttu-id="a2283-615">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="a2283-615">LOCAL_ADDR</span></span>
* <span data-ttu-id="a2283-616">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="a2283-616">QUERY_STRING</span></span>
* <span data-ttu-id="a2283-617">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="a2283-617">REMOTE_ADDR</span></span>
* <span data-ttu-id="a2283-618">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="a2283-618">REMOTE_PORT</span></span>
* <span data-ttu-id="a2283-619">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="a2283-619">REQUEST_FILENAME</span></span>
* <span data-ttu-id="a2283-620">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="a2283-620">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="a2283-621"><xref:Microsoft.Extensions.FileProviders.IFileProvider> můžete také získat pomocí <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="a2283-621">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="a2283-622">Tento přístup může poskytovat větší flexibilitu pro umístění souborů pravidel přepisu.</span><span class="sxs-lookup"><span data-stu-id="a2283-622">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="a2283-623">Ujistěte se, že jsou v cestě, kterou zadáte, nasazené soubory pravidel přepisu na server.</span><span class="sxs-lookup"><span data-stu-id="a2283-623">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="a2283-624">Pravidlo založené na metodách</span><span class="sxs-lookup"><span data-stu-id="a2283-624">Method-based rule</span></span>

<span data-ttu-id="a2283-625">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k implementaci logiky pravidla v metodě.</span><span class="sxs-lookup"><span data-stu-id="a2283-625">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="a2283-626">`Add` zpřístupňuje <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, která zpřístupňuje <xref:Microsoft.AspNetCore.Http.HttpContext> pro použití ve vaší metodě.</span><span class="sxs-lookup"><span data-stu-id="a2283-626">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="a2283-627">[RewriteContext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) určuje, jak se zpracovává další zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="a2283-627">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="a2283-628">Nastavte hodnotu na jedno z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> polí popsaných v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="a2283-628">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="a2283-629">Akce</span><span class="sxs-lookup"><span data-stu-id="a2283-629">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="a2283-630">`RuleResult.ContinueRules` (výchozí)</span><span class="sxs-lookup"><span data-stu-id="a2283-630">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="a2283-631">Pokračovat v používání pravidel.</span><span class="sxs-lookup"><span data-stu-id="a2283-631">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="a2283-632">Zastavení použití pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a2283-632">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="a2283-633">Zastavení použití pravidel a odeslání kontextu do dalšího middlewaru.</span><span class="sxs-lookup"><span data-stu-id="a2283-633">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="a2283-634">Ukázková aplikace ukazuje metodu, která přesměrovává požadavky na cesty, které končí na *. XML*.</span><span class="sxs-lookup"><span data-stu-id="a2283-634">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="a2283-635">Pokud se pro `/file.xml`požadavek provede, je žádost přesměrována na `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="a2283-635">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="a2283-636">Stavový kód je nastaven na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="a2283-636">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="a2283-637">Když prohlížeč vytvoří nový požadavek na */xmlfiles/File.XML*, soubor middleware statických souborů zachová soubor klientovi ze složky *wwwroot/xmlfiles* .</span><span class="sxs-lookup"><span data-stu-id="a2283-637">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="a2283-638">Pro přesměrování explicitně nastavte stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a2283-638">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="a2283-639">V opačném případě se vrátí stavový kód *200-OK* a na klientovi se neobjeví přesměrování.</span><span class="sxs-lookup"><span data-stu-id="a2283-639">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="a2283-640">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="a2283-640">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="a2283-641">Tento přístup může také přezapisovat požadavky.</span><span class="sxs-lookup"><span data-stu-id="a2283-641">This approach can also rewrite requests.</span></span> <span data-ttu-id="a2283-642">Ukázková aplikace ukazuje přepis cesty k libovolné žádosti o textový soubor, aby sloužil textový soubor *. txt* ze složky *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="a2283-642">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="a2283-643">Middleware statických souborů zachovává soubor na základě aktualizované cesty požadavku:</span><span class="sxs-lookup"><span data-stu-id="a2283-643">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="a2283-644">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="a2283-644">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="a2283-645">Pravidlo založené na IRule</span><span class="sxs-lookup"><span data-stu-id="a2283-645">IRule-based rule</span></span>

<span data-ttu-id="a2283-646">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k použití logiky pravidla ve třídě, která implementuje rozhraní <xref:Microsoft.AspNetCore.Rewrite.IRule>.</span><span class="sxs-lookup"><span data-stu-id="a2283-646">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="a2283-647">`IRule` poskytuje větší flexibilitu při používání přístupu k pravidlům založeným na metodách.</span><span class="sxs-lookup"><span data-stu-id="a2283-647">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="a2283-648">Vaše třída implementace může zahrnovat konstruktor, který umožňuje předat parametry pro metodu <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.</span><span class="sxs-lookup"><span data-stu-id="a2283-648">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="a2283-649">Hodnoty parametrů v ukázkové aplikaci pro `extension` a `newPath` jsou kontrolovány, aby splňovaly několik podmínek.</span><span class="sxs-lookup"><span data-stu-id="a2283-649">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="a2283-650">`extension` musí obsahovat hodnotu a hodnota musí být *. png*, *. jpg*nebo *. gif*.</span><span class="sxs-lookup"><span data-stu-id="a2283-650">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="a2283-651">Pokud `newPath` není platný, je vyvolána <xref:System.ArgumentException>.</span><span class="sxs-lookup"><span data-stu-id="a2283-651">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="a2283-652">Pokud je pro *image. png*vytvořen požadavek, je požadavek přesměrován na `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="a2283-652">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="a2283-653">Pokud je pro *image. jpg*vytvořen požadavek, je požadavek přesměrován na `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="a2283-653">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="a2283-654">Stavový kód je nastaven na *301 – trvale přesunuto*a `context.Result` je nastavené na zastavení zpracování pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a2283-654">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="a2283-655">Původní požadavek: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="a2283-655">Original Request: `/image.png`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image. png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="a2283-657">Původní požadavek: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="a2283-657">Original Request: `/image.jpg`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image. jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="a2283-659">Příklady Regex</span><span class="sxs-lookup"><span data-stu-id="a2283-659">Regex examples</span></span>

| <span data-ttu-id="a2283-660">Cíl</span><span class="sxs-lookup"><span data-stu-id="a2283-660">Goal</span></span> | <span data-ttu-id="a2283-661">& Řetězce regulárního výrazu</span><span class="sxs-lookup"><span data-stu-id="a2283-661">Regex String &</span></span><br><span data-ttu-id="a2283-662">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="a2283-662">Match Example</span></span> | <span data-ttu-id="a2283-663">& Řetězce nahrazení</span><span class="sxs-lookup"><span data-stu-id="a2283-663">Replacement String &</span></span><br><span data-ttu-id="a2283-664">Příklad výstupu</span><span class="sxs-lookup"><span data-stu-id="a2283-664">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="a2283-665">Přepište cestu do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="a2283-665">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="a2283-666">Koncové lomítko pruhu</span><span class="sxs-lookup"><span data-stu-id="a2283-666">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="a2283-667">Vymáhat koncové lomítko</span><span class="sxs-lookup"><span data-stu-id="a2283-667">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="a2283-668">Vyhnout se přepisu konkrétních požadavků</span><span class="sxs-lookup"><span data-stu-id="a2283-668">Avoid rewriting specific requests</span></span> | <span data-ttu-id="a2283-669">`^(.*)(?<!\.axd)$` nebo `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="a2283-669">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="a2283-670">Ano: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="a2283-670">Yes: `/resource.htm`</span></span><br><span data-ttu-id="a2283-671">Ne: `/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="a2283-671">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="a2283-672">Změna uspořádání segmentů adresy URL</span><span class="sxs-lookup"><span data-stu-id="a2283-672">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="a2283-673">Nahradit segment adresy URL</span><span class="sxs-lookup"><span data-stu-id="a2283-673">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a2283-674">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a2283-674">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="a2283-675">Regulární výrazy v .NET</span><span class="sxs-lookup"><span data-stu-id="a2283-675">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="a2283-676">Jazyk regulárních výrazů – stručná referenční dokumentace</span><span class="sxs-lookup"><span data-stu-id="a2283-676">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="a2283-677">Mod_rewrite Apache</span><span class="sxs-lookup"><span data-stu-id="a2283-677">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="a2283-678">Použití modulu URL pro přepis 2,0 (pro IIS)</span><span class="sxs-lookup"><span data-stu-id="a2283-678">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="a2283-679">Odkaz na konfiguraci modulu pro přepis adresy URL</span><span class="sxs-lookup"><span data-stu-id="a2283-679">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="a2283-680">Fórum modulu pro přepsání adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="a2283-680">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="a2283-681">Zachování jednoduché struktury URL</span><span class="sxs-lookup"><span data-stu-id="a2283-681">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="a2283-682">10 tipů a triky pro přepis adres URL</span><span class="sxs-lookup"><span data-stu-id="a2283-682">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="a2283-683">Na lomítko nebo ne na lomítko</span><span class="sxs-lookup"><span data-stu-id="a2283-683">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
