---
title: Url Přepisování MiddleWare v ASP.NET Core
author: rick-anderson
description: Přečtěte si o přepisování a přesměrování adres URL pomocí adres URL Přepisování middlewaru v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
uid: fundamentals/url-rewriting
ms.openlocfilehash: 7d63cf381f1d8a19ed4fb789348e36f94304ad63
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666465"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="9e666-103">Url Přepisování MiddleWare v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9e666-103">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="9e666-104">Podle [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="9e666-104">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9e666-105">Tento dokument představuje přepisování adres URL s pokyny, jak používat middleware pro přepisování adres URL v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9e666-105">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="9e666-106">Přepisování adres URL je úprava adres URL požadavků na základě jednoho nebo více předdefinovaných pravidel.</span><span class="sxs-lookup"><span data-stu-id="9e666-106">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="9e666-107">Přepisování adres URL vytvoří abstrakce mezi umístěními prostředků a jejich adresami tak, aby umístění a adresy nebyly úzce propojeny.</span><span class="sxs-lookup"><span data-stu-id="9e666-107">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="9e666-108">Přepisování adres URL je cenné v několika scénářích:</span><span class="sxs-lookup"><span data-stu-id="9e666-108">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="9e666-109">Přesuňte nebo nahraďte serverové prostředky dočasně nebo trvale a udržujte stabilní lokátory pro tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="9e666-109">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="9e666-110">Rozdělení zpracování požadavků na různé aplikace nebo oblasti jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="9e666-110">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="9e666-111">Odeberte, přidejte nebo reorganizujete segmenty adres URL u příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="9e666-111">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="9e666-112">Optimalizace veřejných adres URL pro optimalizaci pro vyhledávače (SEO).</span><span class="sxs-lookup"><span data-stu-id="9e666-112">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="9e666-113">Povolte použití popisných veřejných adres URL, které návštěvníkům pomohou předpovědět obsah vrácený vyžádáním prostředku.</span><span class="sxs-lookup"><span data-stu-id="9e666-113">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="9e666-114">Přesměrovat nezabezpečené požadavky na zabezpečené koncové body.</span><span class="sxs-lookup"><span data-stu-id="9e666-114">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="9e666-115">Zabránit hotlinking, kde externí web používá hostovaný statický datový zdroj na jiném webu propojením datového zdroje do vlastního obsahu.</span><span class="sxs-lookup"><span data-stu-id="9e666-115">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="9e666-116">Přepisování adres URL může snížit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="9e666-116">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="9e666-117">Je-li to proveditelné, omezte počet a složitost pravidel.</span><span class="sxs-lookup"><span data-stu-id="9e666-117">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="9e666-118">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="9e666-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="9e666-119">Přesměrování adresy URL a přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="9e666-119">URL redirect and URL rewrite</span></span>

<span data-ttu-id="9e666-120">Rozdíl ve formulaci mezi *přesměrováním adresy URL* a *přepsáním adresy URL* je jemný, ale má důležité důsledky pro poskytování prostředků klientům.</span><span class="sxs-lookup"><span data-stu-id="9e666-120">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="9e666-121">ASP.NET Core URL Přepisování Middleware je schopen splnit potřebu obou.</span><span class="sxs-lookup"><span data-stu-id="9e666-121">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="9e666-122">*Přesměrování adresy URL* zahrnuje operaci na straně klienta, kde je klientovi pokyn k přístupu k prostředku na jiné adrese, než klient původně požadoval.</span><span class="sxs-lookup"><span data-stu-id="9e666-122">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="9e666-123">To vyžaduje odezvu na server.</span><span class="sxs-lookup"><span data-stu-id="9e666-123">This requires a round trip to the server.</span></span> <span data-ttu-id="9e666-124">Adresa URL přesměrování vrácená klientovi se zobrazí v adresním řádku prohlížeče, když klient vytvoří nový požadavek na prostředek.</span><span class="sxs-lookup"><span data-stu-id="9e666-124">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="9e666-125">Pokud `/resource` je *přesměrován* na `/different-resource`, server odpoví, že `/different-resource` klient by měl získat prostředek na se stavovým kódem označující, že přesměrování je dočasné nebo trvalé.</span><span class="sxs-lookup"><span data-stu-id="9e666-125">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Koncový bod služby WebAPI byl dočasně změněn z verze 1 (v1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="9e666-131">Při přesměrování požadavků na jinou adresu URL označte, zda je přesměrování trvalé nebo dočasné zadáním stavového kódu s odpovědí:</span><span class="sxs-lookup"><span data-stu-id="9e666-131">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="9e666-132">Stavový kód *301 - Přesunout trvale* se používá v případě, že prostředek má novou trvalou adresu URL a chcete dát klientovi pokyn, aby všechny budoucí požadavky na prostředek měly použít novou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="9e666-132">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="9e666-133">*Klient může ukládat do mezipaměti a znovu použít odpověď při přijetí stavového kódu 301.*</span><span class="sxs-lookup"><span data-stu-id="9e666-133">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="9e666-134">Stavový kód *302 - Found* se používá, pokud je přesměrování dočasné nebo obecně může změnit.</span><span class="sxs-lookup"><span data-stu-id="9e666-134">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="9e666-135">Stavový kód 302 označuje klientovi, aby adresu URL neukládal a v budoucnu ji používal.</span><span class="sxs-lookup"><span data-stu-id="9e666-135">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="9e666-136">Další informace o stavových kódech naleznete v [tématu RFC 2616: Definice stavového kódu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="9e666-136">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="9e666-137">*Přepsání adresy URL* je operace na straně serveru, která poskytuje prostředek z jiné adresy prostředku, než klient požadoval.</span><span class="sxs-lookup"><span data-stu-id="9e666-137">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="9e666-138">Přepsání adresy URL nevyžaduje odezvu na server.</span><span class="sxs-lookup"><span data-stu-id="9e666-138">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="9e666-139">Přepsaná adresa URL není vrácena klientovi a nezobrazuje se v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9e666-139">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="9e666-140">Pokud `/resource` je *přepsánna* `/different-resource`, server *interně* načte `/different-resource`a vrátí prostředek na .</span><span class="sxs-lookup"><span data-stu-id="9e666-140">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="9e666-141">Přestože klient může být schopen načíst prostředek na přepsané adresy URL, klient není informován, že prostředek existuje na přepsané adrese URL, když to jeho požadavek a obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="9e666-141">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Koncový bod služby WebAPI byl změněn z verze 1 (v1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="9e666-146">Ukázková aplikace pro přepisování adres URL</span><span class="sxs-lookup"><span data-stu-id="9e666-146">URL rewriting sample app</span></span>

<span data-ttu-id="9e666-147">Můžete prozkoumat funkce URL Přepisování Middleware s [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="9e666-147">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="9e666-148">Aplikace používá pravidla přesměrování a přepisu a zobrazuje přesměrovanou nebo přepsanou adresu URL pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="9e666-148">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="9e666-149">Kdy použít adresu URL Přepisování Middleware</span><span class="sxs-lookup"><span data-stu-id="9e666-149">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="9e666-150">Pomocí adres URL Přepisování Middleware, pokud nemůžete použít následující přístupy:</span><span class="sxs-lookup"><span data-stu-id="9e666-150">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="9e666-151">Modul přepisování adres URL se službou IIS v systému Windows Server</span><span class="sxs-lookup"><span data-stu-id="9e666-151">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="9e666-152">Apache mod_rewrite modul na Apache Serveru</span><span class="sxs-lookup"><span data-stu-id="9e666-152">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="9e666-153">Přepisování adres URL na Nginx</span><span class="sxs-lookup"><span data-stu-id="9e666-153">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="9e666-154">Middleware použijte také v případě, že je aplikace hostována na [serveru HTTP.sys](xref:fundamentals/servers/httpsys) (dříve nazývaném WebListener).</span><span class="sxs-lookup"><span data-stu-id="9e666-154">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="9e666-155">Hlavní důvody pro použití technologií přepisování adres URL na serveru ve službách IIS, Apache a Nginx jsou:</span><span class="sxs-lookup"><span data-stu-id="9e666-155">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="9e666-156">Middleware nepodporuje všechny funkce těchto modulů.</span><span class="sxs-lookup"><span data-stu-id="9e666-156">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="9e666-157">Některé funkce serverových modulů nefungují s ASP.NET základní projekty, jako je například `IsFile` omezení `IsDirectory` modulu přepisování služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9e666-157">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="9e666-158">V těchto scénářích použijte middleware místo.</span><span class="sxs-lookup"><span data-stu-id="9e666-158">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="9e666-159">Výkon middleware pravděpodobně neodpovídá tomu modulu.</span><span class="sxs-lookup"><span data-stu-id="9e666-159">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="9e666-160">Benchmarking je jediný způsob, jak s jistotou vědět, který přístup snižuje výkon nejvíce, nebo pokud je snížený výkon zanedbatelný.</span><span class="sxs-lookup"><span data-stu-id="9e666-160">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="9e666-161">Balíček</span><span class="sxs-lookup"><span data-stu-id="9e666-161">Package</span></span>

<span data-ttu-id="9e666-162">Adresa URL Přepisování Middleware je poskytována [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) balíček, který je implicitně součástí ASP.NET aplikace Core.</span><span class="sxs-lookup"><span data-stu-id="9e666-162">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="9e666-163">Rozšíření a možnosti</span><span class="sxs-lookup"><span data-stu-id="9e666-163">Extension and options</span></span>

<span data-ttu-id="9e666-164">Vytvořte pravidla pro přepsání a přesměrování adresy URL vytvořením instance třídy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) s metodami rozšíření pro každé z pravidel přepsání.</span><span class="sxs-lookup"><span data-stu-id="9e666-164">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="9e666-165">Zřetězit více pravidel v pořadí, ve které je chcete zpracovat.</span><span class="sxs-lookup"><span data-stu-id="9e666-165">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="9e666-166">Jsou `RewriteOptions` předány do adresy URL Přepisování Middleware, jak <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>je přidán do kanálu požadavku s :</span><span class="sxs-lookup"><span data-stu-id="9e666-166">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="9e666-167">Přesměrovat non-www na www</span><span class="sxs-lookup"><span data-stu-id="9e666-167">Redirect non-www to www</span></span>

<span data-ttu-id="9e666-168">Tři možnosti umožňují aplikaci`www` přesměrovat `www`nežádosti na :</span><span class="sxs-lookup"><span data-stu-id="9e666-168">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="9e666-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>&ndash; Trvale přesměrovat požadavek `www` na subdoménu,`www`pokud je požadavek non- .</span><span class="sxs-lookup"><span data-stu-id="9e666-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="9e666-170">Přesměruje pomocí stavového kódu [Stav308PermanentRedirect.](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect)</span><span class="sxs-lookup"><span data-stu-id="9e666-170">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="9e666-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>&ndash; Přesměrujte požadavek `www` na subdoménu, pokud`www`příchozí požadavek není- .</span><span class="sxs-lookup"><span data-stu-id="9e666-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="9e666-172">Přesměruje pomocí stavového kódu [Status307TemporaryRedirect.](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect)</span><span class="sxs-lookup"><span data-stu-id="9e666-172">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="9e666-173">Přetížení umožňuje zadat stavový kód pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="9e666-173">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="9e666-174">Pro přiřazení stavového kódu použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy.</span><span class="sxs-lookup"><span data-stu-id="9e666-174">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="9e666-175">Přesměrování adresy URL</span><span class="sxs-lookup"><span data-stu-id="9e666-175">URL redirect</span></span>

<span data-ttu-id="9e666-176">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> k přesměrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="9e666-176">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="9e666-177">První parametr obsahuje regulární výraz pro porovnávání na cestě k příchozí adrese URL.</span><span class="sxs-lookup"><span data-stu-id="9e666-177">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="9e666-178">Druhý parametr je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="9e666-178">The second parameter is the replacement string.</span></span> <span data-ttu-id="9e666-179">Třetí parametr, pokud je k dispozici, určuje stavový kód.</span><span class="sxs-lookup"><span data-stu-id="9e666-179">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="9e666-180">Pokud nezadáte stavový kód, stavový kód je výchozí *302 - Nalezeno*, což znamená, že prostředek je dočasně přesunut nebo nahrazen.</span><span class="sxs-lookup"><span data-stu-id="9e666-180">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="9e666-181">V prohlížeči s povolenými vývojářskými nástroji podejte `/redirect-rule/1234/5678`požadavek na ukázkovou aplikaci s cestou .</span><span class="sxs-lookup"><span data-stu-id="9e666-181">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="9e666-182">Regulární výraz odpovídá `redirect-rule/(.*)`cestě požadavku na a `/redirected/1234/5678`cesta je nahrazena .</span><span class="sxs-lookup"><span data-stu-id="9e666-182">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="9e666-183">Adresa URL přesměrování je odeslána zpět klientovi se stavovým kódem *302 - Found.*</span><span class="sxs-lookup"><span data-stu-id="9e666-183">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="9e666-184">Prohlížeč provede nový požadavek na adresu URL přesměrování, která se zobrazí v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9e666-184">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="9e666-185">Vzhledem k tomu, že žádná pravidla v ukázkové aplikaci se neshodují na adrese URL přesměrování:</span><span class="sxs-lookup"><span data-stu-id="9e666-185">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="9e666-186">Druhý požadavek obdrží *odpověď 200 - OK* z aplikace.</span><span class="sxs-lookup"><span data-stu-id="9e666-186">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="9e666-187">Text odpovědi zobrazuje adresu URL přesměrování.</span><span class="sxs-lookup"><span data-stu-id="9e666-187">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="9e666-188">Při *přesměrování*adresy URL na server se provádí okroucení .</span><span class="sxs-lookup"><span data-stu-id="9e666-188">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="9e666-189">Při vytváření pravidel přesměrování buďte opatrní.</span><span class="sxs-lookup"><span data-stu-id="9e666-189">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="9e666-190">Pravidla přesměrování se vyhodnocují při každém požadavku na aplikaci, včetně po přesměrování.</span><span class="sxs-lookup"><span data-stu-id="9e666-190">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="9e666-191">Je snadné náhodně vytvořit *smyčku nekonečných přesměrování*.</span><span class="sxs-lookup"><span data-stu-id="9e666-191">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="9e666-192">Původní požadavek:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="9e666-192">Original Request: `/redirect-rule/1234/5678`</span></span>

![Okno prohlížeče s nástroji pro vývojáře sledující požadavky a odpovědi](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="9e666-194">Část výrazu obsažená v závorce se nazývá *skupina zachycení*.</span><span class="sxs-lookup"><span data-stu-id="9e666-194">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="9e666-195">Tečka (`.`) výrazu znamená, že *odpovídá libovolnému znaku*.</span><span class="sxs-lookup"><span data-stu-id="9e666-195">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="9e666-196">Hvězdička (`*`) *označuje, že odpovídá předchozímu znaku nula nebo vícekrát*.</span><span class="sxs-lookup"><span data-stu-id="9e666-196">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="9e666-197">Proto poslední dva segmenty cesty adresy `1234/5678`URL , jsou `(.*)`zachyceny skupinou zachycení .</span><span class="sxs-lookup"><span data-stu-id="9e666-197">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="9e666-198">Všechny hodnoty, které zadáte `redirect-rule/` v adrese URL požadavku po je zachycen a to to jediné zachycení skupiny.</span><span class="sxs-lookup"><span data-stu-id="9e666-198">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="9e666-199">V náhradním řetězci jsou zachycené skupiny vloženy`$`do řetězce se znakem dolaru ( ) následovaným pořadovým číslem zachycení.</span><span class="sxs-lookup"><span data-stu-id="9e666-199">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="9e666-200">První hodnota skupiny zachycení `$1`je získána s , druhá s `$2`a pokračují v pořadí pro skupiny zachycení v regulárním výrazu.</span><span class="sxs-lookup"><span data-stu-id="9e666-200">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="9e666-201">V regulárním období pravidla přesměrování v ukázkové aplikaci je pouze jedna zachycená skupina, takže v `$1`náhradním řetězci je pouze jedna vstřikovaná skupina, což je .</span><span class="sxs-lookup"><span data-stu-id="9e666-201">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="9e666-202">Po použití pravidla se adresa `/redirected/1234/5678`URL změní na .</span><span class="sxs-lookup"><span data-stu-id="9e666-202">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="9e666-203">Přesměrování adresy URL na zabezpečený koncový bod</span><span class="sxs-lookup"><span data-stu-id="9e666-203">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="9e666-204">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> k přesměrování požadavků HTTP na stejného hostitele a cestu pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9e666-204">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="9e666-205">Pokud není zadán stavový kód, middleware výchozí *302 - Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="9e666-205">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="9e666-206">Pokud port není součástí dodávky:</span><span class="sxs-lookup"><span data-stu-id="9e666-206">If the port isn't supplied:</span></span>

* <span data-ttu-id="9e666-207">Middleware výchozí nastavení `null`.</span><span class="sxs-lookup"><span data-stu-id="9e666-207">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="9e666-208">Schéma se `https` změní na (protokol HTTPS) a klient přistupuje k prostředku na portu 443.</span><span class="sxs-lookup"><span data-stu-id="9e666-208">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="9e666-209">Následující příklad ukazuje, jak nastavit stavový kód na *301 - Trvale přesunuta* a změnit port na 5001.</span><span class="sxs-lookup"><span data-stu-id="9e666-209">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="9e666-210">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> k přesměrování nezabezpečených požadavků na stejného hostitele a cestu pomocí zabezpečeného protokolu HTTPS na portu 443.</span><span class="sxs-lookup"><span data-stu-id="9e666-210">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="9e666-211">Middleware nastaví stavový kód na *301 - Trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="9e666-211">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="9e666-212">Při přesměrování na zabezpečený koncový bod bez požadavku na další pravidla přesměrování doporučujeme použít middleware přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9e666-212">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="9e666-213">Další informace naleznete v tématu [Vynucení https.](xref:security/enforcing-ssl#require-https)</span><span class="sxs-lookup"><span data-stu-id="9e666-213">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="9e666-214">Ukázková aplikace je schopna `AddRedirectToHttps` demonstrovat, jak používat nebo `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="9e666-214">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="9e666-215">Přidejte metodu `RewriteOptions`rozšíření do .</span><span class="sxs-lookup"><span data-stu-id="9e666-215">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="9e666-216">Vytvořte nezabezpečený požadavek na aplikaci na libovolné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="9e666-216">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="9e666-217">Zavřete upozornění zabezpečení prohlížeče, že certifikát podepsaný svým držitelem je nedůvěryhodný, nebo vytvořte výjimku pro důvěryhodnost certifikátu.</span><span class="sxs-lookup"><span data-stu-id="9e666-217">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="9e666-218">Původní požadavek pomocí `AddRedirectToHttps(301, 5001)`:`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="9e666-218">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s nástroji pro vývojáře sledující požadavky a odpovědi](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="9e666-220">Původní požadavek pomocí `AddRedirectToHttpsPermanent`:`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="9e666-220">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s nástroji pro vývojáře sledující požadavky a odpovědi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="9e666-222">Přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="9e666-222">URL rewrite</span></span>

<span data-ttu-id="9e666-223">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> k vytvoření pravidla pro přepisování adres URL.</span><span class="sxs-lookup"><span data-stu-id="9e666-223">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="9e666-224">První parametr obsahuje regulární výraz pro porovnávání na příchozí cestě URL.</span><span class="sxs-lookup"><span data-stu-id="9e666-224">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="9e666-225">Druhý parametr je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="9e666-225">The second parameter is the replacement string.</span></span> <span data-ttu-id="9e666-226">Třetí parametr `skipRemainingRules: {true|false}`, označuje middleware, zda mají přeskočit další pravidla přepsání, pokud je použito aktuální pravidlo.</span><span class="sxs-lookup"><span data-stu-id="9e666-226">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="9e666-227">Původní požadavek:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="9e666-227">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Okno prohlížeče s nástroji pro vývojáře sledující požadavek a odpověď](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="9e666-229">Karát`^`( ) na začátku výrazu znamená, že shoda začíná na začátku cesty URL.</span><span class="sxs-lookup"><span data-stu-id="9e666-229">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="9e666-230">V předchozím příkladu s pravidlem `redirect-rule/(.*)`přesměrování , není`^`karát ( ) na začátku regulárního výrazu.</span><span class="sxs-lookup"><span data-stu-id="9e666-230">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="9e666-231">Proto všechny znaky mohou `redirect-rule/` předcházet v cestě pro úspěšnou shodu.</span><span class="sxs-lookup"><span data-stu-id="9e666-231">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="9e666-232">Cesta</span><span class="sxs-lookup"><span data-stu-id="9e666-232">Path</span></span>                               | <span data-ttu-id="9e666-233">Shoda</span><span class="sxs-lookup"><span data-stu-id="9e666-233">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="9e666-234">Ano</span><span class="sxs-lookup"><span data-stu-id="9e666-234">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="9e666-235">Ano</span><span class="sxs-lookup"><span data-stu-id="9e666-235">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="9e666-236">Ano</span><span class="sxs-lookup"><span data-stu-id="9e666-236">Yes</span></span>   |

<span data-ttu-id="9e666-237">Pravidlo přepsání `^rewrite-rule/(\d+)/(\d+)`, pouze odpovídá cestám, pokud začínají na `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="9e666-237">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="9e666-238">V následující tabulce si všimněte rozdílu v porovnávání.</span><span class="sxs-lookup"><span data-stu-id="9e666-238">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="9e666-239">Cesta</span><span class="sxs-lookup"><span data-stu-id="9e666-239">Path</span></span>                              | <span data-ttu-id="9e666-240">Shoda</span><span class="sxs-lookup"><span data-stu-id="9e666-240">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="9e666-241">Ano</span><span class="sxs-lookup"><span data-stu-id="9e666-241">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="9e666-242">Ne</span><span class="sxs-lookup"><span data-stu-id="9e666-242">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="9e666-243">Ne</span><span class="sxs-lookup"><span data-stu-id="9e666-243">No</span></span>    |

<span data-ttu-id="9e666-244">Následující `^rewrite-rule/` část výrazu existují dvě skupiny `(\d+)/(\d+)`zachycení, .</span><span class="sxs-lookup"><span data-stu-id="9e666-244">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="9e666-245">Označení `\d` odpovídají *číslici (číslo).*</span><span class="sxs-lookup"><span data-stu-id="9e666-245">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="9e666-246">Znaménko plus (`+`) znamená, že odpovídá jednomu nebo více *předchozímu znaku*.</span><span class="sxs-lookup"><span data-stu-id="9e666-246">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="9e666-247">Adresa URL proto musí obsahovat číslo následované lomítkem, za nímž následuje jiné číslo.</span><span class="sxs-lookup"><span data-stu-id="9e666-247">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="9e666-248">Tyto skupiny zachycení jsou vloženy `$1` do `$2`přepsané adresy URL jako a .</span><span class="sxs-lookup"><span data-stu-id="9e666-248">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="9e666-249">Řetězec nahrazení pravidla přepisu umístí zachycené skupiny do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="9e666-249">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="9e666-250">Požadovaná cesta `/rewrite-rule/1234/5678` je přepsána k `/rewritten?var1=1234&var2=5678`získání prostředku na adrese .</span><span class="sxs-lookup"><span data-stu-id="9e666-250">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="9e666-251">Pokud je řetězec dotazu k dispozici na původní požadavek, je zachována při přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="9e666-251">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="9e666-252">Neexistuje žádná zpáteční cesta na server získat zdroj.</span><span class="sxs-lookup"><span data-stu-id="9e666-252">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="9e666-253">Pokud prostředek existuje, je načten a vrácen klientovi se stavovým kódem *200 - OK.*</span><span class="sxs-lookup"><span data-stu-id="9e666-253">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="9e666-254">Vzhledem k tomu, že klient není přesměrován, adresa URL v adresním řádku prohlížeče se nezmění.</span><span class="sxs-lookup"><span data-stu-id="9e666-254">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="9e666-255">Klienti nemohou zjistit, že na serveru došlo k operaci přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="9e666-255">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="9e666-256">Použijte `skipRemainingRules: true` vždy, když je to možné, protože odpovídající pravidla jsou výpočtově drahá a prodlužují dobu odezvy aplikace.</span><span class="sxs-lookup"><span data-stu-id="9e666-256">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="9e666-257">Pro nejrychlejší odezvu aplikace:</span><span class="sxs-lookup"><span data-stu-id="9e666-257">For the fastest app response:</span></span>
>
> * <span data-ttu-id="9e666-258">Pořadí přepisovat pravidla z nejčastěji spárovaného pravidla na nejméně často spárované pravidlo.</span><span class="sxs-lookup"><span data-stu-id="9e666-258">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="9e666-259">Přeskočit zpracování zbývajících pravidel, když dojde ke shodě a žádné další zpracování pravidel je vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="9e666-259">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="9e666-260">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="9e666-260">Apache mod_rewrite</span></span>

<span data-ttu-id="9e666-261">Použijte pravidla Apache <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>mod_rewrite s .</span><span class="sxs-lookup"><span data-stu-id="9e666-261">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="9e666-262">Ujistěte se, že soubor pravidel je nasazen s aplikací.</span><span class="sxs-lookup"><span data-stu-id="9e666-262">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="9e666-263">Další informace a příklady mod_rewrite pravidel naleznete v [tématu Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="9e666-263">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="9e666-264">A <xref:System.IO.StreamReader> se používá ke čtení pravidel ze souboru pravidel *ApacheModRewrite.txt:*</span><span class="sxs-lookup"><span data-stu-id="9e666-264">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="9e666-265">Ukázková aplikace přesměruje `/apache-mod-rules-redirect/(.\*)` požadavky `/redirected?id=$1`z do .</span><span class="sxs-lookup"><span data-stu-id="9e666-265">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="9e666-266">Stavový kód odpovědi je *302 - Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="9e666-266">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="9e666-267">Původní požadavek:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="9e666-267">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Okno prohlížeče s nástroji pro vývojáře sledující požadavky a odpovědi](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="9e666-269">Middleware podporuje následující apache mod_rewrite serverové proměnné:</span><span class="sxs-lookup"><span data-stu-id="9e666-269">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="9e666-270">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="9e666-270">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="9e666-271">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="9e666-271">HTTP_ACCEPT</span></span>
* <span data-ttu-id="9e666-272">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="9e666-272">HTTP_CONNECTION</span></span>
* <span data-ttu-id="9e666-273">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="9e666-273">HTTP_COOKIE</span></span>
* <span data-ttu-id="9e666-274">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="9e666-274">HTTP_FORWARDED</span></span>
* <span data-ttu-id="9e666-275">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="9e666-275">HTTP_HOST</span></span>
* <span data-ttu-id="9e666-276">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="9e666-276">HTTP_REFERER</span></span>
* <span data-ttu-id="9e666-277">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="9e666-277">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="9e666-278">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9e666-278">HTTPS</span></span>
* <span data-ttu-id="9e666-279">Protokol IPV6</span><span class="sxs-lookup"><span data-stu-id="9e666-279">IPV6</span></span>
* <span data-ttu-id="9e666-280">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="9e666-280">QUERY_STRING</span></span>
* <span data-ttu-id="9e666-281">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="9e666-281">REMOTE_ADDR</span></span>
* <span data-ttu-id="9e666-282">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="9e666-282">REMOTE_PORT</span></span>
* <span data-ttu-id="9e666-283">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="9e666-283">REQUEST_FILENAME</span></span>
* <span data-ttu-id="9e666-284">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="9e666-284">REQUEST_METHOD</span></span>
* <span data-ttu-id="9e666-285">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="9e666-285">REQUEST_SCHEME</span></span>
* <span data-ttu-id="9e666-286">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="9e666-286">REQUEST_URI</span></span>
* <span data-ttu-id="9e666-287">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="9e666-287">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="9e666-288">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="9e666-288">SERVER_ADDR</span></span>
* <span data-ttu-id="9e666-289">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="9e666-289">SERVER_PORT</span></span>
* <span data-ttu-id="9e666-290">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="9e666-290">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="9e666-291">ČAS</span><span class="sxs-lookup"><span data-stu-id="9e666-291">TIME</span></span>
* <span data-ttu-id="9e666-292">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="9e666-292">TIME_DAY</span></span>
* <span data-ttu-id="9e666-293">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="9e666-293">TIME_HOUR</span></span>
* <span data-ttu-id="9e666-294">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="9e666-294">TIME_MIN</span></span>
* <span data-ttu-id="9e666-295">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="9e666-295">TIME_MON</span></span>
* <span data-ttu-id="9e666-296">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="9e666-296">TIME_SEC</span></span>
* <span data-ttu-id="9e666-297">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="9e666-297">TIME_WDAY</span></span>
* <span data-ttu-id="9e666-298">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="9e666-298">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="9e666-299">Pravidla modulu přepisování adres URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="9e666-299">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="9e666-300">Chcete-li použít stejnou sadu pravidel, která se vztahuje <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>na modul přepisování adres URL služby IIS, použijte .</span><span class="sxs-lookup"><span data-stu-id="9e666-300">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="9e666-301">Ujistěte se, že soubor pravidel je nasazen s aplikací.</span><span class="sxs-lookup"><span data-stu-id="9e666-301">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="9e666-302">Nenasměrujte middleware na použití souboru *web.config* aplikace při spuštění ve službě Windows Server IIS.</span><span class="sxs-lookup"><span data-stu-id="9e666-302">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="9e666-303">Ve službě IIS by tato pravidla měla být uložena mimo soubor *web.config* aplikace, aby nedošlo ke konfliktům s modulem Přepisování služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9e666-303">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="9e666-304">Další informace a příklady pravidel modulu přepisování adres URL služby IIS naleznete [v tématu Použití modulu pro přepis adres URL 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [odkazu na konfiguraci modulu pro přepis adres URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="9e666-304">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="9e666-305">A <xref:System.IO.StreamReader> se používá ke čtení pravidel ze souboru pravidel *IISUrlRewrite.xml:*</span><span class="sxs-lookup"><span data-stu-id="9e666-305">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="9e666-306">Ukázková aplikace přepíše `/iis-rules-rewrite/(.*)` požadavky `/rewritten?id=$1`z do .</span><span class="sxs-lookup"><span data-stu-id="9e666-306">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="9e666-307">Odpověď je odeslána klientovi se stavovým kódem *200 - OK.*</span><span class="sxs-lookup"><span data-stu-id="9e666-307">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="9e666-308">Původní požadavek:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="9e666-308">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Okno prohlížeče s nástroji pro vývojáře sledující požadavek a odpověď](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="9e666-310">Pokud máte aktivní modul pro přepisování služby IIS s nakonfigurovanými pravidly na úrovni serveru, která by měla nepříznivý vliv na vaši aplikaci, můžete modul pro přepisování služby IIS pro aplikaci zakázat.</span><span class="sxs-lookup"><span data-stu-id="9e666-310">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="9e666-311">Další informace naleznete [v tématu Zakázání modulů iis](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="9e666-311">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="9e666-312">Nepodporované funkce</span><span class="sxs-lookup"><span data-stu-id="9e666-312">Unsupported features</span></span>

<span data-ttu-id="9e666-313">Middleware vydaný s ASP.NET Core 2.x nepodporuje následující funkce modulu přepisování adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="9e666-313">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="9e666-314">Pravidla pro odchozí provoz</span><span class="sxs-lookup"><span data-stu-id="9e666-314">Outbound Rules</span></span>
* <span data-ttu-id="9e666-315">Proměnné vlastního serveru</span><span class="sxs-lookup"><span data-stu-id="9e666-315">Custom Server Variables</span></span>
* <span data-ttu-id="9e666-316">Zástupné znaky</span><span class="sxs-lookup"><span data-stu-id="9e666-316">Wildcards</span></span>
* <span data-ttu-id="9e666-317">Adresa LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="9e666-317">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="9e666-318">Podporované proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="9e666-318">Supported server variables</span></span>

<span data-ttu-id="9e666-319">Middleware podporuje následující proměnné serveru serveru IIS URL Rewrite Module:</span><span class="sxs-lookup"><span data-stu-id="9e666-319">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="9e666-320">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="9e666-320">CONTENT_LENGTH</span></span>
* <span data-ttu-id="9e666-321">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="9e666-321">CONTENT_TYPE</span></span>
* <span data-ttu-id="9e666-322">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="9e666-322">HTTP_ACCEPT</span></span>
* <span data-ttu-id="9e666-323">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="9e666-323">HTTP_CONNECTION</span></span>
* <span data-ttu-id="9e666-324">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="9e666-324">HTTP_COOKIE</span></span>
* <span data-ttu-id="9e666-325">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="9e666-325">HTTP_HOST</span></span>
* <span data-ttu-id="9e666-326">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="9e666-326">HTTP_REFERER</span></span>
* <span data-ttu-id="9e666-327">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="9e666-327">HTTP_URL</span></span>
* <span data-ttu-id="9e666-328">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="9e666-328">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="9e666-329">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9e666-329">HTTPS</span></span>
* <span data-ttu-id="9e666-330">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="9e666-330">LOCAL_ADDR</span></span>
* <span data-ttu-id="9e666-331">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="9e666-331">QUERY_STRING</span></span>
* <span data-ttu-id="9e666-332">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="9e666-332">REMOTE_ADDR</span></span>
* <span data-ttu-id="9e666-333">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="9e666-333">REMOTE_PORT</span></span>
* <span data-ttu-id="9e666-334">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="9e666-334">REQUEST_FILENAME</span></span>
* <span data-ttu-id="9e666-335">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="9e666-335">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="9e666-336">Můžete také získat <xref:Microsoft.Extensions.FileProviders.IFileProvider> prostřednictvím <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="9e666-336">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="9e666-337">Tento přístup může poskytnout větší flexibilitu pro umístění souborů pravidel přepisování.</span><span class="sxs-lookup"><span data-stu-id="9e666-337">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="9e666-338">Ujistěte se, že soubory pravidel přepisu jsou nasazeny na server na cestě, kterou zadáte.</span><span class="sxs-lookup"><span data-stu-id="9e666-338">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="9e666-339">Pravidlo založené na metodách</span><span class="sxs-lookup"><span data-stu-id="9e666-339">Method-based rule</span></span>

<span data-ttu-id="9e666-340">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k implementaci vlastní logiky pravidla v metodě.</span><span class="sxs-lookup"><span data-stu-id="9e666-340">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="9e666-341">`Add`zpřístupní <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, který <xref:Microsoft.AspNetCore.Http.HttpContext> zpřístupňuje pro použití v metodě.</span><span class="sxs-lookup"><span data-stu-id="9e666-341">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="9e666-342">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) určuje, jak je zpracováno další zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="9e666-342">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="9e666-343">Nastavte hodnotu na <xref:Microsoft.AspNetCore.Rewrite.RuleResult> jedno z polí popsaných v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="9e666-343">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="9e666-344">Akce</span><span class="sxs-lookup"><span data-stu-id="9e666-344">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="9e666-345">`RuleResult.ContinueRules`(výchozí)</span><span class="sxs-lookup"><span data-stu-id="9e666-345">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="9e666-346">Pokračujte v uplatňování pravidel.</span><span class="sxs-lookup"><span data-stu-id="9e666-346">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="9e666-347">Přestaňte používat pravidla a odešlete odpověď.</span><span class="sxs-lookup"><span data-stu-id="9e666-347">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="9e666-348">Přestaňte používat pravidla a odešlete kontext do dalšího middlewaru.</span><span class="sxs-lookup"><span data-stu-id="9e666-348">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="9e666-349">Ukázková aplikace demonstruje metodu, která přesměruje požadavky na cesty, které končí *.xml*.</span><span class="sxs-lookup"><span data-stu-id="9e666-349">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="9e666-350">Pokud je žádost `/file.xml`podána , je `/xmlfiles/file.xml`žádost přesměrována na .</span><span class="sxs-lookup"><span data-stu-id="9e666-350">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="9e666-351">Stavový kód je nastaven na *301 - Trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="9e666-351">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="9e666-352">Když prohlížeč vytvoří nový požadavek na */xmlfiles/file.xml*, middleware statického souboru zobrazí soubor klientovi ze složky *wwwroot/xmlfiles.*</span><span class="sxs-lookup"><span data-stu-id="9e666-352">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="9e666-353">Pro přesměrování explicitně nastavte stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="9e666-353">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="9e666-354">V opačném případě je vrácen stavový kód *200 - OK* a přesměrování se nevyskytuje na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="9e666-354">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="9e666-355">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e666-355">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="9e666-356">Tento přístup můžete také přepsat požadavky.</span><span class="sxs-lookup"><span data-stu-id="9e666-356">This approach can also rewrite requests.</span></span> <span data-ttu-id="9e666-357">Ukázková aplikace ukazuje přepisování cesty pro libovolný požadavek textového souboru, který má být připojen k textovému souboru *file.txt* ze složky *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="9e666-357">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="9e666-358">Middleware statického souboru slouží souboru na základě aktualizované cesty požadavku:</span><span class="sxs-lookup"><span data-stu-id="9e666-358">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="9e666-359">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e666-359">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="9e666-360">Pravidlo založené na pravidlech IRule</span><span class="sxs-lookup"><span data-stu-id="9e666-360">IRule-based rule</span></span>

<span data-ttu-id="9e666-361">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k použití logiky pravidla ve <xref:Microsoft.AspNetCore.Rewrite.IRule> třídě, která implementuje rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9e666-361">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="9e666-362">`IRule`poskytuje větší flexibilitu při používání přístupu pravidla založeného na metodách.</span><span class="sxs-lookup"><span data-stu-id="9e666-362">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="9e666-363">Vaše třída implementace může obsahovat konstruktor, který umožňuje <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> předat parametry pro metodu.</span><span class="sxs-lookup"><span data-stu-id="9e666-363">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="9e666-364">Hodnoty parametrů v ukázkové aplikaci `extension` pro `newPath` a a jsou kontrolovány tak, aby splňovaly několik podmínek.</span><span class="sxs-lookup"><span data-stu-id="9e666-364">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="9e666-365">Musí `extension` obsahovat hodnotu a hodnota musí být *.png*, *.jpg*nebo *.gif*.</span><span class="sxs-lookup"><span data-stu-id="9e666-365">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="9e666-366">Pokud `newPath` není platný, <xref:System.ArgumentException> je hozen.</span><span class="sxs-lookup"><span data-stu-id="9e666-366">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="9e666-367">Pokud je požadavek na *soubor image.png*podán `/png-images/image.png`, je požadavek přesměrován na .</span><span class="sxs-lookup"><span data-stu-id="9e666-367">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="9e666-368">Pokud je požadavek na *image.jpg*, požadavek je přesměrován na `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="9e666-368">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="9e666-369">Stavový kód je nastaven na *301 - Trvale přesunuta*a `context.Result` je nastavena na zastavení zpracování pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="9e666-369">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="9e666-370">Původní požadavek:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="9e666-370">Original Request: `/image.png`</span></span>

![Okno prohlížeče s nástroji pro vývojáře, které sledují požadavky a odpovědi pro soubor image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="9e666-372">Původní požadavek:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="9e666-372">Original Request: `/image.jpg`</span></span>

![Okno prohlížeče s nástroji pro vývojáře, které sledují požadavky a odpovědi na obrázek.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="9e666-374">Příklady regexu</span><span class="sxs-lookup"><span data-stu-id="9e666-374">Regex examples</span></span>

| <span data-ttu-id="9e666-375">Cíl</span><span class="sxs-lookup"><span data-stu-id="9e666-375">Goal</span></span> | <span data-ttu-id="9e666-376">Řetězec regex &</span><span class="sxs-lookup"><span data-stu-id="9e666-376">Regex String &</span></span><br><span data-ttu-id="9e666-377">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="9e666-377">Match Example</span></span> | <span data-ttu-id="9e666-378">Náhradní řetězec &</span><span class="sxs-lookup"><span data-stu-id="9e666-378">Replacement String &</span></span><br><span data-ttu-id="9e666-379">Příklad výstupu</span><span class="sxs-lookup"><span data-stu-id="9e666-379">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="9e666-380">Přepsat cestu do řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="9e666-380">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="9e666-381">Pruh vlečené lomítko</span><span class="sxs-lookup"><span data-stu-id="9e666-381">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="9e666-382">Vynutit koncové lomítko</span><span class="sxs-lookup"><span data-stu-id="9e666-382">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="9e666-383">Vyhněte se přepisování konkrétních požadavků</span><span class="sxs-lookup"><span data-stu-id="9e666-383">Avoid rewriting specific requests</span></span> | <span data-ttu-id="9e666-384">`^(.*)(?<!\.axd)$` nebo `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="9e666-384">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="9e666-385">Ano:`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="9e666-385">Yes: `/resource.htm`</span></span><br><span data-ttu-id="9e666-386">Ne:`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="9e666-386">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="9e666-387">Změna uspořádání segmentů adres URL</span><span class="sxs-lookup"><span data-stu-id="9e666-387">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="9e666-388">Nahrazení segmentu adresy URL</span><span class="sxs-lookup"><span data-stu-id="9e666-388">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9e666-389">Tento dokument představuje přepisování adres URL s pokyny, jak používat middleware pro přepisování adres URL v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9e666-389">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="9e666-390">Přepisování adres URL je úprava adres URL požadavků na základě jednoho nebo více předdefinovaných pravidel.</span><span class="sxs-lookup"><span data-stu-id="9e666-390">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="9e666-391">Přepisování adres URL vytvoří abstrakce mezi umístěními prostředků a jejich adresami tak, aby umístění a adresy nebyly úzce propojeny.</span><span class="sxs-lookup"><span data-stu-id="9e666-391">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="9e666-392">Přepisování adres URL je cenné v několika scénářích:</span><span class="sxs-lookup"><span data-stu-id="9e666-392">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="9e666-393">Přesuňte nebo nahraďte serverové prostředky dočasně nebo trvale a udržujte stabilní lokátory pro tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="9e666-393">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="9e666-394">Rozdělení zpracování požadavků na různé aplikace nebo oblasti jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="9e666-394">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="9e666-395">Odeberte, přidejte nebo reorganizujete segmenty adres URL u příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="9e666-395">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="9e666-396">Optimalizace veřejných adres URL pro optimalizaci pro vyhledávače (SEO).</span><span class="sxs-lookup"><span data-stu-id="9e666-396">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="9e666-397">Povolte použití popisných veřejných adres URL, které návštěvníkům pomohou předpovědět obsah vrácený vyžádáním prostředku.</span><span class="sxs-lookup"><span data-stu-id="9e666-397">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="9e666-398">Přesměrovat nezabezpečené požadavky na zabezpečené koncové body.</span><span class="sxs-lookup"><span data-stu-id="9e666-398">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="9e666-399">Zabránit hotlinking, kde externí web používá hostovaný statický datový zdroj na jiném webu propojením datového zdroje do vlastního obsahu.</span><span class="sxs-lookup"><span data-stu-id="9e666-399">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="9e666-400">Přepisování adres URL může snížit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="9e666-400">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="9e666-401">Je-li to proveditelné, omezte počet a složitost pravidel.</span><span class="sxs-lookup"><span data-stu-id="9e666-401">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="9e666-402">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="9e666-402">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="9e666-403">Přesměrování adresy URL a přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="9e666-403">URL redirect and URL rewrite</span></span>

<span data-ttu-id="9e666-404">Rozdíl ve formulaci mezi *přesměrováním adresy URL* a *přepsáním adresy URL* je jemný, ale má důležité důsledky pro poskytování prostředků klientům.</span><span class="sxs-lookup"><span data-stu-id="9e666-404">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="9e666-405">ASP.NET Core URL Přepisování Middleware je schopen splnit potřebu obou.</span><span class="sxs-lookup"><span data-stu-id="9e666-405">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="9e666-406">*Přesměrování adresy URL* zahrnuje operaci na straně klienta, kde je klientovi pokyn k přístupu k prostředku na jiné adrese, než klient původně požadoval.</span><span class="sxs-lookup"><span data-stu-id="9e666-406">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="9e666-407">To vyžaduje odezvu na server.</span><span class="sxs-lookup"><span data-stu-id="9e666-407">This requires a round trip to the server.</span></span> <span data-ttu-id="9e666-408">Adresa URL přesměrování vrácená klientovi se zobrazí v adresním řádku prohlížeče, když klient vytvoří nový požadavek na prostředek.</span><span class="sxs-lookup"><span data-stu-id="9e666-408">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="9e666-409">Pokud `/resource` je *přesměrován* na `/different-resource`, server odpoví, že `/different-resource` klient by měl získat prostředek na se stavovým kódem označující, že přesměrování je dočasné nebo trvalé.</span><span class="sxs-lookup"><span data-stu-id="9e666-409">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Koncový bod služby WebAPI byl dočasně změněn z verze 1 (v1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="9e666-415">Při přesměrování požadavků na jinou adresu URL označte, zda je přesměrování trvalé nebo dočasné zadáním stavového kódu s odpovědí:</span><span class="sxs-lookup"><span data-stu-id="9e666-415">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="9e666-416">Stavový kód *301 - Přesunout trvale* se používá v případě, že prostředek má novou trvalou adresu URL a chcete dát klientovi pokyn, aby všechny budoucí požadavky na prostředek měly použít novou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="9e666-416">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="9e666-417">*Klient může ukládat do mezipaměti a znovu použít odpověď při přijetí stavového kódu 301.*</span><span class="sxs-lookup"><span data-stu-id="9e666-417">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="9e666-418">Stavový kód *302 - Found* se používá, pokud je přesměrování dočasné nebo obecně může změnit.</span><span class="sxs-lookup"><span data-stu-id="9e666-418">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="9e666-419">Stavový kód 302 označuje klientovi, aby adresu URL neukládal a v budoucnu ji používal.</span><span class="sxs-lookup"><span data-stu-id="9e666-419">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="9e666-420">Další informace o stavových kódech naleznete v [tématu RFC 2616: Definice stavového kódu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="9e666-420">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="9e666-421">*Přepsání adresy URL* je operace na straně serveru, která poskytuje prostředek z jiné adresy prostředku, než klient požadoval.</span><span class="sxs-lookup"><span data-stu-id="9e666-421">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="9e666-422">Přepsání adresy URL nevyžaduje odezvu na server.</span><span class="sxs-lookup"><span data-stu-id="9e666-422">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="9e666-423">Přepsaná adresa URL není vrácena klientovi a nezobrazuje se v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9e666-423">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="9e666-424">Pokud `/resource` je *přepsánna* `/different-resource`, server *interně* načte `/different-resource`a vrátí prostředek na .</span><span class="sxs-lookup"><span data-stu-id="9e666-424">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="9e666-425">Přestože klient může být schopen načíst prostředek na přepsané adresy URL, klient není informován, že prostředek existuje na přepsané adrese URL, když to jeho požadavek a obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="9e666-425">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Koncový bod služby WebAPI byl změněn z verze 1 (v1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="9e666-430">Ukázková aplikace pro přepisování adres URL</span><span class="sxs-lookup"><span data-stu-id="9e666-430">URL rewriting sample app</span></span>

<span data-ttu-id="9e666-431">Můžete prozkoumat funkce URL Přepisování Middleware s [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="9e666-431">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="9e666-432">Aplikace používá pravidla přesměrování a přepisu a zobrazuje přesměrovanou nebo přepsanou adresu URL pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="9e666-432">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="9e666-433">Kdy použít adresu URL Přepisování Middleware</span><span class="sxs-lookup"><span data-stu-id="9e666-433">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="9e666-434">Pomocí adres URL Přepisování Middleware, pokud nemůžete použít následující přístupy:</span><span class="sxs-lookup"><span data-stu-id="9e666-434">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="9e666-435">Modul přepisování adres URL se službou IIS v systému Windows Server</span><span class="sxs-lookup"><span data-stu-id="9e666-435">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="9e666-436">Apache mod_rewrite modul na Apache Serveru</span><span class="sxs-lookup"><span data-stu-id="9e666-436">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="9e666-437">Přepisování adres URL na Nginx</span><span class="sxs-lookup"><span data-stu-id="9e666-437">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="9e666-438">Middleware použijte také v případě, že je aplikace hostována na [serveru HTTP.sys](xref:fundamentals/servers/httpsys) (dříve nazývaném WebListener).</span><span class="sxs-lookup"><span data-stu-id="9e666-438">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="9e666-439">Hlavní důvody pro použití technologií přepisování adres URL na serveru ve službách IIS, Apache a Nginx jsou:</span><span class="sxs-lookup"><span data-stu-id="9e666-439">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="9e666-440">Middleware nepodporuje všechny funkce těchto modulů.</span><span class="sxs-lookup"><span data-stu-id="9e666-440">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="9e666-441">Některé funkce serverových modulů nefungují s ASP.NET základní projekty, jako je například `IsFile` omezení `IsDirectory` modulu přepisování služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9e666-441">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="9e666-442">V těchto scénářích použijte middleware místo.</span><span class="sxs-lookup"><span data-stu-id="9e666-442">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="9e666-443">Výkon middleware pravděpodobně neodpovídá tomu modulu.</span><span class="sxs-lookup"><span data-stu-id="9e666-443">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="9e666-444">Benchmarking je jediný způsob, jak s jistotou vědět, který přístup snižuje výkon nejvíce, nebo pokud je snížený výkon zanedbatelný.</span><span class="sxs-lookup"><span data-stu-id="9e666-444">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="9e666-445">Balíček</span><span class="sxs-lookup"><span data-stu-id="9e666-445">Package</span></span>

<span data-ttu-id="9e666-446">Chcete-li do projektu zahrnout middleware, přidejte odkaz na balíček [balíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) do souboru projektu, který obsahuje balíček [Microsoft.AspNetCore.Rewrite.](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite)</span><span class="sxs-lookup"><span data-stu-id="9e666-446">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="9e666-447">Pokud nepoužíváte `Microsoft.AspNetCore.App` metabalíček, přidejte `Microsoft.AspNetCore.Rewrite` odkaz na projekt balíčku.</span><span class="sxs-lookup"><span data-stu-id="9e666-447">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="9e666-448">Rozšíření a možnosti</span><span class="sxs-lookup"><span data-stu-id="9e666-448">Extension and options</span></span>

<span data-ttu-id="9e666-449">Vytvořte pravidla pro přepsání a přesměrování adresy URL vytvořením instance třídy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) s metodami rozšíření pro každé z pravidel přepsání.</span><span class="sxs-lookup"><span data-stu-id="9e666-449">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="9e666-450">Zřetězit více pravidel v pořadí, ve které je chcete zpracovat.</span><span class="sxs-lookup"><span data-stu-id="9e666-450">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="9e666-451">Jsou `RewriteOptions` předány do adresy URL Přepisování Middleware, jak <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>je přidán do kanálu požadavku s :</span><span class="sxs-lookup"><span data-stu-id="9e666-451">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="9e666-452">Přesměrovat non-www na www</span><span class="sxs-lookup"><span data-stu-id="9e666-452">Redirect non-www to www</span></span>

<span data-ttu-id="9e666-453">Tři možnosti umožňují aplikaci`www` přesměrovat `www`nežádosti na :</span><span class="sxs-lookup"><span data-stu-id="9e666-453">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="9e666-454"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>&ndash; Trvale přesměrovat požadavek `www` na subdoménu,`www`pokud je požadavek non- .</span><span class="sxs-lookup"><span data-stu-id="9e666-454"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="9e666-455">Přesměruje pomocí stavového kódu [Stav308PermanentRedirect.](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect)</span><span class="sxs-lookup"><span data-stu-id="9e666-455">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="9e666-456"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>&ndash; Přesměrujte požadavek `www` na subdoménu, pokud`www`příchozí požadavek není- .</span><span class="sxs-lookup"><span data-stu-id="9e666-456"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="9e666-457">Přesměruje pomocí stavového kódu [Status307TemporaryRedirect.](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect)</span><span class="sxs-lookup"><span data-stu-id="9e666-457">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="9e666-458">Přetížení umožňuje zadat stavový kód pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="9e666-458">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="9e666-459">Pro přiřazení stavového kódu použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy.</span><span class="sxs-lookup"><span data-stu-id="9e666-459">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="9e666-460">Přesměrování adresy URL</span><span class="sxs-lookup"><span data-stu-id="9e666-460">URL redirect</span></span>

<span data-ttu-id="9e666-461">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> k přesměrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="9e666-461">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="9e666-462">První parametr obsahuje regulární výraz pro porovnávání na cestě k příchozí adrese URL.</span><span class="sxs-lookup"><span data-stu-id="9e666-462">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="9e666-463">Druhý parametr je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="9e666-463">The second parameter is the replacement string.</span></span> <span data-ttu-id="9e666-464">Třetí parametr, pokud je k dispozici, určuje stavový kód.</span><span class="sxs-lookup"><span data-stu-id="9e666-464">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="9e666-465">Pokud nezadáte stavový kód, stavový kód je výchozí *302 - Nalezeno*, což znamená, že prostředek je dočasně přesunut nebo nahrazen.</span><span class="sxs-lookup"><span data-stu-id="9e666-465">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="9e666-466">V prohlížeči s povolenými vývojářskými nástroji podejte `/redirect-rule/1234/5678`požadavek na ukázkovou aplikaci s cestou .</span><span class="sxs-lookup"><span data-stu-id="9e666-466">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="9e666-467">Regulární výraz odpovídá `redirect-rule/(.*)`cestě požadavku na a `/redirected/1234/5678`cesta je nahrazena .</span><span class="sxs-lookup"><span data-stu-id="9e666-467">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="9e666-468">Adresa URL přesměrování je odeslána zpět klientovi se stavovým kódem *302 - Found.*</span><span class="sxs-lookup"><span data-stu-id="9e666-468">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="9e666-469">Prohlížeč provede nový požadavek na adresu URL přesměrování, která se zobrazí v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9e666-469">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="9e666-470">Vzhledem k tomu, že žádná pravidla v ukázkové aplikaci se neshodují na adrese URL přesměrování:</span><span class="sxs-lookup"><span data-stu-id="9e666-470">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="9e666-471">Druhý požadavek obdrží *odpověď 200 - OK* z aplikace.</span><span class="sxs-lookup"><span data-stu-id="9e666-471">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="9e666-472">Text odpovědi zobrazuje adresu URL přesměrování.</span><span class="sxs-lookup"><span data-stu-id="9e666-472">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="9e666-473">Při *přesměrování*adresy URL na server se provádí okroucení .</span><span class="sxs-lookup"><span data-stu-id="9e666-473">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="9e666-474">Při vytváření pravidel přesměrování buďte opatrní.</span><span class="sxs-lookup"><span data-stu-id="9e666-474">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="9e666-475">Pravidla přesměrování se vyhodnocují při každém požadavku na aplikaci, včetně po přesměrování.</span><span class="sxs-lookup"><span data-stu-id="9e666-475">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="9e666-476">Je snadné náhodně vytvořit *smyčku nekonečných přesměrování*.</span><span class="sxs-lookup"><span data-stu-id="9e666-476">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="9e666-477">Původní požadavek:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="9e666-477">Original Request: `/redirect-rule/1234/5678`</span></span>

![Okno prohlížeče s nástroji pro vývojáře sledující požadavky a odpovědi](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="9e666-479">Část výrazu obsažená v závorce se nazývá *skupina zachycení*.</span><span class="sxs-lookup"><span data-stu-id="9e666-479">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="9e666-480">Tečka (`.`) výrazu znamená, že *odpovídá libovolnému znaku*.</span><span class="sxs-lookup"><span data-stu-id="9e666-480">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="9e666-481">Hvězdička (`*`) *označuje, že odpovídá předchozímu znaku nula nebo vícekrát*.</span><span class="sxs-lookup"><span data-stu-id="9e666-481">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="9e666-482">Proto poslední dva segmenty cesty adresy `1234/5678`URL , jsou `(.*)`zachyceny skupinou zachycení .</span><span class="sxs-lookup"><span data-stu-id="9e666-482">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="9e666-483">Všechny hodnoty, které zadáte `redirect-rule/` v adrese URL požadavku po je zachycen a to to jediné zachycení skupiny.</span><span class="sxs-lookup"><span data-stu-id="9e666-483">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="9e666-484">V náhradním řetězci jsou zachycené skupiny vloženy`$`do řetězce se znakem dolaru ( ) následovaným pořadovým číslem zachycení.</span><span class="sxs-lookup"><span data-stu-id="9e666-484">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="9e666-485">První hodnota skupiny zachycení `$1`je získána s , druhá s `$2`a pokračují v pořadí pro skupiny zachycení v regulárním výrazu.</span><span class="sxs-lookup"><span data-stu-id="9e666-485">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="9e666-486">V regulárním období pravidla přesměrování v ukázkové aplikaci je pouze jedna zachycená skupina, takže v `$1`náhradním řetězci je pouze jedna vstřikovaná skupina, což je .</span><span class="sxs-lookup"><span data-stu-id="9e666-486">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="9e666-487">Po použití pravidla se adresa `/redirected/1234/5678`URL změní na .</span><span class="sxs-lookup"><span data-stu-id="9e666-487">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="9e666-488">Přesměrování adresy URL na zabezpečený koncový bod</span><span class="sxs-lookup"><span data-stu-id="9e666-488">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="9e666-489">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> k přesměrování požadavků HTTP na stejného hostitele a cestu pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9e666-489">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="9e666-490">Pokud není zadán stavový kód, middleware výchozí *302 - Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="9e666-490">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="9e666-491">Pokud port není součástí dodávky:</span><span class="sxs-lookup"><span data-stu-id="9e666-491">If the port isn't supplied:</span></span>

* <span data-ttu-id="9e666-492">Middleware výchozí nastavení `null`.</span><span class="sxs-lookup"><span data-stu-id="9e666-492">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="9e666-493">Schéma se `https` změní na (protokol HTTPS) a klient přistupuje k prostředku na portu 443.</span><span class="sxs-lookup"><span data-stu-id="9e666-493">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="9e666-494">Následující příklad ukazuje, jak nastavit stavový kód na *301 - Trvale přesunuta* a změnit port na 5001.</span><span class="sxs-lookup"><span data-stu-id="9e666-494">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="9e666-495">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> k přesměrování nezabezpečených požadavků na stejného hostitele a cestu pomocí zabezpečeného protokolu HTTPS na portu 443.</span><span class="sxs-lookup"><span data-stu-id="9e666-495">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="9e666-496">Middleware nastaví stavový kód na *301 - Trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="9e666-496">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="9e666-497">Při přesměrování na zabezpečený koncový bod bez požadavku na další pravidla přesměrování doporučujeme použít middleware přesměrování HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9e666-497">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="9e666-498">Další informace naleznete v tématu [Vynucení https.](xref:security/enforcing-ssl#require-https)</span><span class="sxs-lookup"><span data-stu-id="9e666-498">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="9e666-499">Ukázková aplikace je schopna `AddRedirectToHttps` demonstrovat, jak používat nebo `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="9e666-499">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="9e666-500">Přidejte metodu `RewriteOptions`rozšíření do .</span><span class="sxs-lookup"><span data-stu-id="9e666-500">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="9e666-501">Vytvořte nezabezpečený požadavek na aplikaci na libovolné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="9e666-501">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="9e666-502">Zavřete upozornění zabezpečení prohlížeče, že certifikát podepsaný svým držitelem je nedůvěryhodný, nebo vytvořte výjimku pro důvěryhodnost certifikátu.</span><span class="sxs-lookup"><span data-stu-id="9e666-502">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="9e666-503">Původní požadavek pomocí `AddRedirectToHttps(301, 5001)`:`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="9e666-503">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s nástroji pro vývojáře sledující požadavky a odpovědi](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="9e666-505">Původní požadavek pomocí `AddRedirectToHttpsPermanent`:`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="9e666-505">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s nástroji pro vývojáře sledující požadavky a odpovědi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="9e666-507">Přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="9e666-507">URL rewrite</span></span>

<span data-ttu-id="9e666-508">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> k vytvoření pravidla pro přepisování adres URL.</span><span class="sxs-lookup"><span data-stu-id="9e666-508">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="9e666-509">První parametr obsahuje regulární výraz pro porovnávání na příchozí cestě URL.</span><span class="sxs-lookup"><span data-stu-id="9e666-509">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="9e666-510">Druhý parametr je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="9e666-510">The second parameter is the replacement string.</span></span> <span data-ttu-id="9e666-511">Třetí parametr `skipRemainingRules: {true|false}`, označuje middleware, zda mají přeskočit další pravidla přepsání, pokud je použito aktuální pravidlo.</span><span class="sxs-lookup"><span data-stu-id="9e666-511">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="9e666-512">Původní požadavek:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="9e666-512">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Okno prohlížeče s nástroji pro vývojáře sledující požadavek a odpověď](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="9e666-514">Karát`^`( ) na začátku výrazu znamená, že shoda začíná na začátku cesty URL.</span><span class="sxs-lookup"><span data-stu-id="9e666-514">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="9e666-515">V předchozím příkladu s pravidlem `redirect-rule/(.*)`přesměrování , není`^`karát ( ) na začátku regulárního výrazu.</span><span class="sxs-lookup"><span data-stu-id="9e666-515">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="9e666-516">Proto všechny znaky mohou `redirect-rule/` předcházet v cestě pro úspěšnou shodu.</span><span class="sxs-lookup"><span data-stu-id="9e666-516">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="9e666-517">Cesta</span><span class="sxs-lookup"><span data-stu-id="9e666-517">Path</span></span>                               | <span data-ttu-id="9e666-518">Shoda</span><span class="sxs-lookup"><span data-stu-id="9e666-518">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="9e666-519">Ano</span><span class="sxs-lookup"><span data-stu-id="9e666-519">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="9e666-520">Ano</span><span class="sxs-lookup"><span data-stu-id="9e666-520">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="9e666-521">Ano</span><span class="sxs-lookup"><span data-stu-id="9e666-521">Yes</span></span>   |

<span data-ttu-id="9e666-522">Pravidlo přepsání `^rewrite-rule/(\d+)/(\d+)`, pouze odpovídá cestám, pokud začínají na `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="9e666-522">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="9e666-523">V následující tabulce si všimněte rozdílu v porovnávání.</span><span class="sxs-lookup"><span data-stu-id="9e666-523">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="9e666-524">Cesta</span><span class="sxs-lookup"><span data-stu-id="9e666-524">Path</span></span>                              | <span data-ttu-id="9e666-525">Shoda</span><span class="sxs-lookup"><span data-stu-id="9e666-525">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="9e666-526">Ano</span><span class="sxs-lookup"><span data-stu-id="9e666-526">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="9e666-527">Ne</span><span class="sxs-lookup"><span data-stu-id="9e666-527">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="9e666-528">Ne</span><span class="sxs-lookup"><span data-stu-id="9e666-528">No</span></span>    |

<span data-ttu-id="9e666-529">Následující `^rewrite-rule/` část výrazu existují dvě skupiny `(\d+)/(\d+)`zachycení, .</span><span class="sxs-lookup"><span data-stu-id="9e666-529">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="9e666-530">Označení `\d` odpovídají *číslici (číslo).*</span><span class="sxs-lookup"><span data-stu-id="9e666-530">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="9e666-531">Znaménko plus (`+`) znamená, že odpovídá jednomu nebo více *předchozímu znaku*.</span><span class="sxs-lookup"><span data-stu-id="9e666-531">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="9e666-532">Adresa URL proto musí obsahovat číslo následované lomítkem, za nímž následuje jiné číslo.</span><span class="sxs-lookup"><span data-stu-id="9e666-532">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="9e666-533">Tyto skupiny zachycení jsou vloženy `$1` do `$2`přepsané adresy URL jako a .</span><span class="sxs-lookup"><span data-stu-id="9e666-533">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="9e666-534">Řetězec nahrazení pravidla přepisu umístí zachycené skupiny do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="9e666-534">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="9e666-535">Požadovaná cesta `/rewrite-rule/1234/5678` je přepsána k `/rewritten?var1=1234&var2=5678`získání prostředku na adrese .</span><span class="sxs-lookup"><span data-stu-id="9e666-535">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="9e666-536">Pokud je řetězec dotazu k dispozici na původní požadavek, je zachována při přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="9e666-536">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="9e666-537">Neexistuje žádná zpáteční cesta na server získat zdroj.</span><span class="sxs-lookup"><span data-stu-id="9e666-537">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="9e666-538">Pokud prostředek existuje, je načten a vrácen klientovi se stavovým kódem *200 - OK.*</span><span class="sxs-lookup"><span data-stu-id="9e666-538">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="9e666-539">Vzhledem k tomu, že klient není přesměrován, adresa URL v adresním řádku prohlížeče se nezmění.</span><span class="sxs-lookup"><span data-stu-id="9e666-539">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="9e666-540">Klienti nemohou zjistit, že na serveru došlo k operaci přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="9e666-540">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="9e666-541">Použijte `skipRemainingRules: true` vždy, když je to možné, protože odpovídající pravidla jsou výpočtově drahá a prodlužují dobu odezvy aplikace.</span><span class="sxs-lookup"><span data-stu-id="9e666-541">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="9e666-542">Pro nejrychlejší odezvu aplikace:</span><span class="sxs-lookup"><span data-stu-id="9e666-542">For the fastest app response:</span></span>
>
> * <span data-ttu-id="9e666-543">Pořadí přepisovat pravidla z nejčastěji spárovaného pravidla na nejméně často spárované pravidlo.</span><span class="sxs-lookup"><span data-stu-id="9e666-543">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="9e666-544">Přeskočit zpracování zbývajících pravidel, když dojde ke shodě a žádné další zpracování pravidel je vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="9e666-544">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="9e666-545">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="9e666-545">Apache mod_rewrite</span></span>

<span data-ttu-id="9e666-546">Použijte pravidla Apache <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>mod_rewrite s .</span><span class="sxs-lookup"><span data-stu-id="9e666-546">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="9e666-547">Ujistěte se, že soubor pravidel je nasazen s aplikací.</span><span class="sxs-lookup"><span data-stu-id="9e666-547">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="9e666-548">Další informace a příklady mod_rewrite pravidel naleznete v [tématu Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="9e666-548">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="9e666-549">A <xref:System.IO.StreamReader> se používá ke čtení pravidel ze souboru pravidel *ApacheModRewrite.txt:*</span><span class="sxs-lookup"><span data-stu-id="9e666-549">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="9e666-550">Ukázková aplikace přesměruje `/apache-mod-rules-redirect/(.\*)` požadavky `/redirected?id=$1`z do .</span><span class="sxs-lookup"><span data-stu-id="9e666-550">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="9e666-551">Stavový kód odpovědi je *302 - Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="9e666-551">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="9e666-552">Původní požadavek:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="9e666-552">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Okno prohlížeče s nástroji pro vývojáře sledující požadavky a odpovědi](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="9e666-554">Middleware podporuje následující apache mod_rewrite serverové proměnné:</span><span class="sxs-lookup"><span data-stu-id="9e666-554">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="9e666-555">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="9e666-555">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="9e666-556">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="9e666-556">HTTP_ACCEPT</span></span>
* <span data-ttu-id="9e666-557">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="9e666-557">HTTP_CONNECTION</span></span>
* <span data-ttu-id="9e666-558">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="9e666-558">HTTP_COOKIE</span></span>
* <span data-ttu-id="9e666-559">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="9e666-559">HTTP_FORWARDED</span></span>
* <span data-ttu-id="9e666-560">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="9e666-560">HTTP_HOST</span></span>
* <span data-ttu-id="9e666-561">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="9e666-561">HTTP_REFERER</span></span>
* <span data-ttu-id="9e666-562">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="9e666-562">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="9e666-563">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9e666-563">HTTPS</span></span>
* <span data-ttu-id="9e666-564">Protokol IPV6</span><span class="sxs-lookup"><span data-stu-id="9e666-564">IPV6</span></span>
* <span data-ttu-id="9e666-565">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="9e666-565">QUERY_STRING</span></span>
* <span data-ttu-id="9e666-566">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="9e666-566">REMOTE_ADDR</span></span>
* <span data-ttu-id="9e666-567">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="9e666-567">REMOTE_PORT</span></span>
* <span data-ttu-id="9e666-568">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="9e666-568">REQUEST_FILENAME</span></span>
* <span data-ttu-id="9e666-569">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="9e666-569">REQUEST_METHOD</span></span>
* <span data-ttu-id="9e666-570">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="9e666-570">REQUEST_SCHEME</span></span>
* <span data-ttu-id="9e666-571">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="9e666-571">REQUEST_URI</span></span>
* <span data-ttu-id="9e666-572">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="9e666-572">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="9e666-573">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="9e666-573">SERVER_ADDR</span></span>
* <span data-ttu-id="9e666-574">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="9e666-574">SERVER_PORT</span></span>
* <span data-ttu-id="9e666-575">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="9e666-575">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="9e666-576">ČAS</span><span class="sxs-lookup"><span data-stu-id="9e666-576">TIME</span></span>
* <span data-ttu-id="9e666-577">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="9e666-577">TIME_DAY</span></span>
* <span data-ttu-id="9e666-578">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="9e666-578">TIME_HOUR</span></span>
* <span data-ttu-id="9e666-579">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="9e666-579">TIME_MIN</span></span>
* <span data-ttu-id="9e666-580">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="9e666-580">TIME_MON</span></span>
* <span data-ttu-id="9e666-581">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="9e666-581">TIME_SEC</span></span>
* <span data-ttu-id="9e666-582">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="9e666-582">TIME_WDAY</span></span>
* <span data-ttu-id="9e666-583">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="9e666-583">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="9e666-584">Pravidla modulu přepisování adres URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="9e666-584">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="9e666-585">Chcete-li použít stejnou sadu pravidel, která se vztahuje <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>na modul přepisování adres URL služby IIS, použijte .</span><span class="sxs-lookup"><span data-stu-id="9e666-585">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="9e666-586">Ujistěte se, že soubor pravidel je nasazen s aplikací.</span><span class="sxs-lookup"><span data-stu-id="9e666-586">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="9e666-587">Nenasměrujte middleware na použití souboru *web.config* aplikace při spuštění ve službě Windows Server IIS.</span><span class="sxs-lookup"><span data-stu-id="9e666-587">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="9e666-588">Ve službě IIS by tato pravidla měla být uložena mimo soubor *web.config* aplikace, aby nedošlo ke konfliktům s modulem Přepisování služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9e666-588">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="9e666-589">Další informace a příklady pravidel modulu přepisování adres URL služby IIS naleznete [v tématu Použití modulu pro přepis adres URL 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [odkazu na konfiguraci modulu pro přepis adres URL](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="9e666-589">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="9e666-590">A <xref:System.IO.StreamReader> se používá ke čtení pravidel ze souboru pravidel *IISUrlRewrite.xml:*</span><span class="sxs-lookup"><span data-stu-id="9e666-590">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="9e666-591">Ukázková aplikace přepíše `/iis-rules-rewrite/(.*)` požadavky `/rewritten?id=$1`z do .</span><span class="sxs-lookup"><span data-stu-id="9e666-591">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="9e666-592">Odpověď je odeslána klientovi se stavovým kódem *200 - OK.*</span><span class="sxs-lookup"><span data-stu-id="9e666-592">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="9e666-593">Původní požadavek:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="9e666-593">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Okno prohlížeče s nástroji pro vývojáře sledující požadavek a odpověď](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="9e666-595">Pokud máte aktivní modul pro přepisování služby IIS s nakonfigurovanými pravidly na úrovni serveru, která by měla nepříznivý vliv na vaši aplikaci, můžete modul pro přepisování služby IIS pro aplikaci zakázat.</span><span class="sxs-lookup"><span data-stu-id="9e666-595">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="9e666-596">Další informace naleznete [v tématu Zakázání modulů iis](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="9e666-596">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="9e666-597">Nepodporované funkce</span><span class="sxs-lookup"><span data-stu-id="9e666-597">Unsupported features</span></span>

<span data-ttu-id="9e666-598">Middleware vydaný s ASP.NET Core 2.x nepodporuje následující funkce modulu přepisování adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="9e666-598">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="9e666-599">Pravidla pro odchozí provoz</span><span class="sxs-lookup"><span data-stu-id="9e666-599">Outbound Rules</span></span>
* <span data-ttu-id="9e666-600">Proměnné vlastního serveru</span><span class="sxs-lookup"><span data-stu-id="9e666-600">Custom Server Variables</span></span>
* <span data-ttu-id="9e666-601">Zástupné znaky</span><span class="sxs-lookup"><span data-stu-id="9e666-601">Wildcards</span></span>
* <span data-ttu-id="9e666-602">Adresa LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="9e666-602">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="9e666-603">Podporované proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="9e666-603">Supported server variables</span></span>

<span data-ttu-id="9e666-604">Middleware podporuje následující proměnné serveru serveru IIS URL Rewrite Module:</span><span class="sxs-lookup"><span data-stu-id="9e666-604">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="9e666-605">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="9e666-605">CONTENT_LENGTH</span></span>
* <span data-ttu-id="9e666-606">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="9e666-606">CONTENT_TYPE</span></span>
* <span data-ttu-id="9e666-607">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="9e666-607">HTTP_ACCEPT</span></span>
* <span data-ttu-id="9e666-608">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="9e666-608">HTTP_CONNECTION</span></span>
* <span data-ttu-id="9e666-609">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="9e666-609">HTTP_COOKIE</span></span>
* <span data-ttu-id="9e666-610">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="9e666-610">HTTP_HOST</span></span>
* <span data-ttu-id="9e666-611">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="9e666-611">HTTP_REFERER</span></span>
* <span data-ttu-id="9e666-612">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="9e666-612">HTTP_URL</span></span>
* <span data-ttu-id="9e666-613">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="9e666-613">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="9e666-614">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9e666-614">HTTPS</span></span>
* <span data-ttu-id="9e666-615">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="9e666-615">LOCAL_ADDR</span></span>
* <span data-ttu-id="9e666-616">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="9e666-616">QUERY_STRING</span></span>
* <span data-ttu-id="9e666-617">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="9e666-617">REMOTE_ADDR</span></span>
* <span data-ttu-id="9e666-618">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="9e666-618">REMOTE_PORT</span></span>
* <span data-ttu-id="9e666-619">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="9e666-619">REQUEST_FILENAME</span></span>
* <span data-ttu-id="9e666-620">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="9e666-620">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="9e666-621">Můžete také získat <xref:Microsoft.Extensions.FileProviders.IFileProvider> prostřednictvím <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="9e666-621">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="9e666-622">Tento přístup může poskytnout větší flexibilitu pro umístění souborů pravidel přepisování.</span><span class="sxs-lookup"><span data-stu-id="9e666-622">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="9e666-623">Ujistěte se, že soubory pravidel přepisu jsou nasazeny na server na cestě, kterou zadáte.</span><span class="sxs-lookup"><span data-stu-id="9e666-623">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="9e666-624">Pravidlo založené na metodách</span><span class="sxs-lookup"><span data-stu-id="9e666-624">Method-based rule</span></span>

<span data-ttu-id="9e666-625">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k implementaci vlastní logiky pravidla v metodě.</span><span class="sxs-lookup"><span data-stu-id="9e666-625">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="9e666-626">`Add`zpřístupní <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, který <xref:Microsoft.AspNetCore.Http.HttpContext> zpřístupňuje pro použití v metodě.</span><span class="sxs-lookup"><span data-stu-id="9e666-626">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="9e666-627">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) určuje, jak je zpracováno další zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="9e666-627">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="9e666-628">Nastavte hodnotu na <xref:Microsoft.AspNetCore.Rewrite.RuleResult> jedno z polí popsaných v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="9e666-628">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="9e666-629">Akce</span><span class="sxs-lookup"><span data-stu-id="9e666-629">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="9e666-630">`RuleResult.ContinueRules`(výchozí)</span><span class="sxs-lookup"><span data-stu-id="9e666-630">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="9e666-631">Pokračujte v uplatňování pravidel.</span><span class="sxs-lookup"><span data-stu-id="9e666-631">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="9e666-632">Přestaňte používat pravidla a odešlete odpověď.</span><span class="sxs-lookup"><span data-stu-id="9e666-632">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="9e666-633">Přestaňte používat pravidla a odešlete kontext do dalšího middlewaru.</span><span class="sxs-lookup"><span data-stu-id="9e666-633">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="9e666-634">Ukázková aplikace demonstruje metodu, která přesměruje požadavky na cesty, které končí *.xml*.</span><span class="sxs-lookup"><span data-stu-id="9e666-634">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="9e666-635">Pokud je žádost `/file.xml`podána , je `/xmlfiles/file.xml`žádost přesměrována na .</span><span class="sxs-lookup"><span data-stu-id="9e666-635">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="9e666-636">Stavový kód je nastaven na *301 - Trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="9e666-636">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="9e666-637">Když prohlížeč vytvoří nový požadavek na */xmlfiles/file.xml*, middleware statického souboru zobrazí soubor klientovi ze složky *wwwroot/xmlfiles.*</span><span class="sxs-lookup"><span data-stu-id="9e666-637">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="9e666-638">Pro přesměrování explicitně nastavte stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="9e666-638">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="9e666-639">V opačném případě je vrácen stavový kód *200 - OK* a přesměrování se nevyskytuje na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="9e666-639">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="9e666-640">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e666-640">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="9e666-641">Tento přístup můžete také přepsat požadavky.</span><span class="sxs-lookup"><span data-stu-id="9e666-641">This approach can also rewrite requests.</span></span> <span data-ttu-id="9e666-642">Ukázková aplikace ukazuje přepisování cesty pro libovolný požadavek textového souboru, který má být připojen k textovému souboru *file.txt* ze složky *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="9e666-642">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="9e666-643">Middleware statického souboru slouží souboru na základě aktualizované cesty požadavku:</span><span class="sxs-lookup"><span data-stu-id="9e666-643">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="9e666-644">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="9e666-644">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="9e666-645">Pravidlo založené na pravidlech IRule</span><span class="sxs-lookup"><span data-stu-id="9e666-645">IRule-based rule</span></span>

<span data-ttu-id="9e666-646">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k použití logiky pravidla ve <xref:Microsoft.AspNetCore.Rewrite.IRule> třídě, která implementuje rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9e666-646">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="9e666-647">`IRule`poskytuje větší flexibilitu při používání přístupu pravidla založeného na metodách.</span><span class="sxs-lookup"><span data-stu-id="9e666-647">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="9e666-648">Vaše třída implementace může obsahovat konstruktor, který umožňuje <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> předat parametry pro metodu.</span><span class="sxs-lookup"><span data-stu-id="9e666-648">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="9e666-649">Hodnoty parametrů v ukázkové aplikaci `extension` pro `newPath` a a jsou kontrolovány tak, aby splňovaly několik podmínek.</span><span class="sxs-lookup"><span data-stu-id="9e666-649">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="9e666-650">Musí `extension` obsahovat hodnotu a hodnota musí být *.png*, *.jpg*nebo *.gif*.</span><span class="sxs-lookup"><span data-stu-id="9e666-650">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="9e666-651">Pokud `newPath` není platný, <xref:System.ArgumentException> je hozen.</span><span class="sxs-lookup"><span data-stu-id="9e666-651">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="9e666-652">Pokud je požadavek na *soubor image.png*podán `/png-images/image.png`, je požadavek přesměrován na .</span><span class="sxs-lookup"><span data-stu-id="9e666-652">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="9e666-653">Pokud je požadavek na *image.jpg*, požadavek je přesměrován na `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="9e666-653">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="9e666-654">Stavový kód je nastaven na *301 - Trvale přesunuta*a `context.Result` je nastavena na zastavení zpracování pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="9e666-654">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="9e666-655">Původní požadavek:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="9e666-655">Original Request: `/image.png`</span></span>

![Okno prohlížeče s nástroji pro vývojáře, které sledují požadavky a odpovědi pro soubor image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="9e666-657">Původní požadavek:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="9e666-657">Original Request: `/image.jpg`</span></span>

![Okno prohlížeče s nástroji pro vývojáře, které sledují požadavky a odpovědi na obrázek.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="9e666-659">Příklady regexu</span><span class="sxs-lookup"><span data-stu-id="9e666-659">Regex examples</span></span>

| <span data-ttu-id="9e666-660">Cíl</span><span class="sxs-lookup"><span data-stu-id="9e666-660">Goal</span></span> | <span data-ttu-id="9e666-661">Řetězec regex &</span><span class="sxs-lookup"><span data-stu-id="9e666-661">Regex String &</span></span><br><span data-ttu-id="9e666-662">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="9e666-662">Match Example</span></span> | <span data-ttu-id="9e666-663">Náhradní řetězec &</span><span class="sxs-lookup"><span data-stu-id="9e666-663">Replacement String &</span></span><br><span data-ttu-id="9e666-664">Příklad výstupu</span><span class="sxs-lookup"><span data-stu-id="9e666-664">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="9e666-665">Přepsat cestu do řetězce dotazu</span><span class="sxs-lookup"><span data-stu-id="9e666-665">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="9e666-666">Pruh vlečené lomítko</span><span class="sxs-lookup"><span data-stu-id="9e666-666">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="9e666-667">Vynutit koncové lomítko</span><span class="sxs-lookup"><span data-stu-id="9e666-667">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="9e666-668">Vyhněte se přepisování konkrétních požadavků</span><span class="sxs-lookup"><span data-stu-id="9e666-668">Avoid rewriting specific requests</span></span> | <span data-ttu-id="9e666-669">`^(.*)(?<!\.axd)$` nebo `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="9e666-669">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="9e666-670">Ano:`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="9e666-670">Yes: `/resource.htm`</span></span><br><span data-ttu-id="9e666-671">Ne:`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="9e666-671">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="9e666-672">Změna uspořádání segmentů adres URL</span><span class="sxs-lookup"><span data-stu-id="9e666-672">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="9e666-673">Nahrazení segmentu adresy URL</span><span class="sxs-lookup"><span data-stu-id="9e666-673">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9e666-674">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9e666-674">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="9e666-675">Regulární výrazy v rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="9e666-675">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="9e666-676">Jazyk regulárních výrazů - stručný přehled</span><span class="sxs-lookup"><span data-stu-id="9e666-676">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="9e666-677">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="9e666-677">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="9e666-678">Použití modulu pro přepis adres URL 2.0 (pro službu IIS)</span><span class="sxs-lookup"><span data-stu-id="9e666-678">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="9e666-679">Odkaz na konfiguraci modulu pro přepis adresy URL</span><span class="sxs-lookup"><span data-stu-id="9e666-679">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="9e666-680">Fórum přepisovacího modulu adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="9e666-680">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="9e666-681">Zachování jednoduché struktury adres URL</span><span class="sxs-lookup"><span data-stu-id="9e666-681">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="9e666-682">10 URL Přepisování Tipy a triky</span><span class="sxs-lookup"><span data-stu-id="9e666-682">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="9e666-683">Chcete-li lomítko nebo ne lomítko</span><span class="sxs-lookup"><span data-stu-id="9e666-683">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
