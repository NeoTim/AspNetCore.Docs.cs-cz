---
<span data-ttu-id="c2ac9-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-102">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-103">'Identity'</span></span>
- <span data-ttu-id="c2ac9-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-105">'Razor'</span></span>
- <span data-ttu-id="c2ac9-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-106">'SignalR' uid:</span></span> 

---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="c2ac9-107">Middleware pro přepis adres URL v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2ac9-107">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="c2ac9-108">Od [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="c2ac9-108">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c2ac9-109">Tento dokument zavádí přepis adres URL s pokyny k použití middlewaru přepisu adresy URL v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-109">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="c2ac9-110">Přepsání adresy URL je to, že se jedná o úpravu adres URL žádostí na základě jednoho nebo více předdefinovaných pravidel.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-110">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="c2ac9-111">Přepsání adresy URL vytvoří abstrakci mezi umístěními prostředků a jejich adresami tak, aby umístění a adresy nebyly pevně propojené.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-111">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="c2ac9-112">Přepsání adresy URL je v několika scénářích užitečné pro:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-112">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="c2ac9-113">Dočasné nebo trvalé přesunutí nebo nahrazení prostředků serveru a udržování stabilních lokátorů pro tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-113">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="c2ac9-114">Rozdělte zpracování požadavků napříč různými aplikacemi nebo napříč oblastmi jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-114">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="c2ac9-115">Umožňuje odebrat, přidat nebo změnit uspořádání segmentů adresy URL příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-115">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="c2ac9-116">Optimalizujte veřejné adresy URL pro optimalizaci vyhledávače (SEO).</span><span class="sxs-lookup"><span data-stu-id="c2ac9-116">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="c2ac9-117">Povolí použití přívětivých veřejných adres URL, které návštěvníkům pomůžou odhadnout obsah vrácený požadavkem na prostředek.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-117">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="c2ac9-118">Přesměrovat nezabezpečené požadavky na zabezpečené koncové body.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-118">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="c2ac9-119">Zabránit hotlinking, kde externí lokalita používá hostovaný statický prostředek na jiné lokalitě propojením prostředku s vlastním obsahem.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-119">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="c2ac9-120">Přepsání adresy URL může snížit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-120">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="c2ac9-121">Pokud je to možné, omezte počet a složitost pravidel.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-121">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="c2ac9-122">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c2ac9-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="c2ac9-123">Přesměrování adresy URL a přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-123">URL redirect and URL rewrite</span></span>

<span data-ttu-id="c2ac9-124">Rozdíl mezi přesměrováním *URL* a *přepsáním adresy URL* je malý, ale má důležitý dopad na poskytování prostředků klientům.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-124">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="c2ac9-125">Rozhraní middleware pro přepis adres URL ASP.NET Core je schopné splnit nutnost obou.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-125">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="c2ac9-126">*Přesměrování adresy URL* zahrnuje i operaci na straně klienta, kde klient má pokyn k přístupu k prostředku na jiné adrese, než jakou požadoval klient původně.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-126">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="c2ac9-127">To vyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-127">This requires a round trip to the server.</span></span> <span data-ttu-id="c2ac9-128">Adresa URL pro přesměrování vracená klientovi se zobrazí v adresním řádku prohlížeče, když klient vytvoří nový požadavek na prostředek.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-128">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="c2ac9-129">Pokud `/resource` je *přesměrován* na `/different-resource` , server odpoví, že by měl klient získat prostředek na `/different-resource` stavovém kódu, což značí, že přesměrování je buď dočasné, nebo trvalé.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-129">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Koncový bod služby WebAPI se dočasně změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="c2ac9-135">Při přesměrování požadavků na jinou adresu URL určete, zda je přesměrování trvalé nebo dočasné, zadáním stavového kódu s odpovědí:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-135">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="c2ac9-136">V případě, že má prostředek novou, trvalou adresu URL a chcete dát klientovi pokyn, aby všechny budoucí požadavky na prostředek používaly novou adresu URL, se použije kód *trvale přesunutý stav 301* .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-136">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="c2ac9-137">*Klient může ukládat odpověď do mezipaměti a znovu ji použít, když se přijme stavový kód 301.*</span><span class="sxs-lookup"><span data-stu-id="c2ac9-137">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="c2ac9-138">V případě, že je přesměrování dočasné nebo obecně se může změnit, je použit stavový kód *302* .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-138">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="c2ac9-139">Stavový kód 302 indikuje klientovi, aby neukládal adresu URL a použil ho v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-139">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="c2ac9-140">Další informace o stavových kódech najdete v [dokumentu RFC 2616: definice stavového kódu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c2ac9-140">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="c2ac9-141">*Přepsání adresy URL* je operace na straně serveru, která poskytuje prostředek z jiné adresy prostředku, než jakou požaduje klient.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-141">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="c2ac9-142">Přepsání adresy URL nevyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-142">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="c2ac9-143">Přepsaná adresa URL se nevrátí klientovi a nezobrazí se v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-143">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="c2ac9-144">Pokud `/resource` je *přepsáno* na `/different-resource` , server *interně* načte a vrátí prostředek na `/different-resource` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-144">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="c2ac9-145">I když klient může být schopný načíst prostředek na adrese URL přepsané adresy, klient nebude informovat o tom, že prostředek existuje na přepsané adrese URL, když odešle svůj požadavek a obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-145">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Koncový bod služby WebAPI se změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="c2ac9-150">Ukázková aplikace pro přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-150">URL rewriting sample app</span></span>

<span data-ttu-id="c2ac9-151">Pomocí [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)můžete prozkoumat funkce middlewaru přepsaného adresou URL.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-151">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="c2ac9-152">Aplikace použije pravidla přesměrování a přepisu a zobrazí přesměrovanou nebo přepsanou adresu URL pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-152">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="c2ac9-153">Kdy použít middleware přepisu adresy URL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-153">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="c2ac9-154">Pokud nemůžete použít následující přístupy, použijte middleware přepisující adresu URL:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-154">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="c2ac9-155">Modul pro přepsání adresy URL ve službě IIS v systému Windows Server</span><span class="sxs-lookup"><span data-stu-id="c2ac9-155">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="c2ac9-156">Apache mod_rewrite modul na serveru Apache</span><span class="sxs-lookup"><span data-stu-id="c2ac9-156">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="c2ac9-157">Přepsání adresy URL na Nginx</span><span class="sxs-lookup"><span data-stu-id="c2ac9-157">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="c2ac9-158">Použijte taky middleware, pokud je aplikace hostována na [serveru http. sys](xref:fundamentals/servers/httpsys) (dříve nazývaném weblisten).</span><span class="sxs-lookup"><span data-stu-id="c2ac9-158">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="c2ac9-159">Hlavními důvody pro použití technologie přepisování adres URL založené na serveru ve službě IIS, Apache a Nginx jsou:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-159">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="c2ac9-160">Middleware nepodporuje všechny funkce těchto modulů.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-160">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="c2ac9-161">Některé funkce v modulech serveru nefungují s ASP.NET Core projekty, jako jsou `IsFile` `IsDirectory` omezení a modulu IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-161">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="c2ac9-162">V těchto scénářích místo toho použijte middleware.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-162">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="c2ac9-163">Výkon middleware se pravděpodobně neshoduje s modulem.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-163">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="c2ac9-164">Srovnávací testy jsou jediným způsobem, jak zjistit, který přístup snižuje výkon, který je v nejvyšší nebo v případě, že je snížený výkon.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-164">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="c2ac9-165">Balíček</span><span class="sxs-lookup"><span data-stu-id="c2ac9-165">Package</span></span>

<span data-ttu-id="c2ac9-166">Middleware pro přepis adres URL poskytuje balíček [Microsoft. AspNetCore. Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) , který je implicitně zahrnutý v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-166">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="c2ac9-167">Rozšíření a možnosti</span><span class="sxs-lookup"><span data-stu-id="c2ac9-167">Extension and options</span></span>

<span data-ttu-id="c2ac9-168">Vytvořte pravidla přepsání a přesměrování adresy URL vytvořením instance třídy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) s metodami rozšíření pro každé pravidlo přepsání.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-168">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="c2ac9-169">Řetězení více pravidel v pořadí, ve kterém se mají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-169">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="c2ac9-170">Rozhraní se `RewriteOptions` předává do middleware přepisování adresy URL při jeho přidání do kanálu požadavků pomocí <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :</span><span class="sxs-lookup"><span data-stu-id="c2ac9-170">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="c2ac9-171">Přesměrovat neexistující na webovou</span><span class="sxs-lookup"><span data-stu-id="c2ac9-171">Redirect non-www to www</span></span>

<span data-ttu-id="c2ac9-172">Tři možnosti umožňují, aby aplikace přesměrovala `www` požadavky bez požadavků na `www` :</span><span class="sxs-lookup"><span data-stu-id="c2ac9-172">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="c2ac9-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Trvale přesměrujte požadavek na `www` subdoménu, pokud je požadavek jiný než `www` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="c2ac9-174">Přesměruje stavový kód [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-174">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="c2ac9-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Přesměrujte požadavek na `www` subdoménu, pokud je příchozí požadavek jiný než `www` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="c2ac9-176">Přesměruje stavový kód [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-176">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="c2ac9-177">Přetížení umožňuje poskytnout stavový kód pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-177">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="c2ac9-178">Použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy pro přiřazení stavového kódu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-178">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="c2ac9-179">Přesměrování adresy URL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-179">URL redirect</span></span>

<span data-ttu-id="c2ac9-180">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> k přesměrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-180">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="c2ac9-181">První parametr obsahuje váš regulární výraz pro porovnání s cestou příchozí adresy URL.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-181">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="c2ac9-182">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-182">The second parameter is the replacement string.</span></span> <span data-ttu-id="c2ac9-183">Třetí parametr, pokud je k dispozici, určuje stavový kód.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-183">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="c2ac9-184">Pokud nezadáte stavový kód, stavový kód se standardně *302 – Nalezeno*, což znamená, že prostředek je dočasně přesunut nebo nahrazen.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-184">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="c2ac9-185">V prohlížeči s povolenými vývojářskými nástroji vytvořte žádost o ukázkovou aplikaci s cestou `/redirect-rule/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-185">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="c2ac9-186">Regulární výraz odpovídá cestě požadavku na `redirect-rule/(.*)` a cesta je nahrazena řetězcem `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-186">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="c2ac9-187">Adresa URL pro přesměrování se pošle zpátky do klienta s kódem stavu 302, který se *našel* .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-187">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="c2ac9-188">Prohlížeč vytvoří novou žádost na adrese URL pro přesměrování, která se zobrazí v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-188">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="c2ac9-189">Vzhledem k tomu, že se žádná pravidla v ukázkové aplikaci neshodují s adresou URL pro přesměrování:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-189">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="c2ac9-190">Druhá žádost obdrží odpověď *200-OK* od aplikace.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-190">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="c2ac9-191">Tělo odpovědi zobrazuje adresu URL pro přesměrování.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-191">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="c2ac9-192">Při *přesměrování*adresy URL se na Server provede cyklická výměna.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-192">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="c2ac9-193">Buďte opatrní při vytváření pravidel přesměrování.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-193">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="c2ac9-194">Pravidla přesměrování se vyhodnocují při každém požadavku na aplikaci, včetně po přesměrování.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-194">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="c2ac9-195">Je snadné vytvořit *smyčku nekonečných přesměrování*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-195">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="c2ac9-196">Původní požadavek:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-196">Original Request: `/redirect-rule/1234/5678`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="c2ac9-198">Část výrazu obsažená v závorkách se nazývá *Skupina zachycení*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-198">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="c2ac9-199">Tečka ( `.` ) výrazu znamená, že *odpovídá libovolnému znaku*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-199">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="c2ac9-200">Hvězdička ( `*` ) značí, *že se předchozí znak rovná nule nebo vícekrát*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-200">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="c2ac9-201">Proto jsou poslední dva segmenty cesty adresy URL `1234/5678` zachyceny skupinou zachycení `(.*)` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-201">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="c2ac9-202">Jakákoli hodnota, kterou zadáte v adrese URL požadavku, potom, co `redirect-rule/` je tato jediná skupina zachycení zachycena.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-202">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="c2ac9-203">V řetězci pro nahrazení jsou zachycené skupiny vloženy do řetězce s znakem dolaru ( `$` ) následovaným pořadovým číslem záznamu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-203">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="c2ac9-204">První hodnota skupiny zachycení je získána s `$1` , druhým s `$2` a pokračuje v pořadí pro skupiny zachycení ve vašem regulárním výrazu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-204">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="c2ac9-205">Regulární výraz pravidla přesměrování v ukázkové aplikaci obsahuje jenom jednu zachycenou skupinu, takže v náhradním řetězci je jenom jedna vložená skupina, která je `$1` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-205">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="c2ac9-206">Po použití pravidla se adresa URL bude nacházet `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-206">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="c2ac9-207">Přesměrování adresy URL na zabezpečený koncový bod</span><span class="sxs-lookup"><span data-stu-id="c2ac9-207">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="c2ac9-208">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> k přesměrování požadavků HTTP na stejného hostitele a cestu pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-208">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="c2ac9-209">Pokud kód stavu není zadaný, použije se ve výchozím nastavení middleware *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-209">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="c2ac9-210">Pokud port není dodán:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-210">If the port isn't supplied:</span></span>

