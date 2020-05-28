---
<span data-ttu-id="7eb91-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-102">'Blazor'</span></span>
- <span data-ttu-id="7eb91-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-103">'Identity'</span></span>
- <span data-ttu-id="7eb91-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-105">'Razor'</span></span>
- <span data-ttu-id="7eb91-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-106">'SignalR' uid:</span></span> 

---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="7eb91-107">Ukládání odpovědí do mezipaměti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7eb91-107">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="7eb91-108">[Jan Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7eb91-108">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7eb91-109">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7eb91-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7eb91-110">Ukládání odpovědí do mezipaměti snižuje počet požadavků, které klient nebo proxy vytvoří na webový server.</span><span class="sxs-lookup"><span data-stu-id="7eb91-110">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="7eb91-111">Ukládání odpovědí do mezipaměti také snižuje množství práce, které webový Server provede k vygenerování odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7eb91-111">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="7eb91-112">Ukládání odpovědí do mezipaměti je řízeno hlavičkami, které určují, jak má klient, proxy a middleware ukládat odpovědi do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-112">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="7eb91-113">[Atribut ResponseCache](#responsecache-attribute) se účastní nastavení hlaviček ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-113">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers.</span></span> <span data-ttu-id="7eb91-114">Klienti a zprostředkující proxy servery by měly dodržovat hlavičky pro ukládání odpovědí do mezipaměti v rámci [specifikace ukládání do mezipaměti HTTP 1,1](https://tools.ietf.org/html/rfc7234).</span><span class="sxs-lookup"><span data-stu-id="7eb91-114">Clients and intermediate proxies should honor the headers for caching responses under the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span>

<span data-ttu-id="7eb91-115">Pro ukládání do mezipaměti na straně serveru, které následuje za specifikací mezipaměti HTTP 1,1, použijte [middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="7eb91-115">For server-side caching that follows the HTTP 1.1 Caching specification, use [Response Caching Middleware](xref:performance/caching/middleware).</span></span> <span data-ttu-id="7eb91-116">Middleware může pomocí <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> vlastností ovlivnit chování ukládání do mezipaměti na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="7eb91-116">The middleware can use the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="7eb91-117">Ukládání odpovědí na základě protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="7eb91-117">HTTP-based response caching</span></span>

<span data-ttu-id="7eb91-118">[Specifikace ukládání do mezipaměti HTTP 1,1](https://tools.ietf.org/html/rfc7234) popisuje, jak se mají chovat internetové mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-118">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="7eb91-119">Primární hlavičkou HTTP použitou pro ukládání do mezipaměti je [řízení mezipaměti](https://tools.ietf.org/html/rfc7234#section-5.2), které se používá k určení *direktiv*mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-119">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="7eb91-120">Direktivy řídí chování ukládání do mezipaměti jako požadavky od klientů po servery a jako reakce způsobují od serverů zpátky klientům.</span><span class="sxs-lookup"><span data-stu-id="7eb91-120">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="7eb91-121">Žádosti a odpovědi se pohybují přes proxy servery a proxy servery musí splňovat i specifikace mezipaměti HTTP 1,1.</span><span class="sxs-lookup"><span data-stu-id="7eb91-121">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="7eb91-122">`Cache-Control`V následující tabulce jsou uvedeny společné direktivy.</span><span class="sxs-lookup"><span data-stu-id="7eb91-122">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="7eb91-123">Směrnici</span><span class="sxs-lookup"><span data-stu-id="7eb91-123">Directive</span></span>                                                       | <span data-ttu-id="7eb91-124">Akce</span><span class="sxs-lookup"><span data-stu-id="7eb91-124">Action</span></span> |
| ---
<span data-ttu-id="7eb91-125">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-126">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-126">'Blazor'</span></span>
- <span data-ttu-id="7eb91-127">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-127">'Identity'</span></span>
- <span data-ttu-id="7eb91-128">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-128">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-129">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-129">'Razor'</span></span>
- <span data-ttu-id="7eb91-130">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-130">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-131">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-132">'Blazor'</span></span>
- <span data-ttu-id="7eb91-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-133">'Identity'</span></span>
- <span data-ttu-id="7eb91-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-135">'Razor'</span></span>
- <span data-ttu-id="7eb91-136">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-137">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-138">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-138">'Blazor'</span></span>
- <span data-ttu-id="7eb91-139">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-139">'Identity'</span></span>
- <span data-ttu-id="7eb91-140">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-140">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-141">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-141">'Razor'</span></span>
- <span data-ttu-id="7eb91-142">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-142">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-143">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-144">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-144">'Blazor'</span></span>
- <span data-ttu-id="7eb91-145">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-145">'Identity'</span></span>
- <span data-ttu-id="7eb91-146">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-146">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-147">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-147">'Razor'</span></span>
- <span data-ttu-id="7eb91-148">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-148">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-149">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-150">'Blazor'</span></span>
- <span data-ttu-id="7eb91-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-151">'Identity'</span></span>
- <span data-ttu-id="7eb91-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-153">'Razor'</span></span>
- <span data-ttu-id="7eb91-154">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-155">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-156">'Blazor'</span></span>
- <span data-ttu-id="7eb91-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-157">'Identity'</span></span>
- <span data-ttu-id="7eb91-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-159">'Razor'</span></span>
- <span data-ttu-id="7eb91-160">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-161">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-162">'Blazor'</span></span>
- <span data-ttu-id="7eb91-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-163">'Identity'</span></span>
- <span data-ttu-id="7eb91-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-165">'Razor'</span></span>
- <span data-ttu-id="7eb91-166">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-166">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-167">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-168">'Blazor'</span></span>
- <span data-ttu-id="7eb91-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-169">'Identity'</span></span>
- <span data-ttu-id="7eb91-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-171">'Razor'</span></span>
- <span data-ttu-id="7eb91-172">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-173">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-174">'Blazor'</span></span>
- <span data-ttu-id="7eb91-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-175">'Identity'</span></span>
- <span data-ttu-id="7eb91-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-177">'Razor'</span></span>
- <span data-ttu-id="7eb91-178">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-179">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-180">'Blazor'</span></span>
- <span data-ttu-id="7eb91-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-181">'Identity'</span></span>
- <span data-ttu-id="7eb91-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-183">'Razor'</span></span>
- <span data-ttu-id="7eb91-184">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-185">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-186">'Blazor'</span></span>
- <span data-ttu-id="7eb91-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-187">'Identity'</span></span>
- <span data-ttu-id="7eb91-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-189">'Razor'</span></span>
- <span data-ttu-id="7eb91-190">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-191">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-192">'Blazor'</span></span>
- <span data-ttu-id="7eb91-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-193">'Identity'</span></span>
- <span data-ttu-id="7eb91-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-195">'Razor'</span></span>
- <span data-ttu-id="7eb91-196">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-197">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-198">'Blazor'</span></span>
- <span data-ttu-id="7eb91-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-199">'Identity'</span></span>
- <span data-ttu-id="7eb91-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-201">'Razor'</span></span>
- <span data-ttu-id="7eb91-202">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-203">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-204">'Blazor'</span></span>
- <span data-ttu-id="7eb91-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-205">'Identity'</span></span>
- <span data-ttu-id="7eb91-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-207">'Razor'</span></span>
- <span data-ttu-id="7eb91-208">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-209">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-210">'Blazor'</span></span>
- <span data-ttu-id="7eb91-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-211">'Identity'</span></span>
- <span data-ttu-id="7eb91-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-213">'Razor'</span></span>
- <span data-ttu-id="7eb91-214">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-215">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-216">'Blazor'</span></span>
- <span data-ttu-id="7eb91-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-217">'Identity'</span></span>
- <span data-ttu-id="7eb91-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-219">'Razor'</span></span>
- <span data-ttu-id="7eb91-220">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-221">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-222">'Blazor'</span></span>
- <span data-ttu-id="7eb91-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-223">'Identity'</span></span>
- <span data-ttu-id="7eb91-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-225">'Razor'</span></span>
- <span data-ttu-id="7eb91-226">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-227">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-228">'Blazor'</span></span>
- <span data-ttu-id="7eb91-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-229">'Identity'</span></span>
- <span data-ttu-id="7eb91-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-231">'Razor'</span></span>
- <span data-ttu-id="7eb91-232">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-233">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-234">'Blazor'</span></span>
- <span data-ttu-id="7eb91-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-235">'Identity'</span></span>
- <span data-ttu-id="7eb91-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-237">'Razor'</span></span>
- <span data-ttu-id="7eb91-238">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-239">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-240">'Blazor'</span></span>
- <span data-ttu-id="7eb91-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-241">'Identity'</span></span>
- <span data-ttu-id="7eb91-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-243">'Razor'</span></span>
- <span data-ttu-id="7eb91-244">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-245">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-246">'Blazor'</span></span>
- <span data-ttu-id="7eb91-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-247">'Identity'</span></span>
- <span data-ttu-id="7eb91-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-249">'Razor'</span></span>
- <span data-ttu-id="7eb91-250">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-251">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-252">'Blazor'</span></span>
- <span data-ttu-id="7eb91-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-253">'Identity'</span></span>
- <span data-ttu-id="7eb91-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-255">'Razor'</span></span>
- <span data-ttu-id="7eb91-256">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-257">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-258">'Blazor'</span></span>
- <span data-ttu-id="7eb91-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-259">'Identity'</span></span>
- <span data-ttu-id="7eb91-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-261">'Razor'</span></span>
- <span data-ttu-id="7eb91-262">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-263">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-264">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-264">'Blazor'</span></span>
- <span data-ttu-id="7eb91-265">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-265">'Identity'</span></span>
- <span data-ttu-id="7eb91-266">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-266">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-267">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-267">'Razor'</span></span>
- <span data-ttu-id="7eb91-268">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-268">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-269">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-270">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-270">'Blazor'</span></span>
- <span data-ttu-id="7eb91-271">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-271">'Identity'</span></span>
- <span data-ttu-id="7eb91-272">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-272">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-273">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-273">'Razor'</span></span>
- <span data-ttu-id="7eb91-274">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-274">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-275">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-276">'Blazor'</span></span>
- <span data-ttu-id="7eb91-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-277">'Identity'</span></span>
- <span data-ttu-id="7eb91-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-279">'Razor'</span></span>
- <span data-ttu-id="7eb91-280">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-281">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-282">'Blazor'</span></span>
- <span data-ttu-id="7eb91-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-283">'Identity'</span></span>
- <span data-ttu-id="7eb91-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-285">'Razor'</span></span>
- <span data-ttu-id="7eb91-286">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-287">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-288">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-288">'Blazor'</span></span>
- <span data-ttu-id="7eb91-289">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-289">'Identity'</span></span>
- <span data-ttu-id="7eb91-290">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-290">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-291">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-291">'Razor'</span></span>
- <span data-ttu-id="7eb91-292">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-292">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-293">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-294">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-294">'Blazor'</span></span>
- <span data-ttu-id="7eb91-295">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-295">'Identity'</span></span>
- <span data-ttu-id="7eb91-296">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-296">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-297">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-297">'Razor'</span></span>
- <span data-ttu-id="7eb91-298">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-298">'SignalR' uid:</span></span> 

<span data-ttu-id="7eb91-299">-------------------------------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-299">-------------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-300">'Blazor'</span></span>
- <span data-ttu-id="7eb91-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-301">'Identity'</span></span>
- <span data-ttu-id="7eb91-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-303">'Razor'</span></span>
- <span data-ttu-id="7eb91-304">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-304">'SignalR' uid:</span></span> 

<span data-ttu-id="7eb91-305">--- | | [veřejné](https://tools.ietf.org/html/rfc7234#section-5.2.2.5) | Mezipaměť může ukládat odpověď.</span><span class="sxs-lookup"><span data-stu-id="7eb91-305">--- | | [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | A cache may store the response.</span></span> <span data-ttu-id="7eb91-306">| | [privátní](https://tools.ietf.org/html/rfc7234#section-5.2.2.6) | Odpověď nesmí být uložena sdílenou mezipamětí.</span><span class="sxs-lookup"><span data-stu-id="7eb91-306">| | [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | The response must not be stored by a shared cache.</span></span> <span data-ttu-id="7eb91-307">Soukromá mezipaměť může uložit a znovu použít odpověď.</span><span class="sxs-lookup"><span data-stu-id="7eb91-307">A private cache may store and reuse the response.</span></span> <span data-ttu-id="7eb91-308">| | [maximální stáří](https://tools.ietf.org/html/rfc7234#section-5.2.1.1) | Klient nepřijme odpověď, jejíž stáří je větší než zadaný počet sekund.</span><span class="sxs-lookup"><span data-stu-id="7eb91-308">| | [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="7eb91-309">Příklady: `max-age=60` (60 sekund), `max-age=2592000` (1 měsíc) | | [No – mezipaměť](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)  |  **V případě požadavků**: mezipaměť nesmí k uspokojení požadavku použít uloženou odpověď.</span><span class="sxs-lookup"><span data-stu-id="7eb91-309">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month) | | [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="7eb91-310">Zdrojový server znovu vygeneruje odpověď pro klienta a middleware aktualizuje uloženou odpověď v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-310">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="7eb91-311">**Na odpovědích**: odpověď nesmí být použita pro následné žádosti bez ověření na zdrojovém serveru.</span><span class="sxs-lookup"><span data-stu-id="7eb91-311">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> <span data-ttu-id="7eb91-312">| | [bez uložení](https://tools.ietf.org/html/rfc7234#section-5.2.1.5)  |  **V žádostech**: mezipaměť nesmí ukládat požadavek.</span><span class="sxs-lookup"><span data-stu-id="7eb91-312">| | [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="7eb91-313">**V odpovědích**: mezipaměť nesmí ukládat žádnou část odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7eb91-313">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="7eb91-314">Další hlavičky mezipaměti, které hrají roli v mezipaměti, jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="7eb91-314">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="7eb91-315">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="7eb91-315">Header</span></span>                                                     | <span data-ttu-id="7eb91-316">Funkce</span><span class="sxs-lookup"><span data-stu-id="7eb91-316">Function</span></span> |
| ---
<span data-ttu-id="7eb91-317">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-318">'Blazor'</span></span>
- <span data-ttu-id="7eb91-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-319">'Identity'</span></span>
- <span data-ttu-id="7eb91-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-321">'Razor'</span></span>
- <span data-ttu-id="7eb91-322">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-323">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-324">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-324">'Blazor'</span></span>
- <span data-ttu-id="7eb91-325">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-325">'Identity'</span></span>
- <span data-ttu-id="7eb91-326">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-326">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-327">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-327">'Razor'</span></span>
- <span data-ttu-id="7eb91-328">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-328">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-329">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-330">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-330">'Blazor'</span></span>
- <span data-ttu-id="7eb91-331">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-331">'Identity'</span></span>
- <span data-ttu-id="7eb91-332">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-332">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-333">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-333">'Razor'</span></span>
- <span data-ttu-id="7eb91-334">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-334">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-335">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-336">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-336">'Blazor'</span></span>
- <span data-ttu-id="7eb91-337">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-337">'Identity'</span></span>
- <span data-ttu-id="7eb91-338">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-338">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-339">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-339">'Razor'</span></span>
- <span data-ttu-id="7eb91-340">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-340">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-341">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-342">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-342">'Blazor'</span></span>
- <span data-ttu-id="7eb91-343">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-343">'Identity'</span></span>
- <span data-ttu-id="7eb91-344">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-344">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-345">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-345">'Razor'</span></span>
- <span data-ttu-id="7eb91-346">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-346">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-347">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-348">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-348">'Blazor'</span></span>
- <span data-ttu-id="7eb91-349">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-349">'Identity'</span></span>
- <span data-ttu-id="7eb91-350">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-350">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-351">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-351">'Razor'</span></span>
- <span data-ttu-id="7eb91-352">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-352">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-353">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-354">'Blazor'</span></span>
- <span data-ttu-id="7eb91-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-355">'Identity'</span></span>
- <span data-ttu-id="7eb91-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-357">'Razor'</span></span>
- <span data-ttu-id="7eb91-358">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-359">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-360">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-360">'Blazor'</span></span>
- <span data-ttu-id="7eb91-361">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-361">'Identity'</span></span>
- <span data-ttu-id="7eb91-362">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-362">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-363">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-363">'Razor'</span></span>
- <span data-ttu-id="7eb91-364">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-364">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-365">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-366">'Blazor'</span></span>
- <span data-ttu-id="7eb91-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-367">'Identity'</span></span>
- <span data-ttu-id="7eb91-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-369">'Razor'</span></span>
- <span data-ttu-id="7eb91-370">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-371">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-372">'Blazor'</span></span>
- <span data-ttu-id="7eb91-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-373">'Identity'</span></span>
- <span data-ttu-id="7eb91-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-375">'Razor'</span></span>
- <span data-ttu-id="7eb91-376">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-377">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-378">'Blazor'</span></span>
- <span data-ttu-id="7eb91-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-379">'Identity'</span></span>
- <span data-ttu-id="7eb91-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-381">'Razor'</span></span>
- <span data-ttu-id="7eb91-382">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-383">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-384">'Blazor'</span></span>
- <span data-ttu-id="7eb91-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-385">'Identity'</span></span>
- <span data-ttu-id="7eb91-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-387">'Razor'</span></span>
- <span data-ttu-id="7eb91-388">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-389">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-390">'Blazor'</span></span>
- <span data-ttu-id="7eb91-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-391">'Identity'</span></span>
- <span data-ttu-id="7eb91-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-393">'Razor'</span></span>
- <span data-ttu-id="7eb91-394">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-395">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-396">'Blazor'</span></span>
- <span data-ttu-id="7eb91-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-397">'Identity'</span></span>
- <span data-ttu-id="7eb91-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-399">'Razor'</span></span>
- <span data-ttu-id="7eb91-400">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-400">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-401">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-402">'Blazor'</span></span>
- <span data-ttu-id="7eb91-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-403">'Identity'</span></span>
- <span data-ttu-id="7eb91-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-405">'Razor'</span></span>
- <span data-ttu-id="7eb91-406">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-407">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-408">'Blazor'</span></span>
- <span data-ttu-id="7eb91-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-409">'Identity'</span></span>
- <span data-ttu-id="7eb91-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-411">'Razor'</span></span>
- <span data-ttu-id="7eb91-412">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-413">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-414">'Blazor'</span></span>
- <span data-ttu-id="7eb91-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-415">'Identity'</span></span>
- <span data-ttu-id="7eb91-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-417">'Razor'</span></span>
- <span data-ttu-id="7eb91-418">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-419">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-420">'Blazor'</span></span>
- <span data-ttu-id="7eb91-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-421">'Identity'</span></span>
- <span data-ttu-id="7eb91-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-423">'Razor'</span></span>
- <span data-ttu-id="7eb91-424">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-424">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-425">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-426">'Blazor'</span></span>
- <span data-ttu-id="7eb91-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-427">'Identity'</span></span>
- <span data-ttu-id="7eb91-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-429">'Razor'</span></span>
- <span data-ttu-id="7eb91-430">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-431">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-432">'Blazor'</span></span>
- <span data-ttu-id="7eb91-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-433">'Identity'</span></span>
- <span data-ttu-id="7eb91-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-435">'Razor'</span></span>
- <span data-ttu-id="7eb91-436">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-436">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-437">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-438">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-438">'Blazor'</span></span>
- <span data-ttu-id="7eb91-439">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-439">'Identity'</span></span>
- <span data-ttu-id="7eb91-440">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-440">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-441">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-441">'Razor'</span></span>
- <span data-ttu-id="7eb91-442">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-442">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-443">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-444">'Blazor'</span></span>
- <span data-ttu-id="7eb91-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-445">'Identity'</span></span>
- <span data-ttu-id="7eb91-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-447">'Razor'</span></span>
- <span data-ttu-id="7eb91-448">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-449">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-450">'Blazor'</span></span>
- <span data-ttu-id="7eb91-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-451">'Identity'</span></span>
- <span data-ttu-id="7eb91-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-453">'Razor'</span></span>
- <span data-ttu-id="7eb91-454">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-455">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-456">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-456">'Blazor'</span></span>
- <span data-ttu-id="7eb91-457">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-457">'Identity'</span></span>
- <span data-ttu-id="7eb91-458">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-458">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-459">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-459">'Razor'</span></span>
- <span data-ttu-id="7eb91-460">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-460">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-461">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-462">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-462">'Blazor'</span></span>
- <span data-ttu-id="7eb91-463">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-463">'Identity'</span></span>
- <span data-ttu-id="7eb91-464">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-464">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-465">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-465">'Razor'</span></span>
- <span data-ttu-id="7eb91-466">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-466">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-467">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-468">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-468">'Blazor'</span></span>
- <span data-ttu-id="7eb91-469">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-469">'Identity'</span></span>
- <span data-ttu-id="7eb91-470">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-470">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-471">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-471">'Razor'</span></span>
- <span data-ttu-id="7eb91-472">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-472">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-473">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-474">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-474">'Blazor'</span></span>
- <span data-ttu-id="7eb91-475">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-475">'Identity'</span></span>
- <span data-ttu-id="7eb91-476">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-476">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-477">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-477">'Razor'</span></span>
- <span data-ttu-id="7eb91-478">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-478">'SignalR' uid:</span></span> 

<span data-ttu-id="7eb91-479">----------------------------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-479">----------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-480">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-480">'Blazor'</span></span>
- <span data-ttu-id="7eb91-481">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-481">'Identity'</span></span>
- <span data-ttu-id="7eb91-482">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-482">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-483">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-483">'Razor'</span></span>
- <span data-ttu-id="7eb91-484">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-484">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-485">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-486">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-486">'Blazor'</span></span>
- <span data-ttu-id="7eb91-487">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-487">'Identity'</span></span>
- <span data-ttu-id="7eb91-488">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-488">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-489">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-489">'Razor'</span></span>
- <span data-ttu-id="7eb91-490">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-490">'SignalR' uid:</span></span> 

<span data-ttu-id="7eb91-491">---- | | [Stáří](https://tools.ietf.org/html/rfc7234#section-5.1) | Odhad doby v sekundách, po jejímž uplynutí byla odpověď generována nebo úspěšně ověřena na zdrojovém serveru.</span><span class="sxs-lookup"><span data-stu-id="7eb91-491">---- | | [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> <span data-ttu-id="7eb91-492">| | [Konec platnosti](https://tools.ietf.org/html/rfc7234#section-5.3) | Čas, po jehož uplynutí je odpověď považována za zastaralou.</span><span class="sxs-lookup"><span data-stu-id="7eb91-492">| | [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | The time after which the response is considered stale.</span></span> <span data-ttu-id="7eb91-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4) | Existuje pro zpětnou kompatibilitu s mezipamětí HTTP/1.0 pro `no-cache` chování nastavení.</span><span class="sxs-lookup"><span data-stu-id="7eb91-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="7eb91-494">Pokud `Cache-Control` je hlavička k dispozici, `Pragma` záhlaví je ignorováno.</span><span class="sxs-lookup"><span data-stu-id="7eb91-494">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> <span data-ttu-id="7eb91-495">| | [Různé](https://tools.ietf.org/html/rfc7231#section-7.1.4) | Určuje, že odpověď uložená v mezipaměti nesmí být odeslána, pokud se všechna `Vary` pole hlaviček shodují v původní žádosti odpovědi v mezipaměti i v nové žádosti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-495">| | [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="7eb91-496">Požadavky na ukládání do mezipaměti založené na protokolu HTTP respektují direktivy řízení mezipaměti</span><span class="sxs-lookup"><span data-stu-id="7eb91-496">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="7eb91-497">[Specifikace mezipaměti HTTP 1,1 pro hlavičku Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) vyžaduje mezipaměť, aby bylo možné akceptovat platnou `Cache-Control` hlavičku odeslanou klientem.</span><span class="sxs-lookup"><span data-stu-id="7eb91-497">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="7eb91-498">Klient může vytvořit požadavky s `no-cache` hodnotou hlavičky a vynutit, aby server vygeneroval novou odpověď pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="7eb91-498">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="7eb91-499">Použití `Cache-Control` hlaviček požadavků klienta je vhodné, pokud považujete cíl ukládání http do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-499">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="7eb91-500">V rámci oficiální specifikace je ukládání do mezipaměti určeno ke snížení latence a zatížení sítě při požadavcích na požadavky v síti klientů, proxy serverů a serverů.</span><span class="sxs-lookup"><span data-stu-id="7eb91-500">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="7eb91-501">To není nutně způsob, jak řídit zatížení na zdrojovém serveru.</span><span class="sxs-lookup"><span data-stu-id="7eb91-501">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="7eb91-502">Při použití [middleware pro ukládání odpovědí](xref:performance/caching/middleware) do mezipaměti není k dispozici žádné vývojářské řízení, protože middleware dodržuje oficiální specifikaci ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-502">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="7eb91-503">[Plánovaná vylepšení middlewaru](https://github.com/dotnet/AspNetCore/issues/2612) jsou příležitostí ke konfiguraci middlewaru pro ignorování `Cache-Control` hlavičky žádosti při rozhodování o obsluze odpovědi v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-503">[Planned enhancements to the middleware](https://github.com/dotnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="7eb91-504">Plánovaná vylepšení poskytují možnost lepšího řízení zatížení serveru.</span><span class="sxs-lookup"><span data-stu-id="7eb91-504">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="7eb91-505">Další technologie pro ukládání do mezipaměti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7eb91-505">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="7eb91-506">Ukládání do mezipaměti webového serveru</span><span class="sxs-lookup"><span data-stu-id="7eb91-506">In-memory caching</span></span>

<span data-ttu-id="7eb91-507">Ukládání do mezipaměti v paměti používá paměť serveru k ukládání dat uložených v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-507">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="7eb91-508">Tento typ ukládání do mezipaměti je vhodný pro jeden server nebo víc serverů pomocí *relací s rychlým cvičením*.</span><span class="sxs-lookup"><span data-stu-id="7eb91-508">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="7eb91-509">Rychlé relace znamenají, že požadavky klienta jsou vždycky směrovány na stejný server ke zpracování.</span><span class="sxs-lookup"><span data-stu-id="7eb91-509">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="7eb91-510">Další informace naleznete v tématu <xref:performance/caching/memory>.</span><span class="sxs-lookup"><span data-stu-id="7eb91-510">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="7eb91-511">Distribuovaná mezipaměť</span><span class="sxs-lookup"><span data-stu-id="7eb91-511">Distributed Cache</span></span>

<span data-ttu-id="7eb91-512">Pomocí distribuované mezipaměti můžete ukládat data v paměti, když je aplikace hostovaná v cloudové nebo serverové farmě.</span><span class="sxs-lookup"><span data-stu-id="7eb91-512">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="7eb91-513">Mezipaměť se sdílí mezi servery, které zpracovávají požadavky.</span><span class="sxs-lookup"><span data-stu-id="7eb91-513">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="7eb91-514">Klient může odeslat žádost, která je zpracována jakýmkoli serverem ve skupině, pokud jsou k dispozici data uložená v mezipaměti pro klienta.</span><span class="sxs-lookup"><span data-stu-id="7eb91-514">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="7eb91-515">ASP.NET Core pracuje s mezipamětí SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)a [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distribuované mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-515">ASP.NET Core works with SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), and [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distributed caches.</span></span>

<span data-ttu-id="7eb91-516">Další informace naleznete v tématu <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="7eb91-516">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="7eb91-517">Uložení pomocné rutiny značky do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="7eb91-517">Cache Tag Helper</span></span>

<span data-ttu-id="7eb91-518">Ukládání obsahu do mezipaměti pro zobrazení nebo Razor stránku MVC pomocí pomocníka značky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="7eb91-518">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="7eb91-519">Pomocník značek Cache používá ukládání dat do mezipaměti v paměti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-519">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="7eb91-520">Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="7eb91-520">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="7eb91-521">Pomocná rutina značek v distribuované mezipaměti</span><span class="sxs-lookup"><span data-stu-id="7eb91-521">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="7eb91-522">Ukládání obsahu do mezipaměti pro zobrazení nebo Razor stránku MVC v distribuovaném cloudu nebo ve scénářích webové farmy pomocí pomocníka značek distribuované mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-522">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="7eb91-523">Pomocný pomocník značek distribuované mezipaměti používá k ukládání dat SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)nebo [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) .</span><span class="sxs-lookup"><span data-stu-id="7eb91-523">The Distributed Cache Tag Helper uses SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), or [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) to store data.</span></span>

<span data-ttu-id="7eb91-524">Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="7eb91-524">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="7eb91-525">ResponseCache – atribut</span><span class="sxs-lookup"><span data-stu-id="7eb91-525">ResponseCache attribute</span></span>

<span data-ttu-id="7eb91-526"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Určuje parametry potřebné pro nastavení příslušných hlaviček v ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-526">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="7eb91-527">Zakáže ukládání do mezipaměti pro obsah, který obsahuje informace pro ověřené klienty.</span><span class="sxs-lookup"><span data-stu-id="7eb91-527">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="7eb91-528">Ukládání do mezipaměti by mělo být povolené jenom pro obsah, který se nemění v závislosti na identitě uživatele nebo na tom, jestli je uživatel přihlášený.</span><span class="sxs-lookup"><span data-stu-id="7eb91-528">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="7eb91-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>mění uloženou odpověď podle hodnot daného seznamu klíčů dotazů.</span><span class="sxs-lookup"><span data-stu-id="7eb91-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="7eb91-530">Pokud `*` je k dispozici jedna hodnota, middleware se liší odezvy všemi parametry řetězce dotazu na žádosti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-530">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="7eb91-531">Aby bylo možné nastavit vlastnost, musí být povoleno [middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> .</span><span class="sxs-lookup"><span data-stu-id="7eb91-531">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="7eb91-532">V opačném případě je vyvolána výjimka modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="7eb91-532">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="7eb91-533">Pro vlastnost není k dispozici odpovídající hlavička protokolu HTTP <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> .</span><span class="sxs-lookup"><span data-stu-id="7eb91-533">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="7eb91-534">Vlastnost je funkce HTTP, kterou zpracovává middleware pro ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-534">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="7eb91-535">Aby middleware poskytovala odpověď uloženou v mezipaměti, řetězec dotazu a hodnota řetězce dotazu musí odpovídat předchozí žádosti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-535">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="7eb91-536">Zvažte například posloupnost požadavků a výsledků, které jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="7eb91-536">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="7eb91-537">Žádost</span><span class="sxs-lookup"><span data-stu-id="7eb91-537">Request</span></span>                          | <span data-ttu-id="7eb91-538">Výsledek</span><span class="sxs-lookup"><span data-stu-id="7eb91-538">Result</span></span>                    |
| ---
<span data-ttu-id="7eb91-539">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-540">'Blazor'</span></span>
- <span data-ttu-id="7eb91-541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-541">'Identity'</span></span>
- <span data-ttu-id="7eb91-542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-542">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-543">'Razor'</span></span>
- <span data-ttu-id="7eb91-544">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-545">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-546">'Blazor'</span></span>
- <span data-ttu-id="7eb91-547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-547">'Identity'</span></span>
- <span data-ttu-id="7eb91-548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-548">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-549">'Razor'</span></span>
- <span data-ttu-id="7eb91-550">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-551">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-552">'Blazor'</span></span>
- <span data-ttu-id="7eb91-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-553">'Identity'</span></span>
- <span data-ttu-id="7eb91-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-555">'Razor'</span></span>
- <span data-ttu-id="7eb91-556">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-557">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-558">'Blazor'</span></span>
- <span data-ttu-id="7eb91-559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-559">'Identity'</span></span>
- <span data-ttu-id="7eb91-560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-560">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-561">'Razor'</span></span>
- <span data-ttu-id="7eb91-562">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-563">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-564">'Blazor'</span></span>
- <span data-ttu-id="7eb91-565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-565">'Identity'</span></span>
- <span data-ttu-id="7eb91-566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-566">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-567">'Razor'</span></span>
- <span data-ttu-id="7eb91-568">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-569">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-570">'Blazor'</span></span>
- <span data-ttu-id="7eb91-571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-571">'Identity'</span></span>
- <span data-ttu-id="7eb91-572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-572">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-573">'Razor'</span></span>
- <span data-ttu-id="7eb91-574">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-575">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-576">'Blazor'</span></span>
- <span data-ttu-id="7eb91-577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-577">'Identity'</span></span>
- <span data-ttu-id="7eb91-578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-578">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-579">'Razor'</span></span>
- <span data-ttu-id="7eb91-580">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-581">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-582">'Blazor'</span></span>
- <span data-ttu-id="7eb91-583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-583">'Identity'</span></span>
- <span data-ttu-id="7eb91-584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-584">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-585">'Razor'</span></span>
- <span data-ttu-id="7eb91-586">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-586">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-587">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-588">'Blazor'</span></span>
- <span data-ttu-id="7eb91-589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-589">'Identity'</span></span>
- <span data-ttu-id="7eb91-590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-590">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-591">'Razor'</span></span>
- <span data-ttu-id="7eb91-592">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-592">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-593">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-594">'Blazor'</span></span>
- <span data-ttu-id="7eb91-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-595">'Identity'</span></span>
- <span data-ttu-id="7eb91-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-597">'Razor'</span></span>
- <span data-ttu-id="7eb91-598">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-599">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-600">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-600">'Blazor'</span></span>
- <span data-ttu-id="7eb91-601">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-601">'Identity'</span></span>
- <span data-ttu-id="7eb91-602">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-602">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-603">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-603">'Razor'</span></span>
- <span data-ttu-id="7eb91-604">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-604">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-605">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-606">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-606">'Blazor'</span></span>
- <span data-ttu-id="7eb91-607">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-607">'Identity'</span></span>
- <span data-ttu-id="7eb91-608">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-608">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-609">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-609">'Razor'</span></span>
- <span data-ttu-id="7eb91-610">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-610">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-611">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-612">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-612">'Blazor'</span></span>
- <span data-ttu-id="7eb91-613">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-613">'Identity'</span></span>
- <span data-ttu-id="7eb91-614">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-614">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-615">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-615">'Razor'</span></span>
- <span data-ttu-id="7eb91-616">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-616">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-617">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-618">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-618">'Blazor'</span></span>
- <span data-ttu-id="7eb91-619">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-619">'Identity'</span></span>
- <span data-ttu-id="7eb91-620">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-620">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-621">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-621">'Razor'</span></span>
- <span data-ttu-id="7eb91-622">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-622">'SignalR' uid:</span></span> 

<span data-ttu-id="7eb91-623">---------------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-623">---------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-624">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-624">'Blazor'</span></span>
- <span data-ttu-id="7eb91-625">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-625">'Identity'</span></span>
- <span data-ttu-id="7eb91-626">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-626">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-627">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-627">'Razor'</span></span>
- <span data-ttu-id="7eb91-628">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-628">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-629">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-630">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-630">'Blazor'</span></span>
- <span data-ttu-id="7eb91-631">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-631">'Identity'</span></span>
- <span data-ttu-id="7eb91-632">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-632">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-633">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-633">'Razor'</span></span>
- <span data-ttu-id="7eb91-634">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-634">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-635">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-636">'Blazor'</span></span>
- <span data-ttu-id="7eb91-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-637">'Identity'</span></span>
- <span data-ttu-id="7eb91-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-639">'Razor'</span></span>
- <span data-ttu-id="7eb91-640">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-641">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-642">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-642">'Blazor'</span></span>
- <span data-ttu-id="7eb91-643">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-643">'Identity'</span></span>
- <span data-ttu-id="7eb91-644">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-644">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-645">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-645">'Razor'</span></span>
- <span data-ttu-id="7eb91-646">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-646">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-647">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-648">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-648">'Blazor'</span></span>
- <span data-ttu-id="7eb91-649">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-649">'Identity'</span></span>
- <span data-ttu-id="7eb91-650">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-650">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-651">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-651">'Razor'</span></span>
- <span data-ttu-id="7eb91-652">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-652">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-653">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-654">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-654">'Blazor'</span></span>
- <span data-ttu-id="7eb91-655">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-655">'Identity'</span></span>
- <span data-ttu-id="7eb91-656">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-656">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-657">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-657">'Razor'</span></span>
- <span data-ttu-id="7eb91-658">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-658">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-659">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-660">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-660">'Blazor'</span></span>
- <span data-ttu-id="7eb91-661">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-661">'Identity'</span></span>
- <span data-ttu-id="7eb91-662">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-662">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-663">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-663">'Razor'</span></span>
- <span data-ttu-id="7eb91-664">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-664">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-665">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-666">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-666">'Blazor'</span></span>
- <span data-ttu-id="7eb91-667">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-667">'Identity'</span></span>
- <span data-ttu-id="7eb91-668">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-668">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-669">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-669">'Razor'</span></span>
- <span data-ttu-id="7eb91-670">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-670">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-671">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-672">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-672">'Blazor'</span></span>
- <span data-ttu-id="7eb91-673">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-673">'Identity'</span></span>
- <span data-ttu-id="7eb91-674">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-674">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-675">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-675">'Razor'</span></span>
- <span data-ttu-id="7eb91-676">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-676">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7eb91-677">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7eb91-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7eb91-678">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-678">'Blazor'</span></span>
- <span data-ttu-id="7eb91-679">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7eb91-679">'Identity'</span></span>
- <span data-ttu-id="7eb91-680">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7eb91-680">'Let's Encrypt'</span></span>
- <span data-ttu-id="7eb91-681">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7eb91-681">'Razor'</span></span>
- <span data-ttu-id="7eb91-682">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="7eb91-682">'SignalR' uid:</span></span> 

<span data-ttu-id="7eb91-683">------------- | | `http://example.com?key1=value1` | Vráceno ze serveru.</span><span class="sxs-lookup"><span data-stu-id="7eb91-683">------------- | | `http://example.com?key1=value1` | Returned from the server.</span></span> <span data-ttu-id="7eb91-684">| | `http://example.com?key1=value1` | Vráceno z middlewaru.</span><span class="sxs-lookup"><span data-stu-id="7eb91-684">| | `http://example.com?key1=value1` | Returned from middleware.</span></span> <span data-ttu-id="7eb91-685">| | `http://example.com?key1=value2` | Vráceno ze serveru.</span><span class="sxs-lookup"><span data-stu-id="7eb91-685">| | `http://example.com?key1=value2` | Returned from the server.</span></span> |

<span data-ttu-id="7eb91-686">První požadavek vrací Server a ukládá do mezipaměti v middlewaru.</span><span class="sxs-lookup"><span data-stu-id="7eb91-686">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="7eb91-687">Druhý požadavek vrací middleware, protože řetězec dotazu odpovídá předchozímu požadavku.</span><span class="sxs-lookup"><span data-stu-id="7eb91-687">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="7eb91-688">Třetí žádost není v mezipaměti middlewaru, protože hodnota řetězce dotazu neodpovídá předchozí žádosti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-688">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="7eb91-689"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Slouží ke konfiguraci a vytvoření (prostřednictvím) a <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` .</span><span class="sxs-lookup"><span data-stu-id="7eb91-689">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="7eb91-690">`ResponseCacheFilter`Provede práci s aktualizací odpovídajících hlaviček protokolu HTTP a funkcí odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7eb91-690">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="7eb91-691">Filtr:</span><span class="sxs-lookup"><span data-stu-id="7eb91-691">The filter:</span></span>

* <span data-ttu-id="7eb91-692">Odebere všechna existující záhlaví pro `Vary` , `Cache-Control` a `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="7eb91-692">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="7eb91-693">Zapíše odpovídající hlavičky na základě vlastností nastavených v <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> .</span><span class="sxs-lookup"><span data-stu-id="7eb91-693">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="7eb91-694">Aktualizuje funkci ukládání do mezipaměti odpovědí HTTP, pokud <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> je nastavená.</span><span class="sxs-lookup"><span data-stu-id="7eb91-694">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="7eb91-695">Toho</span><span class="sxs-lookup"><span data-stu-id="7eb91-695">Vary</span></span>

<span data-ttu-id="7eb91-696">Tato hlavička je zapsána pouze v případě, že <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> je nastavena vlastnost.</span><span class="sxs-lookup"><span data-stu-id="7eb91-696">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="7eb91-697">Vlastnost nastavená na `Vary` hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-697">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="7eb91-698">Následující příklad používá <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> vlastnost:</span><span class="sxs-lookup"><span data-stu-id="7eb91-698">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="7eb91-699">Pomocí ukázkové aplikace si prohlédněte hlavičky odpovědí pomocí síťových nástrojů v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="7eb91-699">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="7eb91-700">Následující hlavičky odpovědí se odesílají s odpovědí na stránku Cache1:</span><span class="sxs-lookup"><span data-stu-id="7eb91-700">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="7eb91-701">Úložiště a umístění. žádné</span><span class="sxs-lookup"><span data-stu-id="7eb91-701">NoStore and Location.None</span></span>

<span data-ttu-id="7eb91-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>přepíše většinu ostatních vlastností.</span><span class="sxs-lookup"><span data-stu-id="7eb91-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="7eb91-703">Je-li tato vlastnost nastavena na hodnotu `true` , `Cache-Control` záhlaví je nastaveno na `no-store` .</span><span class="sxs-lookup"><span data-stu-id="7eb91-703">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="7eb91-704">Pokud <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> je nastaveno na `None` :</span><span class="sxs-lookup"><span data-stu-id="7eb91-704">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="7eb91-705">`Cache-Control`je nastaven na `no-store,no-cache` .</span><span class="sxs-lookup"><span data-stu-id="7eb91-705">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="7eb91-706">`Pragma`je nastaven na `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="7eb91-706">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="7eb91-707">Pokud <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> je `false` a <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> je `None` , `Cache-Control` , a `Pragma` jsou nastaveny na `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="7eb91-707">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="7eb91-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>je obvykle nastaveno na `true` pro chybové stránky.</span><span class="sxs-lookup"><span data-stu-id="7eb91-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="7eb91-709">Stránka Cache2 v ukázkové aplikaci vytváří hlavičky odpovědí, které klientovi neukládají odpověď.</span><span class="sxs-lookup"><span data-stu-id="7eb91-709">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="7eb91-710">Ukázková aplikace vrátí stránku Cache2 s následujícími záhlavími:</span><span class="sxs-lookup"><span data-stu-id="7eb91-710">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="7eb91-711">Umístění a doba trvání</span><span class="sxs-lookup"><span data-stu-id="7eb91-711">Location and Duration</span></span>

<span data-ttu-id="7eb91-712">Aby bylo možné povolit ukládání do mezipaměti, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> musí být nastaveno na kladnou hodnotu a <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> musí být buď `Any` (výchozí), nebo `Client` .</span><span class="sxs-lookup"><span data-stu-id="7eb91-712">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="7eb91-713">Rozhraní nastaví `Cache-Control` hlavičku na hodnotu umístění následovanou `max-age` odpovědí.</span><span class="sxs-lookup"><span data-stu-id="7eb91-713">The framework sets the `Cache-Control` header to the location value followed by the `max-age` of the response.</span></span>

<span data-ttu-id="7eb91-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>možnosti `Any` a `Client` překládat do `Cache-Control` hodnot záhlaví a v `public` `private` uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="7eb91-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="7eb91-715">Jak je uvedeno v části [úložiště a umístění. None](#nostore-and-locationnone) , nastavení <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> pro nastavení `None` `Cache-Control` `Pragma` hlaviček na `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="7eb91-715">As noted in the [NoStore and Location.None](#nostore-and-locationnone) section, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="7eb91-716">`Location.Any`( `Cache-Control` nastaveno na `public` ) znamená, že *klient nebo jakýkoli zprostředkující proxy server* může hodnotu ukládat do mezipaměti, včetně [middlewaru pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="7eb91-716">`Location.Any` (`Cache-Control` set to `public`) indicates that the *client or any intermediate proxy* may cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="7eb91-717">`Location.Client`( `Cache-Control` nastaveno na `private` ) znamená, že hodnotu může ukládat *pouze klient* .</span><span class="sxs-lookup"><span data-stu-id="7eb91-717">`Location.Client` (`Cache-Control` set to `private`) indicates that *only the client* may cache the value.</span></span> <span data-ttu-id="7eb91-718">Žádná mezimezipaměť by neměla hodnotu ukládat do mezipaměti, včetně [middlewaru pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="7eb91-718">No intermediate cache should cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="7eb91-719">Řídicí hlavičky mezipaměti pouze poskytují pokyny klientům a zprostředkujícím proxy serverům, kdy a jak ukládat odpovědi do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-719">Cache control headers merely provide guidance to clients and intermediary proxies when and how to cache responses.</span></span> <span data-ttu-id="7eb91-720">Není nijak zaručeno, že klienti a proxy budou respektovat [specifikace mezipaměti HTTP 1,1](https://tools.ietf.org/html/rfc7234).</span><span class="sxs-lookup"><span data-stu-id="7eb91-720">There's no guarantee that clients and proxies will honor the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span> <span data-ttu-id="7eb91-721">[Middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware) bude vždycky postupovat podle pravidel pro ukládání do mezipaměti, která jsou stanovená specifikací</span><span class="sxs-lookup"><span data-stu-id="7eb91-721">[Response Caching Middleware](xref:performance/caching/middleware) always follows the caching rules laid out by the specification.</span></span>

<span data-ttu-id="7eb91-722">Následující příklad ukazuje model stránky Cache3 z ukázkové aplikace a záhlaví vytvořená nastavením <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> a ponecháním výchozí <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> hodnoty:</span><span class="sxs-lookup"><span data-stu-id="7eb91-722">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="7eb91-723">Ukázková aplikace vrátí stránku Cache3 s následující hlavičkou:</span><span class="sxs-lookup"><span data-stu-id="7eb91-723">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="7eb91-724">Profily mezipaměti</span><span class="sxs-lookup"><span data-stu-id="7eb91-724">Cache profiles</span></span>

<span data-ttu-id="7eb91-725">Místo duplikace nastavení mezipaměti odpovědí u mnoha atributů akce kontroleru lze profily mezipaměti nakonfigurovat jako možnosti při nastavování MVC nebo Razor stránek v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="7eb91-725">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7eb91-726">Hodnoty nalezené v profilu odkazované mezipaměti se používají jako výchozí hodnoty <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> a jsou potlačeny všemi vlastnostmi zadanými v atributu.</span><span class="sxs-lookup"><span data-stu-id="7eb91-726">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="7eb91-727">Nastavte profil mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7eb91-727">Set up a cache profile.</span></span> <span data-ttu-id="7eb91-728">Následující příklad ukazuje 30 sekundový profil mezipaměti v ukázkové aplikaci `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7eb91-728">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="7eb91-729">Model stránky Cache4 ukázkové aplikace odkazuje na `Default30` profil mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="7eb91-729">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="7eb91-730"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Lze použít pro:</span><span class="sxs-lookup"><span data-stu-id="7eb91-730">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* Razor<span data-ttu-id="7eb91-731">Obslužné rutiny stránky (třídy): atributy nelze použít na metody obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="7eb91-731"> Page handlers (classes): Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="7eb91-732">Řadiče MVC (třídy).</span><span class="sxs-lookup"><span data-stu-id="7eb91-732">MVC controllers (classes).</span></span>
* <span data-ttu-id="7eb91-733">Akce MVC (metody): atributy na úrovni metody přepíšou nastavení zadaná v atributech na úrovni třídy.</span><span class="sxs-lookup"><span data-stu-id="7eb91-733">MVC actions (methods): Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="7eb91-734">Výsledná hlavička použitá pro odpověď stránky Cache4 `Default30` profil mezipaměti:</span><span class="sxs-lookup"><span data-stu-id="7eb91-734">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="7eb91-735">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7eb91-735">Additional resources</span></span>

* [<span data-ttu-id="7eb91-736">Ukládání odpovědí do mezipamětí</span><span class="sxs-lookup"><span data-stu-id="7eb91-736">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="7eb91-737">Řízení mezipaměti</span><span class="sxs-lookup"><span data-stu-id="7eb91-737">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
