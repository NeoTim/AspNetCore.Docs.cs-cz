---
title: Middleware v ASP.NET Core přepisování adres URL
author: guardrex
description: Další informace o adrese URL přepsání a přesměrování s Middleware pro přepis adres URL v aplikacích ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/19/2018
uid: fundamentals/url-rewriting
ms.openlocfilehash: 84052789717738a48c346d35d1a2642017a9ab93
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861911"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="15054-103">Middleware v ASP.NET Core přepisování adres URL</span><span class="sxs-lookup"><span data-stu-id="15054-103">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="15054-104">Podle [Luke Latham](https://github.com/guardrex) a [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="15054-104">By [Luke Latham](https://github.com/guardrex) and [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="15054-105">1.1 verzi tohoto tématu, stáhněte si [Middleware pro přepis adres URL v ASP.NET Core (verze 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/URL_Rewriting_1.1.pdf).</span><span class="sxs-lookup"><span data-stu-id="15054-105">For the 1.1 version of this topic, download [URL Rewriting Middleware in ASP.NET Core (version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/URL_Rewriting_1.1.pdf).</span></span>

::: moniker-end

<span data-ttu-id="15054-106">Toto téma představuje přepisování adres URL s pokyny, jak používat Middleware pro přepis adres URL v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="15054-106">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="15054-107">Přepisování adres URL je úprava žádosti o adresy URL v závislosti na jeden nebo více předdefinovaných pravidel.</span><span class="sxs-lookup"><span data-stu-id="15054-107">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="15054-108">Přepisování adres URL vytvoří abstrakci mezi umístění prostředků a jejich adresy tak, aby umístění a adresy nejsou připojeny úzce.</span><span class="sxs-lookup"><span data-stu-id="15054-108">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="15054-109">Přepisování adres URL je užitečné v několika scénářích pro:</span><span class="sxs-lookup"><span data-stu-id="15054-109">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="15054-110">Přesuňte nebo nahradit server zdroje dočasně nebo trvale a udržovat stabilní lokátory pro tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="15054-110">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="15054-111">Rozdělte zpracování požadavků v různých aplikací nebo víc oblastech jednu aplikaci.</span><span class="sxs-lookup"><span data-stu-id="15054-111">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="15054-112">Odebrat, přidat nebo změnit uspořádání segmenty adres URL na příchozí požadavky.</span><span class="sxs-lookup"><span data-stu-id="15054-112">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="15054-113">Optimalizujte veřejné adresy URL pro hledání modulu optimalizace (SEO).</span><span class="sxs-lookup"><span data-stu-id="15054-113">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="15054-114">Povolit používání popisný veřejné adresy URL usnadnit návštěvníkům předpovědět, můžete si vyžádat prostředek vrácený obsah.</span><span class="sxs-lookup"><span data-stu-id="15054-114">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="15054-115">Přesměrujte nezabezpečené požadavky do zabezpečené koncové body.</span><span class="sxs-lookup"><span data-stu-id="15054-115">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="15054-116">Zabraňte hotlinking, kde externí web používá prostředí statické prostředku v jiné lokalitě propojením prostředku do jeho vlastní obsah.</span><span class="sxs-lookup"><span data-stu-id="15054-116">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="15054-117">Přepisování adres URL, může snížit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="15054-117">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="15054-118">Tam, kde je to možné, omezte počet a složitost pravidel.</span><span class="sxs-lookup"><span data-stu-id="15054-118">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="15054-119">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="15054-119">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="15054-120">Adresa URL pro přesměrování a URL revize</span><span class="sxs-lookup"><span data-stu-id="15054-120">URL redirect and URL rewrite</span></span>