* <span data-ttu-id="c2ac9-211">Ve výchozím nastavení se používá middleware `null` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-211">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="c2ac9-212">Schéma se změní na `https` (protokol HTTPS) a klient přistupuje k prostředku na portu 443.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-212">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="c2ac9-213">Následující příklad ukazuje, jak nastavit stavový kód na *301 – trvale přesunuto* a změnit port na 5001.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-213">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="c2ac9-214">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> k přesměrování nezabezpečených požadavků na stejného hostitele a cestu s protokolem Secure https na portu 443.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-214">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="c2ac9-215">Middleware nastaví stavový kód na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-215">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="c2ac9-216">Při přesměrování na zabezpečený koncový bod bez požadavku na další pravidla přesměrování doporučujeme použít middleware pro přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-216">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="c2ac9-217">Další informace najdete v tématu věnovaném [vykonání protokolu HTTPS](xref:security/enforcing-ssl#require-https) .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-217">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="c2ac9-218">Ukázková aplikace dokáže demonstrovat, jak používat `AddRedirectToHttps` nebo `AddRedirectToHttpsPermanent` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-218">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="c2ac9-219">Přidejte metodu rozšíření do `RewriteOptions` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-219">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="c2ac9-220">Vytvořte nezabezpečenou žádost o aplikaci na libovolné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-220">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="c2ac9-221">Zrušte upozornění zabezpečení prohlížeče, že certifikát podepsaný svým držitelem je nedůvěryhodný, nebo vytvořte výjimku pro důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-221">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="c2ac9-222">Původní požadavek s použitím `AddRedirectToHttps(301, 5001)` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-222">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="c2ac9-224">Původní požadavek s použitím `AddRedirectToHttpsPermanent` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-224">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="c2ac9-226">Přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-226">URL rewrite</span></span>

<span data-ttu-id="c2ac9-227">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> k vytvoření pravidla pro přepis adres URL.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-227">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="c2ac9-228">První parametr obsahuje regulární výraz pro porovnání na cestě příchozích adres URL.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-228">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="c2ac9-229">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-229">The second parameter is the replacement string.</span></span> <span data-ttu-id="c2ac9-230">Třetí parametr, `skipRemainingRules: {true|false}` označuje middleware bez ohledu na to, zda má přeskočit další pravidla přepsání, pokud je použito aktuální pravidlo.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-230">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="c2ac9-231">Původní požadavek:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-231">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="c2ac9-233">Kosočtverce ( `^` ) na začátku výrazu znamená, že shoda začíná na začátku cesty URL.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-233">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="c2ac9-234">V předchozím příkladu s pravidlem přesměrování `redirect-rule/(.*)` není `^` na začátku regulárního výrazu k dispozici žádná kosočtverce ().</span><span class="sxs-lookup"><span data-stu-id="c2ac9-234">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="c2ac9-235">Proto jakékoli znaky mohou předcházet `redirect-rule/` v cestě k úspěšné shodě.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-235">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="c2ac9-236">Cesta</span><span class="sxs-lookup"><span data-stu-id="c2ac9-236">Path</span></span>                               | <span data-ttu-id="c2ac9-237">Shoda</span><span class="sxs-lookup"><span data-stu-id="c2ac9-237">Match</span></span> |
| ---
<span data-ttu-id="c2ac9-238">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-239">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-240">'Identity'</span></span>
- <span data-ttu-id="c2ac9-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-242">'Razor'</span></span>
- <span data-ttu-id="c2ac9-243">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-244">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-245">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-246">'Identity'</span></span>
- <span data-ttu-id="c2ac9-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-248">'Razor'</span></span>
- <span data-ttu-id="c2ac9-249">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-250">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-251">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-252">'Identity'</span></span>
- <span data-ttu-id="c2ac9-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-254">'Razor'</span></span>
- <span data-ttu-id="c2ac9-255">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-256">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-257">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-258">'Identity'</span></span>
- <span data-ttu-id="c2ac9-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-260">'Razor'</span></span>
- <span data-ttu-id="c2ac9-261">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-262">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-263">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-264">'Identity'</span></span>
- <span data-ttu-id="c2ac9-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-266">'Razor'</span></span>
- <span data-ttu-id="c2ac9-267">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-268">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-269">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-270">'Identity'</span></span>
- <span data-ttu-id="c2ac9-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-272">'Razor'</span></span>
- <span data-ttu-id="c2ac9-273">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-274">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-275">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-276">'Identity'</span></span>
- <span data-ttu-id="c2ac9-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-278">'Razor'</span></span>
- <span data-ttu-id="c2ac9-279">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-280">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-281">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-282">'Identity'</span></span>
- <span data-ttu-id="c2ac9-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-284">'Razor'</span></span>
- <span data-ttu-id="c2ac9-285">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-286">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-287">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-288">'Identity'</span></span>
- <span data-ttu-id="c2ac9-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-290">'Razor'</span></span>
- <span data-ttu-id="c2ac9-291">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-292">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-293">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-294">'Identity'</span></span>
- <span data-ttu-id="c2ac9-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-296">'Razor'</span></span>
- <span data-ttu-id="c2ac9-297">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-298">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-299">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-300">'Identity'</span></span>
- <span data-ttu-id="c2ac9-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-302">'Razor'</span></span>
- <span data-ttu-id="c2ac9-303">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-304">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-305">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-306">'Identity'</span></span>
- <span data-ttu-id="c2ac9-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-308">'Razor'</span></span>
- <span data-ttu-id="c2ac9-309">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-309">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-310">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-311">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-312">'Identity'</span></span>
- <span data-ttu-id="c2ac9-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-314">'Razor'</span></span>
- <span data-ttu-id="c2ac9-315">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-316">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-317">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-318">'Identity'</span></span>
- <span data-ttu-id="c2ac9-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-320">'Razor'</span></span>
- <span data-ttu-id="c2ac9-321">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-322">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-323">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-324">'Identity'</span></span>
- <span data-ttu-id="c2ac9-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-326">'Razor'</span></span>
- <span data-ttu-id="c2ac9-327">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-327">'SignalR' uid:</span></span> 

<span data-ttu-id="c2ac9-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Ano | | `/my-cool-redirect-rule/1234/5678` | Ano | | `/anotherredirect-rule/1234/5678`  | Ano |</span><span class="sxs-lookup"><span data-stu-id="c2ac9-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="c2ac9-329">Pravidlo přepsaného zápisu `^rewrite-rule/(\d+)/(\d+)` odpovídá pouze cestám, pokud začínají na `rewrite-rule/` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-329">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="c2ac9-330">V následující tabulce si všimněte rozdílu v porovnání.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-330">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="c2ac9-331">Cesta</span><span class="sxs-lookup"><span data-stu-id="c2ac9-331">Path</span></span>                              | <span data-ttu-id="c2ac9-332">Shoda</span><span class="sxs-lookup"><span data-stu-id="c2ac9-332">Match</span></span> |
| ---
<span data-ttu-id="c2ac9-333">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-334">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-335">'Identity'</span></span>
- <span data-ttu-id="c2ac9-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-337">'Razor'</span></span>
- <span data-ttu-id="c2ac9-338">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-339">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-340">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-341">'Identity'</span></span>
- <span data-ttu-id="c2ac9-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-343">'Razor'</span></span>
- <span data-ttu-id="c2ac9-344">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-345">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-346">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-347">'Identity'</span></span>
- <span data-ttu-id="c2ac9-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-349">'Razor'</span></span>
- <span data-ttu-id="c2ac9-350">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-351">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-352">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-353">'Identity'</span></span>
- <span data-ttu-id="c2ac9-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-355">'Razor'</span></span>
- <span data-ttu-id="c2ac9-356">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-357">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-358">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-359">'Identity'</span></span>
- <span data-ttu-id="c2ac9-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-361">'Razor'</span></span>
- <span data-ttu-id="c2ac9-362">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-363">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-364">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-365">'Identity'</span></span>
- <span data-ttu-id="c2ac9-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-367">'Razor'</span></span>
- <span data-ttu-id="c2ac9-368">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-369">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-370">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-371">'Identity'</span></span>
- <span data-ttu-id="c2ac9-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-373">'Razor'</span></span>
- <span data-ttu-id="c2ac9-374">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-375">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-376">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-377">'Identity'</span></span>
- <span data-ttu-id="c2ac9-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-379">'Razor'</span></span>
- <span data-ttu-id="c2ac9-380">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-381">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-382">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-383">'Identity'</span></span>
- <span data-ttu-id="c2ac9-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-385">'Razor'</span></span>
- <span data-ttu-id="c2ac9-386">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-387">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-388">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-389">'Identity'</span></span>
- <span data-ttu-id="c2ac9-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-391">'Razor'</span></span>
- <span data-ttu-id="c2ac9-392">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-393">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-394">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-395">'Identity'</span></span>
- <span data-ttu-id="c2ac9-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-397">'Razor'</span></span>
- <span data-ttu-id="c2ac9-398">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-399">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-400">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-401">'Identity'</span></span>
- <span data-ttu-id="c2ac9-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-403">'Razor'</span></span>
- <span data-ttu-id="c2ac9-404">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-405">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-406">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-407">'Identity'</span></span>
- <span data-ttu-id="c2ac9-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-409">'Razor'</span></span>
- <span data-ttu-id="c2ac9-410">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-411">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-412">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-413">'Identity'</span></span>
- <span data-ttu-id="c2ac9-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-415">'Razor'</span></span>
- <span data-ttu-id="c2ac9-416">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-416">'SignalR' uid:</span></span> 

<span data-ttu-id="c2ac9-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Ano | | `/my-cool-rewrite-rule/1234/5678` | Žádné | | `/anotherrewrite-rule/1234/5678`  | Žádné |</span><span class="sxs-lookup"><span data-stu-id="c2ac9-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="c2ac9-418">Po `^rewrite-rule/` části výrazu jsou k dispozici dvě skupiny zachycení, `(\d+)/(\d+)` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-418">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="c2ac9-419">`\d`Značí *shodu s číslicí (číslem)*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-419">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="c2ac9-420">Znaménko plus ( `+` ) znamená, že se *shoduje s jedním nebo více předcházejícím znakem*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-420">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="c2ac9-421">Proto musí adresa URL obsahovat číslo následované lomítkem, za kterým následuje jiné číslo.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-421">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="c2ac9-422">Tyto skupiny zachycení jsou vloženy do přepsané adresy URL jako `$1` a `$2` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-422">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="c2ac9-423">Náhradní řetězec pravidla přepsaní umístí zachycené skupiny do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-423">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="c2ac9-424">Požadovaná cesta k `/rewrite-rule/1234/5678` je přepsána za účelem získání prostředku v `/rewritten?var1=1234&var2=5678` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-424">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="c2ac9-425">Pokud se v původním požadavku nachází řetězec dotazu, bude při přepisování adresy URL zachován.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-425">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="c2ac9-426">K získání prostředku neexistuje žádná Přenosová cesta k serveru.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-426">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="c2ac9-427">Pokud prostředek existuje, načte se a vrátí do klienta s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-427">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="c2ac9-428">Vzhledem k tomu, že klient není přesměrován, adresa URL v adresním řádku prohlížeče se nezmění.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-428">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="c2ac9-429">Klienti nerozpoznají, že na serveru došlo k operaci přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-429">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="c2ac9-430">`skipRemainingRules: true`Kdykoli je to možné, protože vyhovující pravidla jsou výpočetně náročná a zvyšují dobu odezvy aplikace.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-430">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="c2ac9-431">Pro nejrychlejší aplikační odpověď:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-431">For the fastest app response:</span></span>
>
> * <span data-ttu-id="c2ac9-432">Seřazení pravidel přepisu z nejčastěji spárovaného pravidla na nejméně často spárované pravidlo.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-432">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="c2ac9-433">Pokud dojde ke shodě a není vyžadováno žádné další zpracování pravidla, přeskočte zpracování zbývajících pravidel.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-433">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="c2ac9-434">Mod_rewrite Apache</span><span class="sxs-lookup"><span data-stu-id="c2ac9-434">Apache mod_rewrite</span></span>

<span data-ttu-id="c2ac9-435">Použijte pravidla mod_rewrite Apache s <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-435">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="c2ac9-436">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-436">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="c2ac9-437">Další informace a příklady pravidel mod_rewrite najdete v tématu [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="c2ac9-437">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="c2ac9-438"><xref:System.IO.StreamReader>Používá se ke čtení pravidel ze souboru pravidel *ApacheModRewrite. txt* :</span><span class="sxs-lookup"><span data-stu-id="c2ac9-438">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="c2ac9-439">Ukázková aplikace přesměrovává požadavky od `/apache-mod-rules-redirect/(.\*)` do `/redirected?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-439">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="c2ac9-440">Stavový kód odpovědi je *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-440">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="c2ac9-441">Původní požadavek:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-441">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="c2ac9-443">Middleware podporuje následující proměnné serveru Apache mod_rewrite:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-443">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="c2ac9-444">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="c2ac9-444">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="c2ac9-445">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="c2ac9-445">HTTP_ACCEPT</span></span>
* <span data-ttu-id="c2ac9-446">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="c2ac9-446">HTTP_CONNECTION</span></span>
* <span data-ttu-id="c2ac9-447">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="c2ac9-447">HTTP_COOKIE</span></span>
* <span data-ttu-id="c2ac9-448">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="c2ac9-448">HTTP_FORWARDED</span></span>
* <span data-ttu-id="c2ac9-449">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="c2ac9-449">HTTP_HOST</span></span>
* <span data-ttu-id="c2ac9-450">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="c2ac9-450">HTTP_REFERER</span></span>
* <span data-ttu-id="c2ac9-451">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="c2ac9-451">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="c2ac9-452">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c2ac9-452">HTTPS</span></span>
* <span data-ttu-id="c2ac9-453">PROTOKOLŮ</span><span class="sxs-lookup"><span data-stu-id="c2ac9-453">IPV6</span></span>
* <span data-ttu-id="c2ac9-454">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="c2ac9-454">QUERY_STRING</span></span>
* <span data-ttu-id="c2ac9-455">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="c2ac9-455">REMOTE_ADDR</span></span>
* <span data-ttu-id="c2ac9-456">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="c2ac9-456">REMOTE_PORT</span></span>
* <span data-ttu-id="c2ac9-457">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="c2ac9-457">REQUEST_FILENAME</span></span>
* <span data-ttu-id="c2ac9-458">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="c2ac9-458">REQUEST_METHOD</span></span>
* <span data-ttu-id="c2ac9-459">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="c2ac9-459">REQUEST_SCHEME</span></span>
* <span data-ttu-id="c2ac9-460">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="c2ac9-460">REQUEST_URI</span></span>
* <span data-ttu-id="c2ac9-461">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="c2ac9-461">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="c2ac9-462">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="c2ac9-462">SERVER_ADDR</span></span>
* <span data-ttu-id="c2ac9-463">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="c2ac9-463">SERVER_PORT</span></span>
* <span data-ttu-id="c2ac9-464">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-464">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="c2ac9-465">ČAS</span><span class="sxs-lookup"><span data-stu-id="c2ac9-465">TIME</span></span>
* <span data-ttu-id="c2ac9-466">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="c2ac9-466">TIME_DAY</span></span>
* <span data-ttu-id="c2ac9-467">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="c2ac9-467">TIME_HOUR</span></span>
* <span data-ttu-id="c2ac9-468">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="c2ac9-468">TIME_MIN</span></span>
* <span data-ttu-id="c2ac9-469">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="c2ac9-469">TIME_MON</span></span>
* <span data-ttu-id="c2ac9-470">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="c2ac9-470">TIME_SEC</span></span>
* <span data-ttu-id="c2ac9-471">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="c2ac9-471">TIME_WDAY</span></span>
* <span data-ttu-id="c2ac9-472">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="c2ac9-472">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="c2ac9-473">Pravidla pro přepsání adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="c2ac9-473">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="c2ac9-474">Pokud chcete použít stejnou sadu pravidel, která se vztahuje na modul IIS URL Rewrite, použijte <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-474">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="c2ac9-475">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-475">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="c2ac9-476">Nesměrujte middleware na použití souboru *Web. config* aplikace při spuštění v systému Windows Server IIS.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-476">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="c2ac9-477">V případě služby IIS by tato pravidla měla být uložena mimo soubor *Web. config* aplikace, aby nedocházelo ke konfliktům s modulem pro přepis služby IIS.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-477">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="c2ac9-478">Další informace a příklady pravidel pro přepis adres URL služby IIS najdete v tématu použití odkazu na modul pro [přepis adres url 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [odkaz na konfiguraci modulu přepisu adresy](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)URL.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-478">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="c2ac9-479"><xref:System.IO.StreamReader>Používá se ke čtení pravidel ze souboru pravidel *IISUrlRewrite. XML* :</span><span class="sxs-lookup"><span data-stu-id="c2ac9-479">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="c2ac9-480">Ukázková aplikace přepíše požadavky od `/iis-rules-rewrite/(.*)` do `/rewritten?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-480">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="c2ac9-481">Odpověď se pošle klientovi s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-481">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="c2ac9-482">Původní požadavek:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-482">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="c2ac9-484">Pokud máte aktivní modul pro přepis služby IIS s nakonfigurovanými pravidly na úrovni serveru, který by ovlivnil vaši aplikaci nežádoucím způsobem, můžete pro aplikaci zakázat modul IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-484">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="c2ac9-485">Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="c2ac9-485">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="c2ac9-486">Nepodporované funkce</span><span class="sxs-lookup"><span data-stu-id="c2ac9-486">Unsupported features</span></span>

<span data-ttu-id="c2ac9-487">Middleware vydaná ASP.NET Core 2. x nepodporuje následující funkce modulu pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-487">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="c2ac9-488">Pravidla pro odchozí provoz</span><span class="sxs-lookup"><span data-stu-id="c2ac9-488">Outbound Rules</span></span>
* <span data-ttu-id="c2ac9-489">Vlastní proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="c2ac9-489">Custom Server Variables</span></span>
* <span data-ttu-id="c2ac9-490">Zástupné znaky</span><span class="sxs-lookup"><span data-stu-id="c2ac9-490">Wildcards</span></span>
* <span data-ttu-id="c2ac9-491">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="c2ac9-491">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="c2ac9-492">Podporované proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="c2ac9-492">Supported server variables</span></span>

<span data-ttu-id="c2ac9-493">Middleware podporuje následující proměnné serveru pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-493">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="c2ac9-494">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="c2ac9-494">CONTENT_LENGTH</span></span>
* <span data-ttu-id="c2ac9-495">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="c2ac9-495">CONTENT_TYPE</span></span>
* <span data-ttu-id="c2ac9-496">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="c2ac9-496">HTTP_ACCEPT</span></span>
* <span data-ttu-id="c2ac9-497">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="c2ac9-497">HTTP_CONNECTION</span></span>
* <span data-ttu-id="c2ac9-498">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="c2ac9-498">HTTP_COOKIE</span></span>
* <span data-ttu-id="c2ac9-499">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="c2ac9-499">HTTP_HOST</span></span>
* <span data-ttu-id="c2ac9-500">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="c2ac9-500">HTTP_REFERER</span></span>
* <span data-ttu-id="c2ac9-501">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-501">HTTP_URL</span></span>
* <span data-ttu-id="c2ac9-502">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="c2ac9-502">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="c2ac9-503">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c2ac9-503">HTTPS</span></span>
* <span data-ttu-id="c2ac9-504">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="c2ac9-504">LOCAL_ADDR</span></span>
* <span data-ttu-id="c2ac9-505">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="c2ac9-505">QUERY_STRING</span></span>
* <span data-ttu-id="c2ac9-506">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="c2ac9-506">REMOTE_ADDR</span></span>
* <span data-ttu-id="c2ac9-507">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="c2ac9-507">REMOTE_PORT</span></span>
* <span data-ttu-id="c2ac9-508">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="c2ac9-508">REQUEST_FILENAME</span></span>
* <span data-ttu-id="c2ac9-509">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="c2ac9-509">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="c2ac9-510">Můžete také získat <xref:Microsoft.Extensions.FileProviders.IFileProvider> prostřednictvím <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-510">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="c2ac9-511">Tento přístup může poskytovat větší flexibilitu pro umístění souborů pravidel přepisu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-511">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="c2ac9-512">Ujistěte se, že jsou v cestě, kterou zadáte, nasazené soubory pravidel přepisu na server.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-512">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="c2ac9-513">Pravidlo založené na metodách</span><span class="sxs-lookup"><span data-stu-id="c2ac9-513">Method-based rule</span></span>

<span data-ttu-id="c2ac9-514">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k implementaci logiky vlastního pravidla v metodě.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-514">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="c2ac9-515">`Add`zpřístupňuje <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> , který zpřístupňuje <xref:Microsoft.AspNetCore.Http.HttpContext> metodu pro použití v metodě.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-515">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="c2ac9-516">[RewriteContext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) určuje, jak se zpracovává další zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-516">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="c2ac9-517">Nastavte hodnotu na jedno z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> polí popsaných v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-517">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="c2ac9-518">Akce</span><span class="sxs-lookup"><span data-stu-id="c2ac9-518">Action</span></span>                                                           |
| ---
<span data-ttu-id="c2ac9-519">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-520">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-521">'Identity'</span></span>
- <span data-ttu-id="c2ac9-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-523">'Razor'</span></span>
- <span data-ttu-id="c2ac9-524">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-525">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-526">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-527">'Identity'</span></span>
- <span data-ttu-id="c2ac9-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-529">'Razor'</span></span>
- <span data-ttu-id="c2ac9-530">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-531">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-532">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-533">'Identity'</span></span>
- <span data-ttu-id="c2ac9-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-535">'Razor'</span></span>
- <span data-ttu-id="c2ac9-536">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-537">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-538">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-539">'Identity'</span></span>
- <span data-ttu-id="c2ac9-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-541">'Razor'</span></span>
- <span data-ttu-id="c2ac9-542">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-543">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-544">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-545">'Identity'</span></span>
- <span data-ttu-id="c2ac9-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-547">'Razor'</span></span>
- <span data-ttu-id="c2ac9-548">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-549">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-550">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-551">'Identity'</span></span>
- <span data-ttu-id="c2ac9-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-553">'Razor'</span></span>
- <span data-ttu-id="c2ac9-554">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-555">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-556">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-557">'Identity'</span></span>
- <span data-ttu-id="c2ac9-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-559">'Razor'</span></span>
- <span data-ttu-id="c2ac9-560">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-561">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-562">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-563">'Identity'</span></span>
- <span data-ttu-id="c2ac9-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-565">'Razor'</span></span>
- <span data-ttu-id="c2ac9-566">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-567">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-568">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-569">'Identity'</span></span>
- <span data-ttu-id="c2ac9-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-571">'Razor'</span></span>
- <span data-ttu-id="c2ac9-572">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-573">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-574">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-575">'Identity'</span></span>
- <span data-ttu-id="c2ac9-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-577">'Razor'</span></span>
- <span data-ttu-id="c2ac9-578">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-579">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-580">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-581">'Identity'</span></span>
- <span data-ttu-id="c2ac9-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-583">'Razor'</span></span>
- <span data-ttu-id="c2ac9-584">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-585">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-586">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-587">'Identity'</span></span>
- <span data-ttu-id="c2ac9-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-589">'Razor'</span></span>
- <span data-ttu-id="c2ac9-590">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-591">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-592">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-593">'Identity'</span></span>
- <span data-ttu-id="c2ac9-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-595">'Razor'</span></span>
- <span data-ttu-id="c2ac9-596">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-597">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-598">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-599">'Identity'</span></span>
- <span data-ttu-id="c2ac9-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-601">'Razor'</span></span>
- <span data-ttu-id="c2ac9-602">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-603">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-604">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-605">'Identity'</span></span>
- <span data-ttu-id="c2ac9-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-607">'Razor'</span></span>
- <span data-ttu-id="c2ac9-608">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-609">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-610">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-611">'Identity'</span></span>
- <span data-ttu-id="c2ac9-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-613">'Razor'</span></span>
- <span data-ttu-id="c2ac9-614">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-614">'SignalR' uid:</span></span> 

<span data-ttu-id="c2ac9-615">------------------ | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-616">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-617">'Identity'</span></span>
- <span data-ttu-id="c2ac9-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-619">'Razor'</span></span>
- <span data-ttu-id="c2ac9-620">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-621">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-622">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-623">'Identity'</span></span>
- <span data-ttu-id="c2ac9-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-625">'Razor'</span></span>
- <span data-ttu-id="c2ac9-626">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-627">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-628">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-629">'Identity'</span></span>
- <span data-ttu-id="c2ac9-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-631">'Razor'</span></span>
- <span data-ttu-id="c2ac9-632">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-633">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-634">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-635">'Identity'</span></span>
- <span data-ttu-id="c2ac9-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-637">'Razor'</span></span>
- <span data-ttu-id="c2ac9-638">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-639">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-640">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-641">'Identity'</span></span>
- <span data-ttu-id="c2ac9-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-643">'Razor'</span></span>
- <span data-ttu-id="c2ac9-644">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-645">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-646">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-647">'Identity'</span></span>
- <span data-ttu-id="c2ac9-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-649">'Razor'</span></span>
- <span data-ttu-id="c2ac9-650">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-651">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-652">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-653">'Identity'</span></span>
- <span data-ttu-id="c2ac9-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-655">'Razor'</span></span>
- <span data-ttu-id="c2ac9-656">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-657">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-658">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-659">'Identity'</span></span>
- <span data-ttu-id="c2ac9-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-661">'Razor'</span></span>
- <span data-ttu-id="c2ac9-662">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-663">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-664">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-665">'Identity'</span></span>
- <span data-ttu-id="c2ac9-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-667">'Razor'</span></span>
- <span data-ttu-id="c2ac9-668">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-669">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-670">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-671">'Identity'</span></span>
- <span data-ttu-id="c2ac9-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-673">'Razor'</span></span>
- <span data-ttu-id="c2ac9-674">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-675">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-676">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-677">'Identity'</span></span>
- <span data-ttu-id="c2ac9-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-679">'Razor'</span></span>
- <span data-ttu-id="c2ac9-680">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-681">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-682">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-683">'Identity'</span></span>
- <span data-ttu-id="c2ac9-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-685">'Razor'</span></span>
- <span data-ttu-id="c2ac9-686">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-687">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-688">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-689">'Identity'</span></span>
- <span data-ttu-id="c2ac9-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-691">'Razor'</span></span>
- <span data-ttu-id="c2ac9-692">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-693">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-694">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-695">'Identity'</span></span>
- <span data-ttu-id="c2ac9-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-697">'Razor'</span></span>
- <span data-ttu-id="c2ac9-698">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-699">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-700">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-701">'Identity'</span></span>
- <span data-ttu-id="c2ac9-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-703">'Razor'</span></span>
- <span data-ttu-id="c2ac9-704">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-705">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-706">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-707">'Identity'</span></span>
- <span data-ttu-id="c2ac9-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-709">'Razor'</span></span>
- <span data-ttu-id="c2ac9-710">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-711">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-712">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-713">'Identity'</span></span>
- <span data-ttu-id="c2ac9-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-715">'Razor'</span></span>
- <span data-ttu-id="c2ac9-716">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-717">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-718">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-719">'Identity'</span></span>
- <span data-ttu-id="c2ac9-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-721">'Razor'</span></span>
- <span data-ttu-id="c2ac9-722">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-723">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-724">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-725">'Identity'</span></span>
- <span data-ttu-id="c2ac9-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-727">'Razor'</span></span>
- <span data-ttu-id="c2ac9-728">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-729">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-730">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-731">'Identity'</span></span>
- <span data-ttu-id="c2ac9-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-733">'Razor'</span></span>
- <span data-ttu-id="c2ac9-734">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-735">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-736">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-737">'Identity'</span></span>
- <span data-ttu-id="c2ac9-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-739">'Razor'</span></span>
- <span data-ttu-id="c2ac9-740">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-741">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-742">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-743">'Identity'</span></span>
- <span data-ttu-id="c2ac9-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-745">'Razor'</span></span>
- <span data-ttu-id="c2ac9-746">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-747">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-748">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-749">'Identity'</span></span>
- <span data-ttu-id="c2ac9-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-751">'Razor'</span></span>
- <span data-ttu-id="c2ac9-752">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-753">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-754">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-755">'Identity'</span></span>
- <span data-ttu-id="c2ac9-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-757">'Razor'</span></span>
- <span data-ttu-id="c2ac9-758">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-759">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-760">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-761">'Identity'</span></span>
- <span data-ttu-id="c2ac9-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-763">'Razor'</span></span>
- <span data-ttu-id="c2ac9-764">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-765">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-766">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-767">'Identity'</span></span>
- <span data-ttu-id="c2ac9-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-769">'Razor'</span></span>
- <span data-ttu-id="c2ac9-770">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-771">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-772">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-773">'Identity'</span></span>
- <span data-ttu-id="c2ac9-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-775">'Razor'</span></span>
- <span data-ttu-id="c2ac9-776">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-777">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-778">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-779">'Identity'</span></span>
- <span data-ttu-id="c2ac9-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-781">'Razor'</span></span>
- <span data-ttu-id="c2ac9-782">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-783">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-784">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-785">'Identity'</span></span>
- <span data-ttu-id="c2ac9-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-787">'Razor'</span></span>
- <span data-ttu-id="c2ac9-788">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-789">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-790">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-791">'Identity'</span></span>
- <span data-ttu-id="c2ac9-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-793">'Razor'</span></span>
- <span data-ttu-id="c2ac9-794">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-794">'SignalR' uid:</span></span> 

<span data-ttu-id="c2ac9-795">-------------------------------- | | `RuleResult.ContinueRules`(výchozí) | Pokračovat v používání pravidel.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-795">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="c2ac9-796">| | `RuleResult.EndResponse`             | Zastavení použití pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-796">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="c2ac9-797">| | `RuleResult.SkipRemainingRules`      | Zastavení použití pravidel a odeslání kontextu do dalšího middlewaru.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-797">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="c2ac9-798">Ukázková aplikace ukazuje metodu, která přesměrovává požadavky na cesty, které končí na *. XML*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-798">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="c2ac9-799">Pokud je žádost určena pro `/file.xml` , je požadavek přesměrován na `/xmlfiles/file.xml` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-799">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="c2ac9-800">Stavový kód je nastaven na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-800">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="c2ac9-801">Když prohlížeč vytvoří nový požadavek na */xmlfiles/File.XML*, soubor middleware statických souborů zachová soubor klientovi ze složky *wwwroot/xmlfiles* .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-801">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="c2ac9-802">Pro přesměrování explicitně nastavte stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-802">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="c2ac9-803">V opačném případě se vrátí stavový kód *200-OK* a na klientovi se neobjeví přesměrování.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-803">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="c2ac9-804">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-804">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="c2ac9-805">Tento přístup může také přezapisovat požadavky.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-805">This approach can also rewrite requests.</span></span> <span data-ttu-id="c2ac9-806">Ukázková aplikace ukazuje přepis cesty k libovolné žádosti o textový soubor, aby sloužil textový soubor *. txt* ze složky *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-806">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="c2ac9-807">Middleware statických souborů zachovává soubor na základě aktualizované cesty požadavku:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-807">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="c2ac9-808">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-808">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="c2ac9-809">Pravidlo založené na IRule</span><span class="sxs-lookup"><span data-stu-id="c2ac9-809">IRule-based rule</span></span>

<span data-ttu-id="c2ac9-810">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k použití logiky pravidla ve třídě, která implementuje <xref:Microsoft.AspNetCore.Rewrite.IRule> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-810">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="c2ac9-811">`IRule`poskytuje větší flexibilitu při použití pravidla založeného na metodách.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-811">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="c2ac9-812">Vaše třída implementace může obsahovat konstruktor, který umožňuje předat parametry pro <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> metodu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-812">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="c2ac9-813">Hodnoty parametrů v ukázkové aplikaci pro `extension` a `newPath` jsou zkontrolovány, aby splňovaly několik podmínek.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-813">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="c2ac9-814">`extension`Musí obsahovat hodnotu a hodnota musí být *. png*, *. jpg*nebo *. gif*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-814">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="c2ac9-815">Pokud `newPath` není platná, <xref:System.ArgumentException> je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-815">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="c2ac9-816">Pokud je pro *image. png*vytvořen požadavek, je požadavek přesměrován na `/png-images/image.png` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-816">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="c2ac9-817">Pokud je pro *image. jpg*vytvořen požadavek, je požadavek přesměrován na `/jpg-images/image.jpg` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-817">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="c2ac9-818">Stavový kód je nastaven na *301 – trvale přesunuto*a `context.Result` je nastavené na zastavení zpracování pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-818">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="c2ac9-819">Původní požadavek:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-819">Original Request: `/image.png`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image. png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="c2ac9-821">Původní požadavek:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-821">Original Request: `/image.jpg`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image. jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="c2ac9-823">Příklady Regex</span><span class="sxs-lookup"><span data-stu-id="c2ac9-823">Regex examples</span></span>

| <span data-ttu-id="c2ac9-824">Cíl</span><span class="sxs-lookup"><span data-stu-id="c2ac9-824">Goal</span></span> | <span data-ttu-id="c2ac9-825">& řetězce regulárního výrazu</span><span class="sxs-lookup"><span data-stu-id="c2ac9-825">Regex String &</span></span><br><span data-ttu-id="c2ac9-826">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="c2ac9-826">Match Example</span></span> | <span data-ttu-id="c2ac9-827">& řetězce nahrazení</span><span class="sxs-lookup"><span data-stu-id="c2ac9-827">Replacement String &</span></span><br><span data-ttu-id="c2ac9-828">Příklad výstupu</span><span class="sxs-lookup"><span data-stu-id="c2ac9-828">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="c2ac9-829">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-830">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-831">'Identity'</span></span>
- <span data-ttu-id="c2ac9-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-833">'Razor'</span></span>
- <span data-ttu-id="c2ac9-834">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-835">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-836">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-837">'Identity'</span></span>
- <span data-ttu-id="c2ac9-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-839">'Razor'</span></span>
- <span data-ttu-id="c2ac9-840">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-840">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-841">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-842">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-842">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-843">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-843">'Identity'</span></span>
- <span data-ttu-id="c2ac9-844">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-844">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-845">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-845">'Razor'</span></span>
- <span data-ttu-id="c2ac9-846">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-846">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-847">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-848">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-848">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-849">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-849">'Identity'</span></span>
- <span data-ttu-id="c2ac9-850">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-850">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-851">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-851">'Razor'</span></span>
- <span data-ttu-id="c2ac9-852">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-852">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-853">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-854">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-854">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-855">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-855">'Identity'</span></span>
- <span data-ttu-id="c2ac9-856">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-856">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-857">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-857">'Razor'</span></span>
- <span data-ttu-id="c2ac9-858">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-858">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-859">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-860">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-860">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-861">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-861">'Identity'</span></span>
- <span data-ttu-id="c2ac9-862">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-862">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-863">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-863">'Razor'</span></span>
- <span data-ttu-id="c2ac9-864">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-864">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-865">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-866">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-866">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-867">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-867">'Identity'</span></span>
- <span data-ttu-id="c2ac9-868">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-868">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-869">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-869">'Razor'</span></span>
- <span data-ttu-id="c2ac9-870">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-870">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-871">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-872">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-872">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-873">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-873">'Identity'</span></span>
- <span data-ttu-id="c2ac9-874">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-874">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-875">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-875">'Razor'</span></span>
- <span data-ttu-id="c2ac9-876">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-876">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-877">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-878">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-878">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-879">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-879">'Identity'</span></span>
- <span data-ttu-id="c2ac9-880">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-880">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-881">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-881">'Razor'</span></span>
- <span data-ttu-id="c2ac9-882">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-882">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-883">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-884">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-884">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-885">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-885">'Identity'</span></span>
- <span data-ttu-id="c2ac9-886">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-886">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-887">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-887">'Razor'</span></span>
- <span data-ttu-id="c2ac9-888">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-888">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-889">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-890">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-890">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-891">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-891">'Identity'</span></span>
- <span data-ttu-id="c2ac9-892">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-892">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-893">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-893">'Razor'</span></span>
- <span data-ttu-id="c2ac9-894">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-894">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-895">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-896">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-896">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-897">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-897">'Identity'</span></span>
- <span data-ttu-id="c2ac9-898">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-898">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-899">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-899">'Razor'</span></span>
- <span data-ttu-id="c2ac9-900">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-900">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-901">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-902">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-902">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-903">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-903">'Identity'</span></span>
- <span data-ttu-id="c2ac9-904">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-904">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-905">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-905">'Razor'</span></span>
- <span data-ttu-id="c2ac9-906">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-906">'SignalR' uid:</span></span> 

<span data-ttu-id="c2ac9-907">---------------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-908">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-908">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-909">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-909">'Identity'</span></span>
- <span data-ttu-id="c2ac9-910">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-910">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-911">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-911">'Razor'</span></span>
- <span data-ttu-id="c2ac9-912">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-912">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-913">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-914">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-914">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-915">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-915">'Identity'</span></span>
- <span data-ttu-id="c2ac9-916">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-916">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-917">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-917">'Razor'</span></span>
- <span data-ttu-id="c2ac9-918">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-918">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-919">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-920">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-920">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-921">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-921">'Identity'</span></span>
- <span data-ttu-id="c2ac9-922">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-922">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-923">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-923">'Razor'</span></span>
- <span data-ttu-id="c2ac9-924">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-924">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-925">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-926">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-926">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-927">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-927">'Identity'</span></span>
- <span data-ttu-id="c2ac9-928">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-928">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-929">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-929">'Razor'</span></span>
- <span data-ttu-id="c2ac9-930">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-930">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-931">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-932">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-932">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-933">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-933">'Identity'</span></span>
- <span data-ttu-id="c2ac9-934">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-934">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-935">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-935">'Razor'</span></span>
- <span data-ttu-id="c2ac9-936">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-936">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-937">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-938">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-939">'Identity'</span></span>
- <span data-ttu-id="c2ac9-940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-940">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-941">'Razor'</span></span>
- <span data-ttu-id="c2ac9-942">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-943">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-944">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-945">'Identity'</span></span>
- <span data-ttu-id="c2ac9-946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-946">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-947">'Razor'</span></span>
- <span data-ttu-id="c2ac9-948">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-949">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-950">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-951">'Identity'</span></span>
- <span data-ttu-id="c2ac9-952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-952">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-953">'Razor'</span></span>
- <span data-ttu-id="c2ac9-954">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-954">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-955">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-956">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-957">'Identity'</span></span>
- <span data-ttu-id="c2ac9-958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-958">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-959">'Razor'</span></span>
- <span data-ttu-id="c2ac9-960">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-960">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-961">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-962">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-963">'Identity'</span></span>
- <span data-ttu-id="c2ac9-964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-964">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-965">'Razor'</span></span>
- <span data-ttu-id="c2ac9-966">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-967">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-968">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-969">'Identity'</span></span>
- <span data-ttu-id="c2ac9-970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-970">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-971">'Razor'</span></span>
- <span data-ttu-id="c2ac9-972">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-973">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-974">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-975">'Identity'</span></span>
- <span data-ttu-id="c2ac9-976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-976">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-977">'Razor'</span></span>
- <span data-ttu-id="c2ac9-978">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-979">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-980">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-981">'Identity'</span></span>
- <span data-ttu-id="c2ac9-982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-982">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-983">'Razor'</span></span>
- <span data-ttu-id="c2ac9-984">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-985">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-986">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-987">'Identity'</span></span>
- <span data-ttu-id="c2ac9-988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-988">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-989">'Razor'</span></span>
- <span data-ttu-id="c2ac9-990">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-990">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-991">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-992">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-992">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-993">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-993">'Identity'</span></span>
- <span data-ttu-id="c2ac9-994">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-994">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-995">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-995">'Razor'</span></span>
- <span data-ttu-id="c2ac9-996">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-996">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-997">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-998">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-998">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-999">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-999">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1000">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1000">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1001">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1001">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1002">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1002">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1003">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1004">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1004">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1005">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1005">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1006">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1006">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1007">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1007">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1008">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1008">'SignalR' uid:</span></span> 

<span data-ttu-id="c2ac9-1009">------------------- | | Přepište cestu do řetězce dotazu |`^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1009">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="c2ac9-1010">`/path?var1=abc&var2=123`| | Koncové lomítko pruhu |`(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1010">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="c2ac9-1011">`/path`| | Vynutilo koncové lomítko |`(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1011">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="c2ac9-1012">`/path/`| | Vyhněte se přepisu konkrétních požadavků | `^(.*)(?<!\.axd)$`nebo`^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1012">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="c2ac9-1013">Ano:`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1013">Yes: `/resource.htm`</span></span><br><span data-ttu-id="c2ac9-1014">Ne:`/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1014">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="c2ac9-1015">`/resource.axd`| | Změna uspořádání segmentů adresy URL |`path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1015">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="c2ac9-1016">`path/3/2/1`| | Nahradit segment adresy URL |`^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1016">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c2ac9-1017">Tento dokument zavádí přepis adres URL s pokyny k použití middlewaru přepisu adresy URL v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1017">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="c2ac9-1018">Přepsání adresy URL je to, že se jedná o úpravu adres URL žádostí na základě jednoho nebo více předdefinovaných pravidel.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1018">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="c2ac9-1019">Přepsání adresy URL vytvoří abstrakci mezi umístěními prostředků a jejich adresami tak, aby umístění a adresy nebyly pevně propojené.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1019">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="c2ac9-1020">Přepsání adresy URL je v několika scénářích užitečné pro:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1020">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="c2ac9-1021">Dočasné nebo trvalé přesunutí nebo nahrazení prostředků serveru a udržování stabilních lokátorů pro tyto prostředky.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1021">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="c2ac9-1022">Rozdělte zpracování požadavků napříč různými aplikacemi nebo napříč oblastmi jedné aplikace.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1022">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="c2ac9-1023">Umožňuje odebrat, přidat nebo změnit uspořádání segmentů adresy URL příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1023">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="c2ac9-1024">Optimalizujte veřejné adresy URL pro optimalizaci vyhledávače (SEO).</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1024">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="c2ac9-1025">Povolí použití přívětivých veřejných adres URL, které návštěvníkům pomůžou odhadnout obsah vrácený požadavkem na prostředek.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1025">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="c2ac9-1026">Přesměrovat nezabezpečené požadavky na zabezpečené koncové body.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1026">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="c2ac9-1027">Zabránit hotlinking, kde externí lokalita používá hostovaný statický prostředek na jiné lokalitě propojením prostředku s vlastním obsahem.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1027">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="c2ac9-1028">Přepsání adresy URL může snížit výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1028">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="c2ac9-1029">Pokud je to možné, omezte počet a složitost pravidel.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1029">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="c2ac9-1030">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1030">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="c2ac9-1031">Přesměrování adresy URL a přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1031">URL redirect and URL rewrite</span></span>

<span data-ttu-id="c2ac9-1032">Rozdíl mezi přesměrováním *URL* a *přepsáním adresy URL* je malý, ale má důležitý dopad na poskytování prostředků klientům.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1032">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="c2ac9-1033">Rozhraní middleware pro přepis adres URL ASP.NET Core je schopné splnit nutnost obou.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1033">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="c2ac9-1034">*Přesměrování adresy URL* zahrnuje i operaci na straně klienta, kde klient má pokyn k přístupu k prostředku na jiné adrese, než jakou požadoval klient původně.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1034">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="c2ac9-1035">To vyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1035">This requires a round trip to the server.</span></span> <span data-ttu-id="c2ac9-1036">Adresa URL pro přesměrování vracená klientovi se zobrazí v adresním řádku prohlížeče, když klient vytvoří nový požadavek na prostředek.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1036">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="c2ac9-1037">Pokud `/resource` je *přesměrován* na `/different-resource` , server odpoví, že by měl klient získat prostředek na `/different-resource` stavovém kódu, což značí, že přesměrování je buď dočasné, nebo trvalé.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1037">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Koncový bod služby WebAPI se dočasně změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="c2ac9-1043">Při přesměrování požadavků na jinou adresu URL určete, zda je přesměrování trvalé nebo dočasné, zadáním stavového kódu s odpovědí:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1043">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="c2ac9-1044">V případě, že má prostředek novou, trvalou adresu URL a chcete dát klientovi pokyn, aby všechny budoucí požadavky na prostředek používaly novou adresu URL, se použije kód *trvale přesunutý stav 301* .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1044">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="c2ac9-1045">*Klient může ukládat odpověď do mezipaměti a znovu ji použít, když se přijme stavový kód 301.*</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1045">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="c2ac9-1046">V případě, že je přesměrování dočasné nebo obecně se může změnit, je použit stavový kód *302* .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1046">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="c2ac9-1047">Stavový kód 302 indikuje klientovi, aby neukládal adresu URL a použil ho v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1047">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="c2ac9-1048">Další informace o stavových kódech najdete v [dokumentu RFC 2616: definice stavového kódu](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1048">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="c2ac9-1049">*Přepsání adresy URL* je operace na straně serveru, která poskytuje prostředek z jiné adresy prostředku, než jakou požaduje klient.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1049">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="c2ac9-1050">Přepsání adresy URL nevyžaduje zpáteční cestu k serveru.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1050">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="c2ac9-1051">Přepsaná adresa URL se nevrátí klientovi a nezobrazí se v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1051">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="c2ac9-1052">Pokud `/resource` je *přepsáno* na `/different-resource` , server *interně* načte a vrátí prostředek na `/different-resource` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1052">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="c2ac9-1053">I když klient může být schopný načíst prostředek na adrese URL přepsané adresy, klient nebude informovat o tom, že prostředek existuje na přepsané adrese URL, když odešle svůj požadavek a obdrží odpověď.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1053">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Koncový bod služby WebAPI se změnil z verze 1 (V1) na verzi 2 (v2) na serveru.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="c2ac9-1058">Ukázková aplikace pro přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1058">URL rewriting sample app</span></span>

<span data-ttu-id="c2ac9-1059">Pomocí [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)můžete prozkoumat funkce middlewaru přepsaného adresou URL.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1059">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="c2ac9-1060">Aplikace použije pravidla přesměrování a přepisu a zobrazí přesměrovanou nebo přepsanou adresu URL pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1060">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="c2ac9-1061">Kdy použít middleware přepisu adresy URL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1061">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="c2ac9-1062">Pokud nemůžete použít následující přístupy, použijte middleware přepisující adresu URL:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1062">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="c2ac9-1063">Modul pro přepsání adresy URL ve službě IIS v systému Windows Server</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1063">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="c2ac9-1064">Apache mod_rewrite modul na serveru Apache</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1064">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="c2ac9-1065">Přepsání adresy URL na Nginx</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1065">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="c2ac9-1066">Použijte taky middleware, pokud je aplikace hostována na [serveru http. sys](xref:fundamentals/servers/httpsys) (dříve nazývaném weblisten).</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1066">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="c2ac9-1067">Hlavními důvody pro použití technologie přepisování adres URL založené na serveru ve službě IIS, Apache a Nginx jsou:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1067">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="c2ac9-1068">Middleware nepodporuje všechny funkce těchto modulů.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1068">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="c2ac9-1069">Některé funkce v modulech serveru nefungují s ASP.NET Core projekty, jako jsou `IsFile` `IsDirectory` omezení a modulu IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1069">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="c2ac9-1070">V těchto scénářích místo toho použijte middleware.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1070">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="c2ac9-1071">Výkon middleware se pravděpodobně neshoduje s modulem.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1071">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="c2ac9-1072">Srovnávací testy jsou jediným způsobem, jak zjistit, který přístup snižuje výkon, který je v nejvyšší nebo v případě, že je snížený výkon.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1072">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="c2ac9-1073">Balíček</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1073">Package</span></span>

<span data-ttu-id="c2ac9-1074">Chcete-li do projektu zahrnout middleware, přidejte odkaz na balíček do souboru [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) v souboru projektu, který obsahuje balíček [Microsoft. AspNetCore. Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1074">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="c2ac9-1075">Pokud nepoužíváte `Microsoft.AspNetCore.App` Metapackage, přidejte do balíčku odkaz na projekt `Microsoft.AspNetCore.Rewrite` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1075">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="c2ac9-1076">Rozšíření a možnosti</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1076">Extension and options</span></span>

<span data-ttu-id="c2ac9-1077">Vytvořte pravidla přepsání a přesměrování adresy URL vytvořením instance třídy [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) s metodami rozšíření pro každé pravidlo přepsání.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1077">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="c2ac9-1078">Řetězení více pravidel v pořadí, ve kterém se mají zpracovat.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1078">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="c2ac9-1079">Rozhraní se `RewriteOptions` předává do middleware přepisování adresy URL při jeho přidání do kanálu požadavků pomocí <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1079">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="c2ac9-1080">Přesměrovat neexistující na webovou</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1080">Redirect non-www to www</span></span>

<span data-ttu-id="c2ac9-1081">Tři možnosti umožňují, aby aplikace přesměrovala `www` požadavky bez požadavků na `www` :</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1081">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="c2ac9-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Trvale přesměrujte požadavek na `www` subdoménu, pokud je požadavek jiný než `www` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="c2ac9-1083">Přesměruje stavový kód [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1083">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="c2ac9-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Přesměrujte požadavek na `www` subdoménu, pokud je příchozí požadavek jiný než `www` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="c2ac9-1085">Přesměruje stavový kód [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1085">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="c2ac9-1086">Přetížení umožňuje poskytnout stavový kód pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1086">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="c2ac9-1087">Použijte pole <xref:Microsoft.AspNetCore.Http.StatusCodes> třídy pro přiřazení stavového kódu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1087">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="c2ac9-1088">Přesměrování adresy URL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1088">URL redirect</span></span>

<span data-ttu-id="c2ac9-1089">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> k přesměrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1089">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="c2ac9-1090">První parametr obsahuje váš regulární výraz pro porovnání s cestou příchozí adresy URL.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1090">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="c2ac9-1091">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1091">The second parameter is the replacement string.</span></span> <span data-ttu-id="c2ac9-1092">Třetí parametr, pokud je k dispozici, určuje stavový kód.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1092">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="c2ac9-1093">Pokud nezadáte stavový kód, stavový kód se standardně *302 – Nalezeno*, což znamená, že prostředek je dočasně přesunut nebo nahrazen.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1093">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="c2ac9-1094">V prohlížeči s povolenými vývojářskými nástroji vytvořte žádost o ukázkovou aplikaci s cestou `/redirect-rule/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1094">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="c2ac9-1095">Regulární výraz odpovídá cestě požadavku na `redirect-rule/(.*)` a cesta je nahrazena řetězcem `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1095">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="c2ac9-1096">Adresa URL pro přesměrování se pošle zpátky do klienta s kódem stavu 302, který se *našel* .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1096">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="c2ac9-1097">Prohlížeč vytvoří novou žádost na adrese URL pro přesměrování, která se zobrazí v adresním řádku prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1097">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="c2ac9-1098">Vzhledem k tomu, že se žádná pravidla v ukázkové aplikaci neshodují s adresou URL pro přesměrování:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1098">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="c2ac9-1099">Druhá žádost obdrží odpověď *200-OK* od aplikace.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1099">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="c2ac9-1100">Tělo odpovědi zobrazuje adresu URL pro přesměrování.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1100">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="c2ac9-1101">Při *přesměrování*adresy URL se na Server provede cyklická výměna.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1101">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="c2ac9-1102">Buďte opatrní při vytváření pravidel přesměrování.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1102">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="c2ac9-1103">Pravidla přesměrování se vyhodnocují při každém požadavku na aplikaci, včetně po přesměrování.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1103">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="c2ac9-1104">Je snadné vytvořit *smyčku nekonečných přesměrování*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1104">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="c2ac9-1105">Původní požadavek:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1105">Original Request: `/redirect-rule/1234/5678`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="c2ac9-1107">Část výrazu obsažená v závorkách se nazývá *Skupina zachycení*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1107">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="c2ac9-1108">Tečka ( `.` ) výrazu znamená, že *odpovídá libovolnému znaku*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1108">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="c2ac9-1109">Hvězdička ( `*` ) značí, *že se předchozí znak rovná nule nebo vícekrát*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1109">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="c2ac9-1110">Proto jsou poslední dva segmenty cesty adresy URL `1234/5678` zachyceny skupinou zachycení `(.*)` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1110">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="c2ac9-1111">Jakákoli hodnota, kterou zadáte v adrese URL požadavku, potom, co `redirect-rule/` je tato jediná skupina zachycení zachycena.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1111">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="c2ac9-1112">V řetězci pro nahrazení jsou zachycené skupiny vloženy do řetězce s znakem dolaru ( `$` ) následovaným pořadovým číslem záznamu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1112">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="c2ac9-1113">První hodnota skupiny zachycení je získána s `$1` , druhým s `$2` a pokračuje v pořadí pro skupiny zachycení ve vašem regulárním výrazu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1113">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="c2ac9-1114">Regulární výraz pravidla přesměrování v ukázkové aplikaci obsahuje jenom jednu zachycenou skupinu, takže v náhradním řetězci je jenom jedna vložená skupina, která je `$1` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1114">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="c2ac9-1115">Po použití pravidla se adresa URL bude nacházet `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1115">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="c2ac9-1116">Přesměrování adresy URL na zabezpečený koncový bod</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1116">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="c2ac9-1117">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> k přesměrování požadavků HTTP na stejného hostitele a cestu pomocí protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1117">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="c2ac9-1118">Pokud kód stavu není zadaný, použije se ve výchozím nastavení middleware *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1118">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="c2ac9-1119">Pokud port není dodán:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1119">If the port isn't supplied:</span></span>

* <span data-ttu-id="c2ac9-1120">Ve výchozím nastavení se používá middleware `null` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1120">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="c2ac9-1121">Schéma se změní na `https` (protokol HTTPS) a klient přistupuje k prostředku na portu 443.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1121">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="c2ac9-1122">Následující příklad ukazuje, jak nastavit stavový kód na *301 – trvale přesunuto* a změnit port na 5001.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1122">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="c2ac9-1123">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> k přesměrování nezabezpečených požadavků na stejného hostitele a cestu s protokolem Secure https na portu 443.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1123">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="c2ac9-1124">Middleware nastaví stavový kód na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1124">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="c2ac9-1125">Při přesměrování na zabezpečený koncový bod bez požadavku na další pravidla přesměrování doporučujeme použít middleware pro přesměrování protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1125">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="c2ac9-1126">Další informace najdete v tématu věnovaném [vykonání protokolu HTTPS](xref:security/enforcing-ssl#require-https) .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1126">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="c2ac9-1127">Ukázková aplikace dokáže demonstrovat, jak používat `AddRedirectToHttps` nebo `AddRedirectToHttpsPermanent` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1127">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="c2ac9-1128">Přidejte metodu rozšíření do `RewriteOptions` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1128">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="c2ac9-1129">Vytvořte nezabezpečenou žádost o aplikaci na libovolné adrese URL.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1129">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="c2ac9-1130">Zrušte upozornění zabezpečení prohlížeče, že certifikát podepsaný svým držitelem je nedůvěryhodný, nebo vytvořte výjimku pro důvěřování certifikátu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1130">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="c2ac9-1131">Původní požadavek s použitím `AddRedirectToHttps(301, 5001)` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1131">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="c2ac9-1133">Původní požadavek s použitím `AddRedirectToHttpsPermanent` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1133">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="c2ac9-1135">Přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1135">URL rewrite</span></span>

<span data-ttu-id="c2ac9-1136">Slouží <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> k vytvoření pravidla pro přepis adres URL.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1136">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="c2ac9-1137">První parametr obsahuje regulární výraz pro porovnání na cestě příchozích adres URL.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1137">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="c2ac9-1138">Druhým parametrem je náhradní řetězec.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1138">The second parameter is the replacement string.</span></span> <span data-ttu-id="c2ac9-1139">Třetí parametr, `skipRemainingRules: {true|false}` označuje middleware bez ohledu na to, zda má přeskočit další pravidla přepsání, pokud je použito aktuální pravidlo.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1139">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="c2ac9-1140">Původní požadavek:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1140">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="c2ac9-1142">Kosočtverce ( `^` ) na začátku výrazu znamená, že shoda začíná na začátku cesty URL.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1142">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="c2ac9-1143">V předchozím příkladu s pravidlem přesměrování `redirect-rule/(.*)` není `^` na začátku regulárního výrazu k dispozici žádná kosočtverce ().</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1143">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="c2ac9-1144">Proto jakékoli znaky mohou předcházet `redirect-rule/` v cestě k úspěšné shodě.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1144">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="c2ac9-1145">Cesta</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1145">Path</span></span>                               | <span data-ttu-id="c2ac9-1146">Shoda</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1146">Match</span></span> |
| ---
<span data-ttu-id="c2ac9-1147">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1148">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1149">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1150">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1151">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1152">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1153">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1154">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1155">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1156">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1157">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1158">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1159">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1160">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1161">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1162">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1163">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1164">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1165">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1166">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1167">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1168">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1169">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1170">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1171">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1172">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1173">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1174">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1175">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1176">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1177">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1178">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1179">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1180">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1181">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1182">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1183">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1184">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1185">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1186">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1187">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1188">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1189">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1190">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1191">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1192">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1193">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1194">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1195">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1196">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1197">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1198">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1199">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1200">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1201">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1202">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1203">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1204">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1205">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1206">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1207">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1208">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1209">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1210">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1211">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1212">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1213">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1214">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1215">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1216">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1217">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1218">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1219">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1220">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1221">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1222">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1223">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1224">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1225">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1226">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1227">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1228">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1229">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1230">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1231">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1232">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1233">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1234">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1235">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1236">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1236">'SignalR' uid:</span></span> 

<span data-ttu-id="c2ac9-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Ano | | `/my-cool-redirect-rule/1234/5678` | Ano | | `/anotherredirect-rule/1234/5678`  | Ano |</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="c2ac9-1238">Pravidlo přepsaného zápisu `^rewrite-rule/(\d+)/(\d+)` odpovídá pouze cestám, pokud začínají na `rewrite-rule/` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1238">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="c2ac9-1239">V následující tabulce si všimněte rozdílu v porovnání.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1239">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="c2ac9-1240">Cesta</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1240">Path</span></span>                              | <span data-ttu-id="c2ac9-1241">Shoda</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1241">Match</span></span> |
| ---
<span data-ttu-id="c2ac9-1242">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1243">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1244">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1245">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1246">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1247">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1248">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1249">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1250">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1251">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1252">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1253">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1254">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1255">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1256">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1257">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1258">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1259">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1260">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1261">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1262">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1263">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1264">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1265">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1266">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1267">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1268">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1269">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1270">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1271">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1272">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1273">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1274">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1275">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1276">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1277">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1278">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1279">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1280">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1281">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1282">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1283">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1284">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1285">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1286">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1287">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1288">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1289">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1290">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1291">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1292">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1293">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1294">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1295">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1296">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1297">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1298">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1299">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1300">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1301">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1302">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1303">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1304">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1305">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1306">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1307">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1308">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1309">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1310">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1311">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1312">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1313">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1314">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1315">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1316">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1317">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1318">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1319">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1320">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1321">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1322">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1323">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1324">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1325">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1325">'SignalR' uid:</span></span> 

<span data-ttu-id="c2ac9-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Ano | | `/my-cool-rewrite-rule/1234/5678` | Žádné | | `/anotherrewrite-rule/1234/5678`  | Žádné |</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="c2ac9-1327">Po `^rewrite-rule/` části výrazu jsou k dispozici dvě skupiny zachycení, `(\d+)/(\d+)` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1327">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="c2ac9-1328">`\d`Značí *shodu s číslicí (číslem)*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1328">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="c2ac9-1329">Znaménko plus ( `+` ) znamená, že se *shoduje s jedním nebo více předcházejícím znakem*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1329">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="c2ac9-1330">Proto musí adresa URL obsahovat číslo následované lomítkem, za kterým následuje jiné číslo.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1330">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="c2ac9-1331">Tyto skupiny zachycení jsou vloženy do přepsané adresy URL jako `$1` a `$2` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1331">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="c2ac9-1332">Náhradní řetězec pravidla přepsaní umístí zachycené skupiny do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1332">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="c2ac9-1333">Požadovaná cesta k `/rewrite-rule/1234/5678` je přepsána za účelem získání prostředku v `/rewritten?var1=1234&var2=5678` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1333">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="c2ac9-1334">Pokud se v původním požadavku nachází řetězec dotazu, bude při přepisování adresy URL zachován.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1334">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="c2ac9-1335">K získání prostředku neexistuje žádná Přenosová cesta k serveru.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1335">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="c2ac9-1336">Pokud prostředek existuje, načte se a vrátí do klienta s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1336">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="c2ac9-1337">Vzhledem k tomu, že klient není přesměrován, adresa URL v adresním řádku prohlížeče se nezmění.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1337">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="c2ac9-1338">Klienti nerozpoznají, že na serveru došlo k operaci přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1338">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="c2ac9-1339">`skipRemainingRules: true`Kdykoli je to možné, protože vyhovující pravidla jsou výpočetně náročná a zvyšují dobu odezvy aplikace.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1339">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="c2ac9-1340">Pro nejrychlejší aplikační odpověď:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1340">For the fastest app response:</span></span>
>
> * <span data-ttu-id="c2ac9-1341">Seřazení pravidel přepisu z nejčastěji spárovaného pravidla na nejméně často spárované pravidlo.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1341">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="c2ac9-1342">Pokud dojde ke shodě a není vyžadováno žádné další zpracování pravidla, přeskočte zpracování zbývajících pravidel.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1342">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="c2ac9-1343">Mod_rewrite Apache</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1343">Apache mod_rewrite</span></span>

<span data-ttu-id="c2ac9-1344">Použijte pravidla mod_rewrite Apache s <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1344">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="c2ac9-1345">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1345">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="c2ac9-1346">Další informace a příklady pravidel mod_rewrite najdete v tématu [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1346">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="c2ac9-1347"><xref:System.IO.StreamReader>Používá se ke čtení pravidel ze souboru pravidel *ApacheModRewrite. txt* :</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1347">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="c2ac9-1348">Ukázková aplikace přesměrovává požadavky od `/apache-mod-rules-redirect/(.\*)` do `/redirected?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1348">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="c2ac9-1349">Stavový kód odpovědi je *302 – Nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1349">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="c2ac9-1350">Původní požadavek:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1350">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="c2ac9-1352">Middleware podporuje následující proměnné serveru Apache mod_rewrite:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1352">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="c2ac9-1353">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1353">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="c2ac9-1354">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1354">HTTP_ACCEPT</span></span>
* <span data-ttu-id="c2ac9-1355">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1355">HTTP_CONNECTION</span></span>
* <span data-ttu-id="c2ac9-1356">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1356">HTTP_COOKIE</span></span>
* <span data-ttu-id="c2ac9-1357">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1357">HTTP_FORWARDED</span></span>
* <span data-ttu-id="c2ac9-1358">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1358">HTTP_HOST</span></span>
* <span data-ttu-id="c2ac9-1359">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1359">HTTP_REFERER</span></span>
* <span data-ttu-id="c2ac9-1360">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1360">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="c2ac9-1361">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1361">HTTPS</span></span>
* <span data-ttu-id="c2ac9-1362">PROTOKOLŮ</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1362">IPV6</span></span>
* <span data-ttu-id="c2ac9-1363">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1363">QUERY_STRING</span></span>
* <span data-ttu-id="c2ac9-1364">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1364">REMOTE_ADDR</span></span>
* <span data-ttu-id="c2ac9-1365">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1365">REMOTE_PORT</span></span>
* <span data-ttu-id="c2ac9-1366">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1366">REQUEST_FILENAME</span></span>
* <span data-ttu-id="c2ac9-1367">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1367">REQUEST_METHOD</span></span>
* <span data-ttu-id="c2ac9-1368">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1368">REQUEST_SCHEME</span></span>
* <span data-ttu-id="c2ac9-1369">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1369">REQUEST_URI</span></span>
* <span data-ttu-id="c2ac9-1370">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1370">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="c2ac9-1371">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1371">SERVER_ADDR</span></span>
* <span data-ttu-id="c2ac9-1372">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1372">SERVER_PORT</span></span>
* <span data-ttu-id="c2ac9-1373">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1373">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="c2ac9-1374">ČAS</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1374">TIME</span></span>
* <span data-ttu-id="c2ac9-1375">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1375">TIME_DAY</span></span>
* <span data-ttu-id="c2ac9-1376">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1376">TIME_HOUR</span></span>
* <span data-ttu-id="c2ac9-1377">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1377">TIME_MIN</span></span>
* <span data-ttu-id="c2ac9-1378">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1378">TIME_MON</span></span>
* <span data-ttu-id="c2ac9-1379">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1379">TIME_SEC</span></span>
* <span data-ttu-id="c2ac9-1380">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1380">TIME_WDAY</span></span>
* <span data-ttu-id="c2ac9-1381">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1381">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="c2ac9-1382">Pravidla pro přepsání adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1382">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="c2ac9-1383">Pokud chcete použít stejnou sadu pravidel, která se vztahuje na modul IIS URL Rewrite, použijte <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1383">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="c2ac9-1384">Ujistěte se, že je soubor pravidel nasazený spolu s aplikací.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1384">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="c2ac9-1385">Nesměrujte middleware na použití souboru *Web. config* aplikace při spuštění v systému Windows Server IIS.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1385">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="c2ac9-1386">V případě služby IIS by tato pravidla měla být uložena mimo soubor *Web. config* aplikace, aby nedocházelo ke konfliktům s modulem pro přepis služby IIS.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1386">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="c2ac9-1387">Další informace a příklady pravidel pro přepis adres URL služby IIS najdete v tématu použití odkazu na modul pro [přepis adres url 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) a [odkaz na konfiguraci modulu přepisu adresy](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)URL.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1387">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="c2ac9-1388"><xref:System.IO.StreamReader>Používá se ke čtení pravidel ze souboru pravidel *IISUrlRewrite. XML* :</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1388">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="c2ac9-1389">Ukázková aplikace přepíše požadavky od `/iis-rules-rewrite/(.*)` do `/rewritten?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1389">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="c2ac9-1390">Odpověď se pošle klientovi s kódem stavu *200-OK* .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1390">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="c2ac9-1391">Původní požadavek:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1391">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Okno prohlížeče se Vývojářské nástroje sledování žádosti a odpovědi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="c2ac9-1393">Pokud máte aktivní modul pro přepis služby IIS s nakonfigurovanými pravidly na úrovni serveru, který by ovlivnil vaši aplikaci nežádoucím způsobem, můžete pro aplikaci zakázat modul IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1393">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="c2ac9-1394">Další informace najdete v tématu [zakázání modulů IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1394">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="c2ac9-1395">Nepodporované funkce</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1395">Unsupported features</span></span>

<span data-ttu-id="c2ac9-1396">Middleware vydaná ASP.NET Core 2. x nepodporuje následující funkce modulu pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1396">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="c2ac9-1397">Pravidla pro odchozí provoz</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1397">Outbound Rules</span></span>
* <span data-ttu-id="c2ac9-1398">Vlastní proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1398">Custom Server Variables</span></span>
* <span data-ttu-id="c2ac9-1399">Zástupné znaky</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1399">Wildcards</span></span>
* <span data-ttu-id="c2ac9-1400">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1400">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="c2ac9-1401">Podporované proměnné serveru</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1401">Supported server variables</span></span>

<span data-ttu-id="c2ac9-1402">Middleware podporuje následující proměnné serveru pro přepis adres URL služby IIS:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1402">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="c2ac9-1403">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1403">CONTENT_LENGTH</span></span>
* <span data-ttu-id="c2ac9-1404">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1404">CONTENT_TYPE</span></span>
* <span data-ttu-id="c2ac9-1405">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1405">HTTP_ACCEPT</span></span>
* <span data-ttu-id="c2ac9-1406">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1406">HTTP_CONNECTION</span></span>
* <span data-ttu-id="c2ac9-1407">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1407">HTTP_COOKIE</span></span>
* <span data-ttu-id="c2ac9-1408">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1408">HTTP_HOST</span></span>
* <span data-ttu-id="c2ac9-1409">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1409">HTTP_REFERER</span></span>
* <span data-ttu-id="c2ac9-1410">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1410">HTTP_URL</span></span>
* <span data-ttu-id="c2ac9-1411">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1411">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="c2ac9-1412">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1412">HTTPS</span></span>
* <span data-ttu-id="c2ac9-1413">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1413">LOCAL_ADDR</span></span>
* <span data-ttu-id="c2ac9-1414">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1414">QUERY_STRING</span></span>
* <span data-ttu-id="c2ac9-1415">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1415">REMOTE_ADDR</span></span>
* <span data-ttu-id="c2ac9-1416">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1416">REMOTE_PORT</span></span>
* <span data-ttu-id="c2ac9-1417">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1417">REQUEST_FILENAME</span></span>
* <span data-ttu-id="c2ac9-1418">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1418">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="c2ac9-1419">Můžete také získat <xref:Microsoft.Extensions.FileProviders.IFileProvider> prostřednictvím <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1419">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="c2ac9-1420">Tento přístup může poskytovat větší flexibilitu pro umístění souborů pravidel přepisu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1420">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="c2ac9-1421">Ujistěte se, že jsou v cestě, kterou zadáte, nasazené soubory pravidel přepisu na server.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1421">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="c2ac9-1422">Pravidlo založené na metodách</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1422">Method-based rule</span></span>

<span data-ttu-id="c2ac9-1423">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k implementaci logiky vlastního pravidla v metodě.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1423">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="c2ac9-1424">`Add`zpřístupňuje <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> , který zpřístupňuje <xref:Microsoft.AspNetCore.Http.HttpContext> metodu pro použití v metodě.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1424">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="c2ac9-1425">[RewriteContext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) určuje, jak se zpracovává další zpracování kanálu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1425">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="c2ac9-1426">Nastavte hodnotu na jedno z <xref:Microsoft.AspNetCore.Rewrite.RuleResult> polí popsaných v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1426">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="c2ac9-1427">Akce</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1427">Action</span></span>                                                           |
| ---
<span data-ttu-id="c2ac9-1428">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1429">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1430">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1431">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1432">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1433">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1434">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1435">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1436">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1437">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1438">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1439">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1439">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1440">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1441">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1442">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1443">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1444">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1445">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1446">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1447">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1448">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1449">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1450">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1451">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1451">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1452">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1453">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1453">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1454">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1454">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1455">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1455">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1456">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1456">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1457">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1457">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1458">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1459">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1459">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1460">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1460">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1461">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1461">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1462">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1462">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1463">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1463">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1464">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1465">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1466">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1467">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1468">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1469">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1470">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1471">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1472">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1473">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1474">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1475">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1476">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1477">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1478">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1479">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1480">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1481">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1481">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1482">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1483">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1484">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1485">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1486">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1487">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1488">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1489">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1490">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1491">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1492">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1493">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1494">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1495">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1496">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1497">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1498">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1499">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1499">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1500">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1501">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1501">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1502">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1502">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1503">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1503">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1504">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1504">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1505">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1505">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1506">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1507">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1507">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1508">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1508">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1509">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1509">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1510">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1510">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1511">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1511">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1512">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1513">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1513">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1514">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1514">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1515">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1515">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1516">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1516">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1517">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1517">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1518">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1519">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1520">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1522">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1523">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1523">'SignalR' uid:</span></span> 

<span data-ttu-id="c2ac9-1524">------------------ | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1525">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1526">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1528">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1529">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1530">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1531">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1532">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1534">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1535">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1536">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1537">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1538">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1540">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1541">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1542">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1543">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1544">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1546">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1547">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1548">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1549">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1550">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1552">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1553">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1553">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1554">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1555">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1556">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1558">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1559">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1560">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1561">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1562">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1564">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1565">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1565">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1566">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1567">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1567">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1568">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1568">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1569">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1569">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1570">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1570">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1571">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1571">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1572">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1573">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1574">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1575">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1576">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1577">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1578">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1579">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1579">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1580">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1580">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1581">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1581">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1582">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1582">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1583">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1583">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1584">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1585">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1585">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1586">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1586">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1587">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1587">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1588">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1588">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1589">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1589">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1590">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1591">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1591">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1592">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1592">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1593">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1593">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1594">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1594">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1595">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1595">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1596">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1597">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1597">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1598">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1598">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1599">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1599">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1600">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1600">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1601">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1601">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1602">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1603">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1603">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1604">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1604">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1605">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1605">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1606">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1606">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1607">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1607">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1608">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1609">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1609">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1610">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1610">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1611">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1611">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1612">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1612">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1613">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1613">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1614">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1615">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1616">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1617">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1618">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1619">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1620">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1621">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1621">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1622">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1622">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1623">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1623">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1624">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1624">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1625">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1625">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1626">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1627">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1627">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1628">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1628">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1629">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1629">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1630">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1630">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1631">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1631">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1632">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1633">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1633">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1634">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1634">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1635">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1635">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1636">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1636">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1637">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1637">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1638">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1639">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1639">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1640">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1640">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1641">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1641">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1642">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1642">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1643">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1643">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1644">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1645">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1645">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1646">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1646">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1647">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1647">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1648">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1648">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1649">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1649">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1650">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1651">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1651">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1652">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1652">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1653">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1653">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1654">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1654">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1655">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1655">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1656">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1657">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1658">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1659">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1660">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1661">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1662">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1663">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1663">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1664">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1664">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1665">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1665">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1666">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1666">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1667">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1667">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1668">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1669">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1669">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1670">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1670">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1671">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1671">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1672">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1672">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1673">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1673">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1674">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1675">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1675">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1676">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1676">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1677">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1677">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1678">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1678">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1679">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1679">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1680">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1681">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1681">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1682">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1682">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1683">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1683">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1684">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1684">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1685">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1685">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1686">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1687">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1687">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1688">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1688">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1689">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1689">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1690">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1690">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1691">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1691">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1692">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1693">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1693">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1694">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1694">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1695">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1695">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1696">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1696">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1697">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1697">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1698">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1699">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1699">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1700">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1700">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1701">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1701">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1702">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1702">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1703">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1703">'SignalR' uid:</span></span> 

<span data-ttu-id="c2ac9-1704">-------------------------------- | | `RuleResult.ContinueRules`(výchozí) | Pokračovat v používání pravidel.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1704">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="c2ac9-1705">| | `RuleResult.EndResponse`             | Zastavení použití pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1705">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="c2ac9-1706">| | `RuleResult.SkipRemainingRules`      | Zastavení použití pravidel a odeslání kontextu do dalšího middlewaru.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1706">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="c2ac9-1707">Ukázková aplikace ukazuje metodu, která přesměrovává požadavky na cesty, které končí na *. XML*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1707">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="c2ac9-1708">Pokud je žádost určena pro `/file.xml` , je požadavek přesměrován na `/xmlfiles/file.xml` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1708">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="c2ac9-1709">Stavový kód je nastaven na *301 – trvale přesunuto*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1709">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="c2ac9-1710">Když prohlížeč vytvoří nový požadavek na */xmlfiles/File.XML*, soubor middleware statických souborů zachová soubor klientovi ze složky *wwwroot/xmlfiles* .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1710">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="c2ac9-1711">Pro přesměrování explicitně nastavte stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1711">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="c2ac9-1712">V opačném případě se vrátí stavový kód *200-OK* a na klientovi se neobjeví přesměrování.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1712">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="c2ac9-1713">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1713">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="c2ac9-1714">Tento přístup může také přezapisovat požadavky.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1714">This approach can also rewrite requests.</span></span> <span data-ttu-id="c2ac9-1715">Ukázková aplikace ukazuje přepis cesty k libovolné žádosti o textový soubor, aby sloužil textový soubor *. txt* ze složky *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1715">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="c2ac9-1716">Middleware statických souborů zachovává soubor na základě aktualizované cesty požadavku:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1716">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="c2ac9-1717">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1717">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="c2ac9-1718">Pravidlo založené na IRule</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1718">IRule-based rule</span></span>

<span data-ttu-id="c2ac9-1719">Použijte <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> k použití logiky pravidla ve třídě, která implementuje <xref:Microsoft.AspNetCore.Rewrite.IRule> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1719">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="c2ac9-1720">`IRule`poskytuje větší flexibilitu při použití pravidla založeného na metodách.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1720">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="c2ac9-1721">Vaše třída implementace může obsahovat konstruktor, který umožňuje předat parametry pro <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> metodu.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1721">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="c2ac9-1722">Hodnoty parametrů v ukázkové aplikaci pro `extension` a `newPath` jsou zkontrolovány, aby splňovaly několik podmínek.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1722">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="c2ac9-1723">`extension`Musí obsahovat hodnotu a hodnota musí být *. png*, *. jpg*nebo *. gif*.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1723">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="c2ac9-1724">Pokud `newPath` není platná, <xref:System.ArgumentException> je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1724">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="c2ac9-1725">Pokud je pro *image. png*vytvořen požadavek, je požadavek přesměrován na `/png-images/image.png` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1725">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="c2ac9-1726">Pokud je pro *image. jpg*vytvořen požadavek, je požadavek přesměrován na `/jpg-images/image.jpg` .</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1726">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="c2ac9-1727">Stavový kód je nastaven na *301 – trvale přesunuto*a `context.Result` je nastavené na zastavení zpracování pravidel a odeslání odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1727">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="c2ac9-1728">Původní požadavek:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1728">Original Request: `/image.png`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image. png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="c2ac9-1730">Původní požadavek:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1730">Original Request: `/image.jpg`</span></span>

![Okno prohlížeče s Vývojářské nástroje sledování požadavků a odpovědí pro image. jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="c2ac9-1732">Příklady Regex</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1732">Regex examples</span></span>

| <span data-ttu-id="c2ac9-1733">Cíl</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1733">Goal</span></span> | <span data-ttu-id="c2ac9-1734">& řetězce regulárního výrazu</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1734">Regex String &</span></span><br><span data-ttu-id="c2ac9-1735">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1735">Match Example</span></span> | <span data-ttu-id="c2ac9-1736">& řetězce nahrazení</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1736">Replacement String &</span></span><br><span data-ttu-id="c2ac9-1737">Příklad výstupu</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1737">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="c2ac9-1738">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1739">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1740">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1741">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1742">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1743">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1744">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1745">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1746">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1747">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1748">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1749">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1750">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1751">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1752">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1753">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1754">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1755">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1755">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1756">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1757">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1758">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1759">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1760">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1761">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1762">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1763">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1764">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1765">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1766">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1767">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1768">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1769">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1770">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1771">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1772">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1773">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1774">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1775">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1776">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1777">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1778">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1779">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1780">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1781">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1782">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1783">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1784">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1785">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1786">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1787">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1788">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1789">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1790">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1791">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1792">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1793">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1794">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1795">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1796">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1797">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1798">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1799">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1800">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1801">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1802">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1803">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1804">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1805">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1806">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1807">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1808">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1809">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1810">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1811">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1812">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1813">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1814">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1815">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1815">'SignalR' uid:</span></span> 

<span data-ttu-id="c2ac9-1816">---------------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1817">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1818">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1819">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1820">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1821">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1821">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1822">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1823">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1823">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1824">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1824">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1825">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1825">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1826">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1826">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1827">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1827">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1828">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1829">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1829">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1830">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1830">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1831">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1831">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1832">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1832">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1833">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1833">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1834">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1835">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1835">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1836">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1836">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1837">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1837">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1838">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1838">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1839">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1839">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1840">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1841">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1841">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1842">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1842">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1843">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1843">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1844">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1844">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1845">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1845">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1846">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1847">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1847">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1848">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1848">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1849">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1849">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1850">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1850">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1851">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1851">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1852">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1853">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1853">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1854">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1854">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1855">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1855">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1856">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1856">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1857">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1857">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1858">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1859">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1859">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1860">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1860">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1861">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1861">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1862">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1862">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1863">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1863">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1864">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1865">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1865">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1866">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1866">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1867">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1867">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1868">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1868">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1869">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1869">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1870">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1871">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1871">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1872">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1872">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1873">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1873">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1874">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1874">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1875">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1875">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1876">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1877">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1877">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1878">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1878">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1879">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1879">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1880">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1880">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1881">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1881">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1882">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1883">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1883">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1884">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1884">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1885">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1885">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1886">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1886">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1887">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1887">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1888">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1889">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1889">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1890">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1890">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1891">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1891">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1892">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1892">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1893">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1893">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1894">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1895">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1895">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1896">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1896">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1897">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1897">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1898">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1898">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1899">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1899">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1900">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1901">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1901">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1902">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1902">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1903">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1903">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1904">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1904">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1905">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1905">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1906">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1907">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1907">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1908">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1908">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1909">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1909">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1910">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1910">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1911">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1911">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c2ac9-1912">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c2ac9-1913">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1913">'Blazor'</span></span>
- <span data-ttu-id="c2ac9-1914">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1914">'Identity'</span></span>
- <span data-ttu-id="c2ac9-1915">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1915">'Let's Encrypt'</span></span>
- <span data-ttu-id="c2ac9-1916">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1916">'Razor'</span></span>
- <span data-ttu-id="c2ac9-1917">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1917">'SignalR' uid:</span></span> 

<span data-ttu-id="c2ac9-1918">------------------- | | Přepište cestu do řetězce dotazu |`^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1918">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="c2ac9-1919">`/path?var1=abc&var2=123`| | Koncové lomítko pruhu |`(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1919">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="c2ac9-1920">`/path`| | Vynutilo koncové lomítko |`(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1920">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="c2ac9-1921">`/path/`| | Vyhněte se přepisu konkrétních požadavků | `^(.*)(?<!\.axd)$`nebo`^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1921">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="c2ac9-1922">Ano:`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1922">Yes: `/resource.htm`</span></span><br><span data-ttu-id="c2ac9-1923">Ne:`/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1923">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="c2ac9-1924">`/resource.axd`| | Změna uspořádání segmentů adresy URL |`path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1924">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="c2ac9-1925">`path/3/2/1`| | Nahradit segment adresy URL |`^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1925">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c2ac9-1926">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1926">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="c2ac9-1927">Regulární výrazy v rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1927">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="c2ac9-1928">Jazyk regulárních výrazů – stručná referenční dokumentace</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1928">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="c2ac9-1929">Mod_rewrite Apache</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1929">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="c2ac9-1930">Použití modulu URL pro přepis 2,0 (pro IIS)</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1930">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="c2ac9-1931">Odkaz na konfiguraci modulu pro přepis adresy URL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1931">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="c2ac9-1932">Fórum modulu pro přepsání adresy URL služby IIS</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1932">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="c2ac9-1933">Zachování jednoduché struktury URL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1933">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="c2ac9-1934">10 tipů a triky pro přepis adres URL</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1934">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="c2ac9-1935">Na lomítko nebo ne na lomítko</span><span class="sxs-lookup"><span data-stu-id="c2ac9-1935">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