<span data-ttu-id="15054-121">Rozdíl mezi formulace *adresy URL přesměrování* a *přepsání adresy URL* je jednoduchý, ale má vliv na důležité pro poskytování prostředků pro klienty.</span><span class="sxs-lookup"><span data-stu-id="15054-121">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="15054-122">Middleware pro přepis adres URL ASP.NET Core je schopen splnit potřebu obojí.</span><span class="sxs-lookup"><span data-stu-id="15054-122">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="15054-123">A *adresy URL přesměrování* zahrnuje operace na straně klienta, kde klientovi je odeslán pokyn k přístupu k prostředku na jinou adresu než klient původně požadována.</span><span class="sxs-lookup"><span data-stu-id="15054-123">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="15054-124">To vyžaduje odezvy serveru.</span><span class="sxs-lookup"><span data-stu-id="15054-124">This requires a round trip to the server.</span></span> <span data-ttu-id="15054-125">Adresa URL přesměrování, který je vrácen do klienta se zobrazí v adresním řádku prohlížeče, pokud klient odešle novou žádost o prostředku.</span><span class="sxs-lookup"><span data-stu-id="15054-125">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="15054-126">Pokud `/resource` je *přesměrováno* k `/different-resource`, tento server odpoví, že klient by měl získat prostředek na `/different-resource` s kód stavu označující, že přesměrování je dočasný nebo trvalý.</span><span class="sxs-lookup"><span data-stu-id="15054-126">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Koncový bod služby WebAPI dočasně změnila z verze 1 (v1) verze 2 (v2) na serveru.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="15054-132">Při přesměrování požadavků na jinou adresu URL, označuje, zda přesměrování trvalé nebo dočasné zadáním stavový kód s odpovědí:</span><span class="sxs-lookup"><span data-stu-id="15054-132">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="15054-133">*301 - trvale přesunuto* stavový kód se používá ve kterém prostředek má novou, trvalé adresy URL a chcete, aby dáte pokyn, aby klient, všechny budoucí žádosti o prostředku by měl použít novou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="15054-133">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="15054-134">*Klient může ukládat do mezipaměti a opakovaně používat odpovědi, když je obdržena 301 stavový kód.*</span><span class="sxs-lookup"><span data-stu-id="15054-134">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="15054-135">*302 - nalezen* stavový kód se používá v případě přesměrování je dočasný nebo obecně změnit.</span><span class="sxs-lookup"><span data-stu-id="15054-135">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="15054-136">302 stavový kód označuje klientovi nechcete uložit adresu URL a budoucí použití.</span><span class="sxs-lookup"><span data-stu-id="15054-136">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="15054-137">Další informace o stavových kódů, najdete v části [dokumentu RFC 2616: definice stavových kódů](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="15054-137">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="15054-138">A *přepsání adresy URL* je operace na straně serveru, který poskytuje prostředek z adres různých prostředků než vyžádáno klientem.</span><span class="sxs-lookup"><span data-stu-id="15054-138">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="15054-139">Přepisování adres URL nevyžaduje odezvy serveru.</span><span class="sxs-lookup"><span data-stu-id="15054-139">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="15054-140">Přepsaný adresa URL není vrácen do klienta a nebude zobrazovat v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="15054-140">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="15054-141">Pokud `/resource` je *přepsán* k `/different-resource`, server *interně* načte a vrátí prostředek na `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="15054-141">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="15054-142">I když se klient může být schopný načíst prostředek na přepsaný adresu URL, není klient informován, že prostředky existují na přepsaný adrese URL, když vytvoří jeho žádost a obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="15054-142">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Koncový bod služby WebAPI se změnil z verze 1 (v1) verze 2 (v2) na serveru.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="15054-147">Ukázková aplikace přepis adres URL</span><span class="sxs-lookup"><span data-stu-id="15054-147">URL rewriting sample app</span></span>

<span data-ttu-id="15054-148">Můžete prozkoumat funkce Middleware přepisování adres URL se [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="15054-148">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="15054-149">Aplikace použije pro přesměrování a přepsání pravidel a zobrazuje přesměrované nebo přepsaný adresu URL pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="15054-149">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="15054-150">Kdy použít Middleware pro přepis adres URL</span><span class="sxs-lookup"><span data-stu-id="15054-150">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="15054-151">Middleware pro přepis adres URL používejte, až budete moci pomocí následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="15054-151">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="15054-152">Modul přepisování adres URL se službou IIS v systému Windows Server</span><span class="sxs-lookup"><span data-stu-id="15054-152">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="15054-153">Apache mod_rewrite modulu na serveru Apache</span><span class="sxs-lookup"><span data-stu-id="15054-153">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="15054-154">Na serveru Nginx přepisování adres URL</span><span class="sxs-lookup"><span data-stu-id="15054-154">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="15054-155">Navíc používat middleware, když je aplikace hostovaná na [serveru HTTP.sys](xref:fundamentals/servers/httpsys) (dříve se označovaly jako [WebListener](xref:fundamentals/servers/weblistener)).</span><span class="sxs-lookup"><span data-stu-id="15054-155">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called [WebListener](xref:fundamentals/servers/weblistener)).</span></span>

<span data-ttu-id="15054-156">Hlavní důvody pro použití serverem přepisování adres URL technologií ve službě IIS, Apache a Nginx jsou:</span><span class="sxs-lookup"><span data-stu-id="15054-156">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="15054-157">Middleware nepodporuje úplné funkce aplikace tyto moduly.</span><span class="sxs-lookup"><span data-stu-id="15054-157">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="15054-158">Některé funkce serveru moduly nechcete pracovat s projekty ASP.NET Core, jako `IsFile` a `IsDirectory` omezení modulu IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="15054-158">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="15054-159">V těchto scénářích platí místo toho použijte middleware.</span><span class="sxs-lookup"><span data-stu-id="15054-159">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="15054-160">Výkon middleware pravděpodobně neodpovídá počtu moduly.</span><span class="sxs-lookup"><span data-stu-id="15054-160">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="15054-161">Srovnávací testy je jediný způsob, jak zjistit poprvé jaký přístup nejvíce snižuje výkon, nebo pokud snížení výkonu zanedbatelné.</span><span class="sxs-lookup"><span data-stu-id="15054-161">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="15054-162">Balíček</span><span class="sxs-lookup"><span data-stu-id="15054-162">Package</span></span>

<span data-ttu-id="15054-163">Aby byly middleware ve vašem projektu, přidejte odkaz na balíček pro [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) v souboru projektu, který obsahuje [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) balíčku.</span><span class="sxs-lookup"><span data-stu-id="15054-163">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="15054-164">Pokud nepoužíváte `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all, přidejte odkaz na projekt `Microsoft.AspNetCore.Rewrite` balíčku.</span><span class="sxs-lookup"><span data-stu-id="15054-164">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="15054-165">Rozšíření a možnosti</span><span class="sxs-lookup"><span data-stu-id="15054-165">Extension and options</span></span>

<span data-ttu-id="15054-166">Vytvořit přepsání adresy URL a přesměrovat pravidla tak, že vytvoříte instanci [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) třída s atributem rozšiřující metody pro každý z vašich pravidlech přepisování.</span><span class="sxs-lookup"><span data-stu-id="15054-166">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="15054-167">Zřetězit více pravidel v pořadí, které chcete je zpracována.</span><span class="sxs-lookup"><span data-stu-id="15054-167">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="15054-168">`RewriteOptions` Jsou předány do Middleware pro přepis adres URL, protože se přidá do kanálu požadavku s <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span><span class="sxs-lookup"><span data-stu-id="15054-168">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="15054-169">Přesměrovat na www webové služby.</span><span class="sxs-lookup"><span data-stu-id="15054-169">Redirect non-www to www</span></span>

<span data-ttu-id="15054-170">Povolení aplikace pro přesměrování jinou hodnotu než tři možnosti`www` vyžádá, aby `www`:</span><span class="sxs-lookup"><span data-stu-id="15054-170">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="15054-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Požadavek na trvalé přesměrování `www` subdomény, pokud je požadavek jinou hodnotu než`www`.</span><span class="sxs-lookup"><span data-stu-id="15054-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="15054-172">Přesměruje se [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) stavový kód.</span><span class="sxs-lookup"><span data-stu-id="15054-172">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="15054-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Požadavek na přesměrování `www` subdomény, pokud je příchozí požadavek jinou hodnotu než`www`.</span><span class="sxs-lookup"><span data-stu-id="15054-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="15054-174">Přesměruje se [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) stavový kód.</span><span class="sxs-lookup"><span data-stu-id="15054-174">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="15054-175">Přetížení umožňuje stanovit stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="15054-175">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="15054-176">Použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídu pro přiřazení kódu stavu.</span><span class="sxs-lookup"><span data-stu-id="15054-176">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="15054-177">Adresa URL pro přesměrování</span><span class="sxs-lookup"><span data-stu-id="15054-177">URL redirect</span></span>

<span data-ttu-id="15054-178">Použití <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> pro přesměrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="15054-178">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="15054-179">První parametr obsahuje vaše regulárního výrazu pro porovnávání v cestě adresy URL příchozích.</span><span class="sxs-lookup"><span data-stu-id="15054-179">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="15054-180">Druhý parametr je řetězci pro nahrazení.</span><span class="sxs-lookup"><span data-stu-id="15054-180">The second parameter is the replacement string.</span></span> <span data-ttu-id="15054-181">Třetí parametr, pokud jsou k dispozici, určuje kód stavu.</span><span class="sxs-lookup"><span data-stu-id="15054-181">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="15054-182">Pokud nezadáte kód stavu, stavový kód výchozí *302 - nalezen*, což znamená, že prostředek je dočasně nepřesunul ani nahradit.</span><span class="sxs-lookup"><span data-stu-id="15054-182">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="15054-183">V prohlížeči pomocí nástrojů pro vývojáře, vytvořte žádost na ukázkovou aplikaci s cestou `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="15054-183">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="15054-184">Odpovídá regulárnímu cestu požadavku na `redirect-rule/(.*)`, a cesta se nahradí `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="15054-184">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="15054-185">Přesměrování URL budou odeslána zpět do klienta se *302 - nalezen* stavový kód.</span><span class="sxs-lookup"><span data-stu-id="15054-185">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="15054-186">Prohlížeč odešle nový požadavek na adrese URL přesměrování, které se zobrazí v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="15054-186">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="15054-187">Protože žádná pravidla v aplikaci vzorek se vyhledala shoda podle adresy URL pro přesměrování:</span><span class="sxs-lookup"><span data-stu-id="15054-187">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="15054-188">Druhý požadavek přijme *200 – OK* odezvu aplikace.</span><span class="sxs-lookup"><span data-stu-id="15054-188">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="15054-189">Text odpovědi zobrazí adresu URL přesměrování.</span><span class="sxs-lookup"><span data-stu-id="15054-189">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="15054-190">A odezvy se provádí na server, když je adresa URL *přesměrováno*.</span><span class="sxs-lookup"><span data-stu-id="15054-190">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="15054-191">Buďte opatrní při vytváření pravidla pro přesměrování.</span><span class="sxs-lookup"><span data-stu-id="15054-191">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="15054-192">U každého požadavku do aplikace, včetně po přesměrování se vyhodnocují pravidla pro přesměrování.</span><span class="sxs-lookup"><span data-stu-id="15054-192">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="15054-193">Je snadné vytvořit náhodou *smyčky nekonečné přesměrování*.</span><span class="sxs-lookup"><span data-stu-id="15054-193">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="15054-194">Původní žádosti: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="15054-194">Original Request: `/redirect-rule/1234/5678`</span></span>

![Okno prohlížeče s vývojářskými nástroji pro sledování požadavků a odpovědí](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="15054-196">Část výraz v závorkách se nazývá *skupina zachycení*.</span><span class="sxs-lookup"><span data-stu-id="15054-196">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="15054-197">Tečka (`.`) výrazu znamená, že *odpovídá jakémukoli znaku*.</span><span class="sxs-lookup"><span data-stu-id="15054-197">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="15054-198">Hvězdička (`*`) označuje *odpovídá předchozímu znaku nula nebo vícekrát*.</span><span class="sxs-lookup"><span data-stu-id="15054-198">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="15054-199">Proto segmenty poslední dvě cesty adresy URL, `1234/5678`, jsou zachyceny na základě skupina zachycení `(.*)`.</span><span class="sxs-lookup"><span data-stu-id="15054-199">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="15054-200">Libovolná hodnota je zadat v adrese URL požadavku po `redirect-rule/` zachycena této skupiny jediné zachycení.</span><span class="sxs-lookup"><span data-stu-id="15054-200">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="15054-201">V řetězci pro nahrazení zachycené skupiny jsou vloženy do řetězce bez znak dolaru (`$`) následované pořadové číslo pro zachytávání.</span><span class="sxs-lookup"><span data-stu-id="15054-201">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="15054-202">Hodnota první skupiny zachycení se získá pomocí `$1`, druhý s `$2`, a budou pokračovat v práci v pořadí pro zachycení skupiny ve vaší regulární výraz.</span><span class="sxs-lookup"><span data-stu-id="15054-202">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="15054-203">Je pouze jeden zachycené skupině regex pravidlo přesměrování v ukázkovou aplikaci, takže pouze jednu skupinu vložený v řetězci pro nahrazení, který je `$1`.</span><span class="sxs-lookup"><span data-stu-id="15054-203">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="15054-204">Když se pravidlo použije, adresa URL stane `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="15054-204">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="15054-205">Adresa URL přesměrování do zabezpečeného koncového bodu</span><span class="sxs-lookup"><span data-stu-id="15054-205">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="15054-206">Použití <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> pro přesměrování požadavků protokolu HTTP na stejného hostitele a cestu pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="15054-206">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="15054-207">Pokud není zadán kód stavu, middleware použije se výchozí *302 - nalezen*.</span><span class="sxs-lookup"><span data-stu-id="15054-207">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="15054-208">Pokud není zadán port:</span><span class="sxs-lookup"><span data-stu-id="15054-208">If the port isn't supplied:</span></span>

* <span data-ttu-id="15054-209">Výchozí hodnota middleware `null`.</span><span class="sxs-lookup"><span data-stu-id="15054-209">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="15054-210">Schéma se změní na `https` (protokol HTTPS), a klient přistupuje k prostředku na portu 443.</span><span class="sxs-lookup"><span data-stu-id="15054-210">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="15054-211">Následující příklad ukazuje, jak nastavit stavový kód na *301 - trvale přesunuto* a změňte port 5001.</span><span class="sxs-lookup"><span data-stu-id="15054-211">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="15054-212">Použití <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> přesměrovat nezabezpečené požadavky na stejného hostitele a cestu s zabezpečený protokol HTTPS na portu 443.</span><span class="sxs-lookup"><span data-stu-id="15054-212">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="15054-213">Middleware nastaví stavový kód na *301 - trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="15054-213">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="15054-214">Při přesměrování do zabezpečeného koncového bodu bez nutnosti další přesměrování pravidla, doporučujeme používat Middleware přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="15054-214">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="15054-215">Další informace najdete v tématu [vynucení HTTPS](xref:security/enforcing-ssl#require-https) tématu.</span><span class="sxs-lookup"><span data-stu-id="15054-215">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="15054-216">Ukázková aplikace je schopná představením toho, jak používat `AddRedirectToHttps` nebo `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="15054-216">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="15054-217">Přidat metodu rozšíření k `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="15054-217">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="15054-218">Ujistěte se, nezabezpečený požadavek na aplikaci na libovolnou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="15054-218">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="15054-219">Zavřít upozornění, že nedůvěryhodný certifikát podepsaný svým držitelem zabezpečení v prohlížeči nebo vytvořte výjimku důvěřovat certifikátům.</span><span class="sxs-lookup"><span data-stu-id="15054-219">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="15054-220">Původní žádost o prostřednictvím `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="15054-220">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s vývojářskými nástroji pro sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="15054-222">Původní žádost o prostřednictvím `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="15054-222">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s vývojářskými nástroji pro sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="15054-224">Přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="15054-224">URL rewrite</span></span>

<span data-ttu-id="15054-225">Použití <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> k vytvoření pravidla pro přepis adres URL.</span><span class="sxs-lookup"><span data-stu-id="15054-225">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="15054-226">První parametr obsahuje regulárního výrazu pro porovnávání na příchozí cestě adresy URL.</span><span class="sxs-lookup"><span data-stu-id="15054-226">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="15054-227">Druhý parametr je řetězci pro nahrazení.</span><span class="sxs-lookup"><span data-stu-id="15054-227">The second parameter is the replacement string.</span></span> <span data-ttu-id="15054-228">Třetí parametr `skipRemainingRules: {true|false}`, pozná middleware, jestli se mají přeskočit další přepsání pravidla, pokud aktuální pravidlo použije.</span><span class="sxs-lookup"><span data-stu-id="15054-228">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="15054-229">Původní žádosti: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="15054-229">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Okno prohlížeče s vývojářskými nástroji pro sledování požadavků a odpovědí](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="15054-231">Stříšky (`^`) na začátku výrazu znamená že odpovídající začne na začátku cesty URL.</span><span class="sxs-lookup"><span data-stu-id="15054-231">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="15054-232">V předchozím příkladu se pravidlo přesměrování `redirect-rule/(.*)`, neexistuje žádný stříšky (`^`) na začátku regulární výraz.</span><span class="sxs-lookup"><span data-stu-id="15054-232">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="15054-233">Proto může předcházet žádné znaky `redirect-rule/` v cestě k porovnání.</span><span class="sxs-lookup"><span data-stu-id="15054-233">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="15054-234">Cesta</span><span class="sxs-lookup"><span data-stu-id="15054-234">Path</span></span>                               | <span data-ttu-id="15054-235">Shoda</span><span class="sxs-lookup"><span data-stu-id="15054-235">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="15054-236">Ano</span><span class="sxs-lookup"><span data-stu-id="15054-236">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="15054-237">Ano</span><span class="sxs-lookup"><span data-stu-id="15054-237">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="15054-238">Ano</span><span class="sxs-lookup"><span data-stu-id="15054-238">Yes</span></span>   |

<span data-ttu-id="15054-239">Pravidlo pro přepis adres `^rewrite-rule/(\d+)/(\d+)`, pouze odpovídá cesty, pokud jsou při spuštění systému `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="15054-239">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="15054-240">V následující tabulce Všimněte si rozdílů v porovnávání.</span><span class="sxs-lookup"><span data-stu-id="15054-240">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="15054-241">Cesta</span><span class="sxs-lookup"><span data-stu-id="15054-241">Path</span></span>                              | <span data-ttu-id="15054-242">Shoda</span><span class="sxs-lookup"><span data-stu-id="15054-242">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="15054-243">Ano</span><span class="sxs-lookup"><span data-stu-id="15054-243">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="15054-244">Ne</span><span class="sxs-lookup"><span data-stu-id="15054-244">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="15054-245">Ne</span><span class="sxs-lookup"><span data-stu-id="15054-245">No</span></span>    |

<span data-ttu-id="15054-246">Následující `^rewrite-rule/` část výrazu, jsou dvě skupiny zachycení `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="15054-246">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="15054-247">`\d` Označuje, že *odpovídá číslici (číslo)*.</span><span class="sxs-lookup"><span data-stu-id="15054-247">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="15054-248">Znaménko plus (`+`) znamená, že *odpovídat nejméně jeden z předchozí znak*.</span><span class="sxs-lookup"><span data-stu-id="15054-248">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="15054-249">Proto se adresa URL musí obsahovat číslo za nímž následuje lomítko následované jiné číslo.</span><span class="sxs-lookup"><span data-stu-id="15054-249">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="15054-250">Tyto zachytávání skupiny jsou vloženy do přepsaný adresy URL jako `$1` a `$2`.</span><span class="sxs-lookup"><span data-stu-id="15054-250">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="15054-251">Přepište řetězci pro nahrazení pravidlo umístí zachycené skupiny do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="15054-251">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="15054-252">Požadovaná cesta `/rewrite-rule/1234/5678` je přepsán získat prostředek na `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="15054-252">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="15054-253">Pokud řetězec dotazu je k dispozici na původní požadavek, to se zachová, i když je přepsán adresu URL.</span><span class="sxs-lookup"><span data-stu-id="15054-253">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="15054-254">Neexistuje žádné odezvy na server se získat prostředek.</span><span class="sxs-lookup"><span data-stu-id="15054-254">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="15054-255">Pokud existuje prostředek se načíst a vrácen do klienta se *200 – OK* stavový kód.</span><span class="sxs-lookup"><span data-stu-id="15054-255">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="15054-256">Protože klient není přesměrován, adresu URL v adresním řádku prohlížeče se nezmění.</span><span class="sxs-lookup"><span data-stu-id="15054-256">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="15054-257">Klienty nelze rozpoznat, že na serveru došlo k operaci přepisování adres URL.</span><span class="sxs-lookup"><span data-stu-id="15054-257">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="15054-258">Použití `skipRemainingRules: true` vždy, když možné protože pravidel je výpočetně náročná a zvyšuje doba odezvy aplikace.</span><span class="sxs-lookup"><span data-stu-id="15054-258">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="15054-259">Nejrychlejší odezvu aplikace:</span><span class="sxs-lookup"><span data-stu-id="15054-259">For the fastest app response:</span></span>
>
> * <span data-ttu-id="15054-260">Pravidla z nejčastěji odpovídající pravidlo pro nejméně často odpovídající pravidlo pro přepis adres pořadí.</span><span class="sxs-lookup"><span data-stu-id="15054-260">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="15054-261">Zpracování zbývající pravidla přeskočte, pokud je nalezena shoda, a je potřeba žádná další pravidla zpracování.</span><span class="sxs-lookup"><span data-stu-id="15054-261">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-modrewrite"></a><span data-ttu-id="15054-262">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="15054-262">Apache mod_rewrite</span></span>

<span data-ttu-id="15054-263">Použití Apache mod_rewrite pravidel s <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="15054-263">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="15054-264">Ujistěte se, že soubor pravidel je nasazen s aplikací.</span><span class="sxs-lookup"><span data-stu-id="15054-264">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="15054-265">Další informace a příklady pravidel mod_rewrite najdete v tématu [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="15054-265">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="15054-266">A <xref:System.IO.StreamReader> slouží k načtení pravidla z *ApacheModRewrite.txt* souboru pravidel:</span><span class="sxs-lookup"><span data-stu-id="15054-266">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="15054-267">Ukázková aplikace přesměruje požadavky z `/apache-mod-rules-redirect/(.\*)` k `/redirected?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="15054-267">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="15054-268">Stavový kód odpovědi je *302 - nalezen*.</span><span class="sxs-lookup"><span data-stu-id="15054-268">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="15054-269">Původní žádosti: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="15054-269">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Okno prohlížeče s vývojářskými nástroji pro sledování požadavků a odpovědí](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="15054-271">Middleware podporuje následující proměnné na serveru Apache mod_rewrite:</span><span class="sxs-lookup"><span data-stu-id="15054-271">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="15054-272">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="15054-272">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="15054-273">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="15054-273">HTTP_ACCEPT</span></span>
* <span data-ttu-id="15054-274">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="15054-274">HTTP_CONNECTION</span></span>
* <span data-ttu-id="15054-275">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="15054-275">HTTP_COOKIE</span></span>
* <span data-ttu-id="15054-276">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="15054-276">HTTP_FORWARDED</span></span>
* <span data-ttu-id="15054-277">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="15054-277">HTTP_HOST</span></span>
* <span data-ttu-id="15054-278">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="15054-278">HTTP_REFERER</span></span>
* <span data-ttu-id="15054-279">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="15054-279">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="15054-280">HTTPS</span><span class="sxs-lookup"><span data-stu-id="15054-280">HTTPS</span></span>
* <span data-ttu-id="15054-281">IPV6</span><span class="sxs-lookup"><span data-stu-id="15054-281">IPV6</span></span>
* <span data-ttu-id="15054-282">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="15054-282">QUERY_STRING</span></span>
* <span data-ttu-id="15054-283">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="15054-283">REMOTE_ADDR</span></span>
* <span data-ttu-id="15054-284">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="15054-284">REMOTE_PORT</span></span>
* <span data-ttu-id="15054-285">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="15054-285">REQUEST_FILENAME</span></span>
* <span data-ttu-id="15054-286">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="15054-286">REQUEST_METHOD</span></span>
* <span data-ttu-id="15054-287">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="15054-287">REQUEST_SCHEME</span></span>
* <span data-ttu-id="15054-288">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="15054-288">REQUEST_URI</span></span>
* <span data-ttu-id="15054-289">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="15054-289">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="15054-290">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="15054-290">SERVER_ADDR</span></span>
* <span data-ttu-id="15054-291">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="15054-291">SERVER_PORT</span></span>
* <span data-ttu-id="15054-292">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="15054-292">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="15054-293">ČAS</span><span class="sxs-lookup"><span data-stu-id="15054-293">TIME</span></span>
* <span data-ttu-id="15054-294">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="15054-294">TIME_DAY</span></span>
* <span data-ttu-id="15054-295">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="15054-295">TIME_HOUR</span></span>
* <span data-ttu-id="15054-296">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="15054-296">TIME_MIN</span></span>
* <span data-ttu-id="15054-297">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="15054-297">TIME_MON</span></span>
* <span data-ttu-id="15054-298">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="15054-298">TIME_SEC</span></span>
* <span data-ttu-id="15054-299">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="15054-299">TIME_WDAY</span></span>
* <span data-ttu-id="15054-300">MODULU KPI</span><span class="sxs-lookup"><span data-stu-id="15054-300">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="15054-301">Modul přepisování adres URL služby IIS pravidla</span><span class="sxs-lookup"><span data-stu-id="15054-301">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="15054-302">Pokud chcete použít stejnou sadu pravidel, která se vztahuje na modul přepisování adres URL služby IIS, použijte <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="15054-302">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="15054-303">Ujistěte se, že soubor pravidel je nasazen s aplikací.</span><span class="sxs-lookup"><span data-stu-id="15054-303">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="15054-304">Není přímá middlewaru, který má být použit aplikace *web.config* souboru při spuštění ve Windows serveru služby IIS.</span><span class="sxs-lookup"><span data-stu-id="15054-304">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="15054-305">Se službou IIS, tato pravidla by měly být uloženy mimo aplikaci prvku *web.config* souboru, aby nedocházelo ke konfliktům s modulem IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="15054-305">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="15054-306">Další informace a příklady pravidel modul přepisování adres URL služby IIS najdete v tématu [pomocí přepisování adres Url modul 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [URL přepsání konfigurace odkazu na modul](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="15054-306">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="15054-307">A <xref:System.IO.StreamReader> slouží k načtení pravidla z *IISUrlRewrite.xml* souboru pravidel:</span><span class="sxs-lookup"><span data-stu-id="15054-307">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="15054-308">Ukázková aplikace přepíše žádosti od `/iis-rules-rewrite/(.*)` k `/rewritten?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="15054-308">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="15054-309">Odpověď je odeslána na klienta se *200 – OK* stavový kód.</span><span class="sxs-lookup"><span data-stu-id="15054-309">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="15054-310">Původní žádosti: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="15054-310">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Okno prohlížeče s vývojářskými nástroji pro sledování požadavků a odpovědí](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="15054-312">Pokud máte aktivní revize modul IIS s nakonfigurovaná pravidla úrovni serveru, které by ovlivnily aplikace nežádoucí způsoby, můžete zakázat modul IIS revize pro danou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="15054-312">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="15054-313">Další informace najdete v tématu [moduly IIS zakázání](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="15054-313">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="15054-314">Nepodporované funkce</span><span class="sxs-lookup"><span data-stu-id="15054-314">Unsupported features</span></span>

<span data-ttu-id="15054-315">Middleware vydané s ASP.NET Core 2.x nepodporuje následující funkce modul přepisování adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="15054-315">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="15054-316">Odchozí pravidla</span><span class="sxs-lookup"><span data-stu-id="15054-316">Outbound Rules</span></span>
* <span data-ttu-id="15054-317">Vlastní serverové proměnné</span><span class="sxs-lookup"><span data-stu-id="15054-317">Custom Server Variables</span></span>
* <span data-ttu-id="15054-318">Zástupné znaky</span><span class="sxs-lookup"><span data-stu-id="15054-318">Wildcards</span></span>
* <span data-ttu-id="15054-319">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="15054-319">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="15054-320">Podporované serverové proměnné</span><span class="sxs-lookup"><span data-stu-id="15054-320">Supported server variables</span></span>

<span data-ttu-id="15054-321">Middleware podporuje následující proměnné serveru modul přepisování adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="15054-321">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="15054-322">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="15054-322">CONTENT_LENGTH</span></span>
* <span data-ttu-id="15054-323">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="15054-323">CONTENT_TYPE</span></span>
* <span data-ttu-id="15054-324">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="15054-324">HTTP_ACCEPT</span></span>
* <span data-ttu-id="15054-325">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="15054-325">HTTP_CONNECTION</span></span>
* <span data-ttu-id="15054-326">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="15054-326">HTTP_COOKIE</span></span>
* <span data-ttu-id="15054-327">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="15054-327">HTTP_HOST</span></span>
* <span data-ttu-id="15054-328">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="15054-328">HTTP_REFERER</span></span>
* <span data-ttu-id="15054-329">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="15054-329">HTTP_URL</span></span>
* <span data-ttu-id="15054-330">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="15054-330">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="15054-331">HTTPS</span><span class="sxs-lookup"><span data-stu-id="15054-331">HTTPS</span></span>
* <span data-ttu-id="15054-332">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="15054-332">LOCAL_ADDR</span></span>
* <span data-ttu-id="15054-333">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="15054-333">QUERY_STRING</span></span>
* <span data-ttu-id="15054-334">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="15054-334">REMOTE_ADDR</span></span>
* <span data-ttu-id="15054-335">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="15054-335">REMOTE_PORT</span></span>
* <span data-ttu-id="15054-336">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="15054-336">REQUEST_FILENAME</span></span>
* <span data-ttu-id="15054-337">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="15054-337">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="15054-338">Můžete také získat <xref:Microsoft.Extensions.FileProviders.IFileProvider> prostřednictvím <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="15054-338">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="15054-339">Tento přístup může poskytnout větší flexibilitu pro umístění vaší revize souborů pravidel.</span><span class="sxs-lookup"><span data-stu-id="15054-339">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="15054-340">Ujistěte se, že soubory přepsání pravidel jsou nasazeny na server v cestě, které zadáte.</span><span class="sxs-lookup"><span data-stu-id="15054-340">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="15054-341">Pravidlo na základě – metoda</span><span class="sxs-lookup"><span data-stu-id="15054-341">Method-based rule</span></span>

<span data-ttu-id="15054-342">Použití <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> implementovat vlastní logiku pravidla v metodě.</span><span class="sxs-lookup"><span data-stu-id="15054-342">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="15054-343">`Add` Zpřístupňuje <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, který je k dispozici <xref:Microsoft.AspNetCore.Http.HttpContext> pro použití v metodě.</span><span class="sxs-lookup"><span data-stu-id="15054-343">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="15054-344">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) Určuje, jak další kanálu probíhá zpracování.</span><span class="sxs-lookup"><span data-stu-id="15054-344">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="15054-345">Nastavte hodnotu na jednu z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> polí popsaných v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="15054-345">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="15054-346">Akce</span><span class="sxs-lookup"><span data-stu-id="15054-346">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="15054-347">`RuleResult.ContinueRules` (výchozí)</span><span class="sxs-lookup"><span data-stu-id="15054-347">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="15054-348">Pokračujte v použití pravidel.</span><span class="sxs-lookup"><span data-stu-id="15054-348">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="15054-349">Zastavit použití pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="15054-349">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="15054-350">Zastavit použití pravidel a odeslat kontextu do dalšího middlewaru.</span><span class="sxs-lookup"><span data-stu-id="15054-350">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="15054-351">Ukázková aplikace předvádí metodu, který přesměruje požadavky pro cesty, která končit *.xml*.</span><span class="sxs-lookup"><span data-stu-id="15054-351">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="15054-352">Pokud se požadavek `/file.xml`, je požadavek přesměrován na `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="15054-352">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="15054-353">Kód stavu je nastaven na *301 - trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="15054-353">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="15054-354">Při prohlížeč provede novou žádost */xmlfiles/file.xml*, Middleware statické soubory slouží soubor do klienta z *wwwroot/xmlfiles* složky.</span><span class="sxs-lookup"><span data-stu-id="15054-354">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="15054-355">Pro přesměrování explicitně nastavte stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="15054-355">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="15054-356">V opačném případě *200 – OK* se vrátí stavový kód a přesměrování nedojde na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="15054-356">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="15054-357">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="15054-357">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="15054-358">Tento přístup může také přepsání požadavků.</span><span class="sxs-lookup"><span data-stu-id="15054-358">This approach can also rewrite requests.</span></span> <span data-ttu-id="15054-359">Ukázková aplikace předvádí přepsání cesty u všech žádostí textový soubor k poskytování *soubor.txt* textový soubor z *wwwroot* složky.</span><span class="sxs-lookup"><span data-stu-id="15054-359">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="15054-360">Middleware statické soubory slouží souboru na základě cesty aktualizované požadavku:</span><span class="sxs-lookup"><span data-stu-id="15054-360">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="15054-361">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="15054-361">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="15054-362">Pravidlo na základě IRule</span><span class="sxs-lookup"><span data-stu-id="15054-362">IRule-based rule</span></span>

<span data-ttu-id="15054-363">Použít <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> používat logice pravidla v třídě, která implementuje <xref:Microsoft.AspNetCore.Rewrite.IRule> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="15054-363">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="15054-364">`IRule` nabízí větší flexibilitu v porovnání s využitím pravidel založených na volání metody přístupu.</span><span class="sxs-lookup"><span data-stu-id="15054-364">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="15054-365">Vaše implementace třídy může obsahovat konstruktor, který umožňuje, můžete předat parametry <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> metody.</span><span class="sxs-lookup"><span data-stu-id="15054-365">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="15054-366">Hodnoty parametrů v ukázkové aplikaci pro `extension` a `newPath` jsou kontrolovány pro splnění určitých podmínek.</span><span class="sxs-lookup"><span data-stu-id="15054-366">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="15054-367">`extension` Musí obsahovat hodnotu, a hodnota musí být *.png*, *.jpg*, nebo *.gif*.</span><span class="sxs-lookup"><span data-stu-id="15054-367">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="15054-368">Pokud `newPath` není platný, <xref:System.ArgumentException> je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="15054-368">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="15054-369">Pokud se požadavek *image.png*, je požadavek přesměrován na `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="15054-369">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="15054-370">Pokud se požadavek *image.jpg*, je požadavek přesměrován na `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="15054-370">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="15054-371">Kód stavu je nastaven na *301 - trvale přesunuto*a `context.Result` je nastavena na zastavení zpracování pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="15054-371">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="15054-372">Původní žádosti: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="15054-372">Original Request: `/image.png`</span></span>

![Okno prohlížeče s vývojářskými nástroji pro sledování požadavků a odpovědí pro image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="15054-374">Původní žádosti: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="15054-374">Original Request: `/image.jpg`</span></span>

![Okno prohlížeče s vývojářskými nástroji pro sledování požadavků a odpovědí pro image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="15054-376">Příklady regulární výraz</span><span class="sxs-lookup"><span data-stu-id="15054-376">Regex examples</span></span>

| <span data-ttu-id="15054-377">Cíl</span><span class="sxs-lookup"><span data-stu-id="15054-377">Goal</span></span> | <span data-ttu-id="15054-378">Řetězec regulárního výrazu &</span><span class="sxs-lookup"><span data-stu-id="15054-378">Regex String &</span></span><br><span data-ttu-id="15054-379">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="15054-379">Match Example</span></span> | <span data-ttu-id="15054-380">Náhradní řetězec &</span><span class="sxs-lookup"><span data-stu-id="15054-380">Replacement String &</span></span><br><span data-ttu-id="15054-381">Příklad výstupu</span><span class="sxs-lookup"><span data-stu-id="15054-381">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="15054-382">Přepsat cestu do řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="15054-382">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="15054-383">Odstranit koncové lomítko</span><span class="sxs-lookup"><span data-stu-id="15054-383">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="15054-384">Vynucení adresy koncové lomítko</span><span class="sxs-lookup"><span data-stu-id="15054-384">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="15054-385">Vyhněte se přepsání určité žádosti</span><span class="sxs-lookup"><span data-stu-id="15054-385">Avoid rewriting specific requests</span></span> | <span data-ttu-id="15054-386">`^(.*)(?<!\.axd)$` Nebo `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="15054-386">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="15054-387">Ano: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="15054-387">Yes: `/resource.htm`</span></span><br><span data-ttu-id="15054-388">Ne: `/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="15054-388">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="15054-389">Změna uspořádání segmenty adres URL</span><span class="sxs-lookup"><span data-stu-id="15054-389">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="15054-390">Nahraďte segment adresy URL</span><span class="sxs-lookup"><span data-stu-id="15054-390">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

## <a name="additional-resources"></a><span data-ttu-id="15054-391">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="15054-391">Additional resources</span></span>

* [<span data-ttu-id="15054-392">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="15054-392">Application Startup</span></span>](startup.md)
* [<span data-ttu-id="15054-393">Middleware</span><span class="sxs-lookup"><span data-stu-id="15054-393">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="15054-394">Regulární výrazy v rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="15054-394">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="15054-395">Jazyk regulárních výrazů – Stručná referenční příručka</span><span class="sxs-lookup"><span data-stu-id="15054-395">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="15054-396">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="15054-396">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="15054-397">Pomocí modulu přepisování adres Url 2.0 (pro IIS)</span><span class="sxs-lookup"><span data-stu-id="15054-397">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="15054-398">Odkaz na modul konfigurace adresy URL revize</span><span class="sxs-lookup"><span data-stu-id="15054-398">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="15054-399">Fórum modulu přepsání adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="15054-399">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="15054-400">Zachovat jednoduchá struktura adresy URL</span><span class="sxs-lookup"><span data-stu-id="15054-400">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="15054-401">10 přepisování adres URL tipy a triky</span><span class="sxs-lookup"><span data-stu-id="15054-401">10 URL Rewriting Tips and Tricks</span></span>](http://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="15054-402">Lomítko nebo lomítko</span><span class="sxs-lookup"><span data-stu-id="15054-402">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
