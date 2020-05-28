---
<span data-ttu-id="d8702-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-102">'Blazor'</span></span>
- <span data-ttu-id="d8702-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-103">'Identity'</span></span>
- <span data-ttu-id="d8702-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-105">'Razor'</span></span>
- <span data-ttu-id="d8702-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-106">'SignalR' uid:</span></span> 

---
# <a name="compare-grpc-services-with-http-apis"></a><span data-ttu-id="d8702-107">Porovnání služeb gRPC pomocí rozhraní HTTP API</span><span class="sxs-lookup"><span data-stu-id="d8702-107">Compare gRPC services with HTTP APIs</span></span>

<span data-ttu-id="d8702-108">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="d8702-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="d8702-109">Tento článek vysvětluje, jak [gRPC služby](https://grpc.io/docs/guides/) porovnávají s rozhraními HTTP API pomocí JSON (včetně ASP.NET Core [webových rozhraní API](xref:web-api/index)).</span><span class="sxs-lookup"><span data-stu-id="d8702-109">This article explains how [gRPC services](https://grpc.io/docs/guides/) compare to HTTP APIs with JSON (including ASP.NET Core [web APIs](xref:web-api/index)).</span></span> <span data-ttu-id="d8702-110">Technologie používaná k poskytování rozhraní API pro vaši aplikaci je důležitou volbou a gRPC nabízí jedinečné výhody v porovnání s rozhraními API protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="d8702-110">The technology used to provide an API for your app is an important choice, and gRPC offers unique benefits compared to HTTP APIs.</span></span> <span data-ttu-id="d8702-111">Tento článek popisuje silné a slabé stránky gRPC a doporučuje scénáře použití gRPC nad jinými technologiemi.</span><span class="sxs-lookup"><span data-stu-id="d8702-111">This article discusses the strengths and weaknesses of gRPC and recommends scenarios for using gRPC over other technologies.</span></span>

## <a name="high-level-comparison"></a><span data-ttu-id="d8702-112">Porovnání na vysoké úrovni</span><span class="sxs-lookup"><span data-stu-id="d8702-112">High-level comparison</span></span>

<span data-ttu-id="d8702-113">Následující tabulka nabízí vysoké srovnání funkcí mezi gRPC a rozhraními API protokolu HTTP s JSON.</span><span class="sxs-lookup"><span data-stu-id="d8702-113">The following table offers a high-level comparison of features between gRPC and HTTP APIs with JSON.</span></span>

| <span data-ttu-id="d8702-114">Funkce</span><span class="sxs-lookup"><span data-stu-id="d8702-114">Feature</span></span>          | <span data-ttu-id="d8702-115">gRPC</span><span class="sxs-lookup"><span data-stu-id="d8702-115">gRPC</span></span>                                               | <span data-ttu-id="d8702-116">HTTP API s JSON</span><span class="sxs-lookup"><span data-stu-id="d8702-116">HTTP APIs with JSON</span></span>           |
| ---
<span data-ttu-id="d8702-117">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-117">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-118">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-118">'Blazor'</span></span>
- <span data-ttu-id="d8702-119">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-119">'Identity'</span></span>
- <span data-ttu-id="d8702-120">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-120">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-121">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-121">'Razor'</span></span>
- <span data-ttu-id="d8702-122">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-122">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-123">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-123">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-124">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-124">'Blazor'</span></span>
- <span data-ttu-id="d8702-125">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-125">'Identity'</span></span>
- <span data-ttu-id="d8702-126">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-126">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-127">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-127">'Razor'</span></span>
- <span data-ttu-id="d8702-128">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-128">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-129">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-129">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-130">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-130">'Blazor'</span></span>
- <span data-ttu-id="d8702-131">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-131">'Identity'</span></span>
- <span data-ttu-id="d8702-132">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-132">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-133">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-133">'Razor'</span></span>
- <span data-ttu-id="d8702-134">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-134">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-135">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-135">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-136">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-136">'Blazor'</span></span>
- <span data-ttu-id="d8702-137">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-137">'Identity'</span></span>
- <span data-ttu-id="d8702-138">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-138">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-139">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-139">'Razor'</span></span>
- <span data-ttu-id="d8702-140">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-140">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-141">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-141">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-142">'Blazor'</span></span>
- <span data-ttu-id="d8702-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-143">'Identity'</span></span>
- <span data-ttu-id="d8702-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-145">'Razor'</span></span>
- <span data-ttu-id="d8702-146">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-146">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-147">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-147">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-148">'Blazor'</span></span>
- <span data-ttu-id="d8702-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-149">'Identity'</span></span>
- <span data-ttu-id="d8702-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-151">'Razor'</span></span>
- <span data-ttu-id="d8702-152">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-152">'SignalR' uid:</span></span> 

<span data-ttu-id="d8702-153">-------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-153">-------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-154">'Blazor'</span></span>
- <span data-ttu-id="d8702-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-155">'Identity'</span></span>
- <span data-ttu-id="d8702-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-157">'Razor'</span></span>
- <span data-ttu-id="d8702-158">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-159">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-159">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-160">'Blazor'</span></span>
- <span data-ttu-id="d8702-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-161">'Identity'</span></span>
- <span data-ttu-id="d8702-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-163">'Razor'</span></span>
- <span data-ttu-id="d8702-164">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-165">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-165">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-166">'Blazor'</span></span>
- <span data-ttu-id="d8702-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-167">'Identity'</span></span>
- <span data-ttu-id="d8702-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-169">'Razor'</span></span>
- <span data-ttu-id="d8702-170">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-171">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-171">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-172">'Blazor'</span></span>
- <span data-ttu-id="d8702-173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-173">'Identity'</span></span>
- <span data-ttu-id="d8702-174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-174">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-175">'Razor'</span></span>
- <span data-ttu-id="d8702-176">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-177">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-177">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-178">'Blazor'</span></span>
- <span data-ttu-id="d8702-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-179">'Identity'</span></span>
- <span data-ttu-id="d8702-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-181">'Razor'</span></span>
- <span data-ttu-id="d8702-182">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-183">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-183">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-184">'Blazor'</span></span>
- <span data-ttu-id="d8702-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-185">'Identity'</span></span>
- <span data-ttu-id="d8702-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-187">'Razor'</span></span>
- <span data-ttu-id="d8702-188">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-189">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-189">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-190">'Blazor'</span></span>
- <span data-ttu-id="d8702-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-191">'Identity'</span></span>
- <span data-ttu-id="d8702-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-193">'Razor'</span></span>
- <span data-ttu-id="d8702-194">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-195">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-195">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-196">'Blazor'</span></span>
- <span data-ttu-id="d8702-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-197">'Identity'</span></span>
- <span data-ttu-id="d8702-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-199">'Razor'</span></span>
- <span data-ttu-id="d8702-200">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-201">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-201">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-202">'Blazor'</span></span>
- <span data-ttu-id="d8702-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-203">'Identity'</span></span>
- <span data-ttu-id="d8702-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-205">'Razor'</span></span>
- <span data-ttu-id="d8702-206">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-207">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-207">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-208">'Blazor'</span></span>
- <span data-ttu-id="d8702-209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-209">'Identity'</span></span>
- <span data-ttu-id="d8702-210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-210">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-211">'Razor'</span></span>
- <span data-ttu-id="d8702-212">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-213">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-213">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-214">'Blazor'</span></span>
- <span data-ttu-id="d8702-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-215">'Identity'</span></span>
- <span data-ttu-id="d8702-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-217">'Razor'</span></span>
- <span data-ttu-id="d8702-218">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-219">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-219">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-220">'Blazor'</span></span>
- <span data-ttu-id="d8702-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-221">'Identity'</span></span>
- <span data-ttu-id="d8702-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-223">'Razor'</span></span>
- <span data-ttu-id="d8702-224">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-225">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-225">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-226">'Blazor'</span></span>
- <span data-ttu-id="d8702-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-227">'Identity'</span></span>
- <span data-ttu-id="d8702-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-229">'Razor'</span></span>
- <span data-ttu-id="d8702-230">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-231">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-231">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-232">'Blazor'</span></span>
- <span data-ttu-id="d8702-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-233">'Identity'</span></span>
- <span data-ttu-id="d8702-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-235">'Razor'</span></span>
- <span data-ttu-id="d8702-236">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-237">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-237">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-238">'Blazor'</span></span>
- <span data-ttu-id="d8702-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-239">'Identity'</span></span>
- <span data-ttu-id="d8702-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-241">'Razor'</span></span>
- <span data-ttu-id="d8702-242">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-243">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-243">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-244">'Blazor'</span></span>
- <span data-ttu-id="d8702-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-245">'Identity'</span></span>
- <span data-ttu-id="d8702-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-247">'Razor'</span></span>
- <span data-ttu-id="d8702-248">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-249">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-249">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-250">'Blazor'</span></span>
- <span data-ttu-id="d8702-251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-251">'Identity'</span></span>
- <span data-ttu-id="d8702-252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-252">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-253">'Razor'</span></span>
- <span data-ttu-id="d8702-254">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-255">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-255">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-256">'Blazor'</span></span>
- <span data-ttu-id="d8702-257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-257">'Identity'</span></span>
- <span data-ttu-id="d8702-258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-258">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-259">'Razor'</span></span>
- <span data-ttu-id="d8702-260">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-261">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-261">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-262">'Blazor'</span></span>
- <span data-ttu-id="d8702-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-263">'Identity'</span></span>
- <span data-ttu-id="d8702-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-265">'Razor'</span></span>
- <span data-ttu-id="d8702-266">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-267">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-267">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-268">'Blazor'</span></span>
- <span data-ttu-id="d8702-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-269">'Identity'</span></span>
- <span data-ttu-id="d8702-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-271">'Razor'</span></span>
- <span data-ttu-id="d8702-272">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-273">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-273">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-274">'Blazor'</span></span>
- <span data-ttu-id="d8702-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-275">'Identity'</span></span>
- <span data-ttu-id="d8702-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-277">'Razor'</span></span>
- <span data-ttu-id="d8702-278">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-279">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-279">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-280">'Blazor'</span></span>
- <span data-ttu-id="d8702-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-281">'Identity'</span></span>
- <span data-ttu-id="d8702-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-283">'Razor'</span></span>
- <span data-ttu-id="d8702-284">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-285">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-285">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-286">'Blazor'</span></span>
- <span data-ttu-id="d8702-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-287">'Identity'</span></span>
- <span data-ttu-id="d8702-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-289">'Razor'</span></span>
- <span data-ttu-id="d8702-290">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-290">'SignalR' uid:</span></span> 

<span data-ttu-id="d8702-291">------------------------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-291">------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-292">'Blazor'</span></span>
- <span data-ttu-id="d8702-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-293">'Identity'</span></span>
- <span data-ttu-id="d8702-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-295">'Razor'</span></span>
- <span data-ttu-id="d8702-296">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-297">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-297">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-298">'Blazor'</span></span>
- <span data-ttu-id="d8702-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-299">'Identity'</span></span>
- <span data-ttu-id="d8702-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-301">'Razor'</span></span>
- <span data-ttu-id="d8702-302">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-303">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-303">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-304">'Blazor'</span></span>
- <span data-ttu-id="d8702-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-305">'Identity'</span></span>
- <span data-ttu-id="d8702-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-307">'Razor'</span></span>
- <span data-ttu-id="d8702-308">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-309">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-309">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-310">'Blazor'</span></span>
- <span data-ttu-id="d8702-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-311">'Identity'</span></span>
- <span data-ttu-id="d8702-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-313">'Razor'</span></span>
- <span data-ttu-id="d8702-314">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-315">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-315">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-316">'Blazor'</span></span>
- <span data-ttu-id="d8702-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-317">'Identity'</span></span>
- <span data-ttu-id="d8702-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-319">'Razor'</span></span>
- <span data-ttu-id="d8702-320">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-321">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-321">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-322">'Blazor'</span></span>
- <span data-ttu-id="d8702-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-323">'Identity'</span></span>
- <span data-ttu-id="d8702-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-325">'Razor'</span></span>
- <span data-ttu-id="d8702-326">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-327">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-327">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-328">'Blazor'</span></span>
- <span data-ttu-id="d8702-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-329">'Identity'</span></span>
- <span data-ttu-id="d8702-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-331">'Razor'</span></span>
- <span data-ttu-id="d8702-332">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-333">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-333">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-334">'Blazor'</span></span>
- <span data-ttu-id="d8702-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-335">'Identity'</span></span>
- <span data-ttu-id="d8702-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-337">'Razor'</span></span>
- <span data-ttu-id="d8702-338">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-339">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-339">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-340">'Blazor'</span></span>
- <span data-ttu-id="d8702-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-341">'Identity'</span></span>
- <span data-ttu-id="d8702-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-343">'Razor'</span></span>
- <span data-ttu-id="d8702-344">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-345">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-345">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-346">'Blazor'</span></span>
- <span data-ttu-id="d8702-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-347">'Identity'</span></span>
- <span data-ttu-id="d8702-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-349">'Razor'</span></span>
- <span data-ttu-id="d8702-350">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-351">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-351">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-352">'Blazor'</span></span>
- <span data-ttu-id="d8702-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-353">'Identity'</span></span>
- <span data-ttu-id="d8702-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-355">'Razor'</span></span>
- <span data-ttu-id="d8702-356">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d8702-357">Název: Autor: Popis: monikerRange: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="d8702-357">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="d8702-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d8702-358">'Blazor'</span></span>
- <span data-ttu-id="d8702-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d8702-359">'Identity'</span></span>
- <span data-ttu-id="d8702-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d8702-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="d8702-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d8702-361">'Razor'</span></span>
- <span data-ttu-id="d8702-362">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="d8702-362">'SignalR' uid:</span></span> 

<span data-ttu-id="d8702-363">--------------- | | Kontrakt | Požadováno (*. proto*) | Volitelné (OpenAPI) | | Protokol | HTTP/2 | HTTP | | Datová část | [Protobuf (malý, binární)](#performance) | JSON (Velká, lidská čitelná) | | Prescriptiveness | [Striktní specifikace](#strict-specification) | Spojování.</span><span class="sxs-lookup"><span data-stu-id="d8702-363">--------------- | | Contract         | Required (*.proto*)                                | Optional (OpenAPI)            | | Protocol         | HTTP/2                                             | HTTP                          | | Payload          | [Protobuf (small, binary)](#performance)           | JSON (large, human readable)  | | Prescriptiveness | [Strict specification](#strict-specification)      | Loose.</span></span> <span data-ttu-id="d8702-364">Všechny požadavky HTTP jsou platné.</span><span class="sxs-lookup"><span data-stu-id="d8702-364">Any HTTP is valid.</span></span>     <span data-ttu-id="d8702-365">| | Streamování | [Klient, server, obousměrný směr](#streaming) | Klient, Server | | Podpora prohlížeče | [Ne (vyžaduje grpc-Web)](#limited-browser-support) | Ano | | Zabezpečení | Transport (TLS) | Transport (TLS) | | Generování kódu klienta | [Ano](#code-generation) | OpenAPI + nástroje třetích stran |</span><span class="sxs-lookup"><span data-stu-id="d8702-365">| | Streaming        | [Client, server, bi-directional](#streaming)       | Client, server                | | Browser support  | [No (requires grpc-web)](#limited-browser-support) | Yes                           | | Security         | Transport (TLS)                                    | Transport (TLS)               | | Client code-generation | [Yes](#code-generation)                      | OpenAPI + third-party tooling |</span></span>

## <a name="grpc-strengths"></a><span data-ttu-id="d8702-366">gRPC síly</span><span class="sxs-lookup"><span data-stu-id="d8702-366">gRPC strengths</span></span>

### <a name="performance"></a><span data-ttu-id="d8702-367">Výkon</span><span class="sxs-lookup"><span data-stu-id="d8702-367">Performance</span></span>

<span data-ttu-id="d8702-368">zprávy gRPC jsou serializovány pomocí [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), efektivního formátu binární zprávy.</span><span class="sxs-lookup"><span data-stu-id="d8702-368">gRPC messages are serialized using [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), an efficient binary message format.</span></span> <span data-ttu-id="d8702-369">Protobuf se na server a klientovi velmi rychle serializovat.</span><span class="sxs-lookup"><span data-stu-id="d8702-369">Protobuf serializes very quickly on the server and client.</span></span> <span data-ttu-id="d8702-370">Protobuf serializace vede k malým datovým částem zprávy, důležité ve scénářích s omezenou šířkou pásma, jako jsou mobilní aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8702-370">Protobuf serialization results in small message payloads, important in limited bandwidth scenarios like mobile apps.</span></span>

<span data-ttu-id="d8702-371">gRPC je navržená pro HTTP/2, což je hlavní revize HTTP, která poskytuje významné výhody výkonu oproti HTTP 1. x:</span><span class="sxs-lookup"><span data-stu-id="d8702-371">gRPC is designed for HTTP/2, a major revision of HTTP that provides significant performance benefits over HTTP 1.x:</span></span>

* <span data-ttu-id="d8702-372">Binární rámce a komprese.</span><span class="sxs-lookup"><span data-stu-id="d8702-372">Binary framing and compression.</span></span> <span data-ttu-id="d8702-373">Protokol HTTP/2 je komprimován a efektivní při posílání i přijímání.</span><span class="sxs-lookup"><span data-stu-id="d8702-373">HTTP/2 protocol is compact and efficient both in sending and receiving.</span></span>
* <span data-ttu-id="d8702-374">Multiplexování více volání HTTP/2 přes jedno připojení TCP.</span><span class="sxs-lookup"><span data-stu-id="d8702-374">Multiplexing of multiple HTTP/2 calls over a single TCP connection.</span></span> <span data-ttu-id="d8702-375">Multiplexování eliminuje [blokování po řádcích](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span><span class="sxs-lookup"><span data-stu-id="d8702-375">Multiplexing eliminates [head-of-line blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span></span>

<span data-ttu-id="d8702-376">HTTP/2 není výhradně pro gRPC.</span><span class="sxs-lookup"><span data-stu-id="d8702-376">HTTP/2 is not exclusive to gRPC.</span></span> <span data-ttu-id="d8702-377">Mnoho typů požadavků, včetně HTTP API s JSON, může používat HTTP/2 a využívat výhody jeho vylepšení výkonu.</span><span class="sxs-lookup"><span data-stu-id="d8702-377">Many request types, including HTTP APIs with JSON, can use HTTP/2 and benefit from its performance improvements.</span></span>

### <a name="code-generation"></a><span data-ttu-id="d8702-378">Generování kódu</span><span class="sxs-lookup"><span data-stu-id="d8702-378">Code generation</span></span>

<span data-ttu-id="d8702-379">Všechny gRPC architektury poskytují prvotřídní podporu pro generování kódu.</span><span class="sxs-lookup"><span data-stu-id="d8702-379">All gRPC frameworks provide first-class support for code generation.</span></span> <span data-ttu-id="d8702-380">Základním souborem pro gRPC vývoj je [soubor...](https://developers.google.com/protocol-buffers/docs/proto3)to definuje kontrakt služeb gRPC a zpráv.</span><span class="sxs-lookup"><span data-stu-id="d8702-380">A core file to gRPC development is the [.proto file](https://developers.google.com/protocol-buffers/docs/proto3), which defines the contract of gRPC services and messages.</span></span> <span data-ttu-id="d8702-381">Z tohoto souboru gRPC architektury budou kód generovat základní třídu služby, zprávy a kompletního klienta.</span><span class="sxs-lookup"><span data-stu-id="d8702-381">From this file gRPC frameworks will code generate a service base class, messages, and a complete client.</span></span>

<span data-ttu-id="d8702-382">Sdílení souboru *.* a klienta mezi serverem a klientem může být vygenerováno z koncového konce.</span><span class="sxs-lookup"><span data-stu-id="d8702-382">By sharing the *.proto* file between the server and client, messages and client code can be generated from end to end.</span></span> <span data-ttu-id="d8702-383">Generování kódu klienta eliminuje duplikaci zpráv na straně klienta a serveru a pro vás vytvoří klienta se silným typem.</span><span class="sxs-lookup"><span data-stu-id="d8702-383">Code generation of the client eliminates duplication of messages on the client and server, and creates a strongly-typed client for you.</span></span> <span data-ttu-id="d8702-384">Nemusíte psát klienta, ale v aplikacích s mnoha službami ušetříte významnou dobu vývoje.</span><span class="sxs-lookup"><span data-stu-id="d8702-384">Not having to write a client saves significant development time in applications with many services.</span></span>

### <a name="strict-specification"></a><span data-ttu-id="d8702-385">Striktní specifikace</span><span class="sxs-lookup"><span data-stu-id="d8702-385">Strict specification</span></span>

<span data-ttu-id="d8702-386">Formální specifikace pro HTTP API s JSON neexistuje.</span><span class="sxs-lookup"><span data-stu-id="d8702-386">A formal specification for HTTP API with JSON doesn't exist.</span></span> <span data-ttu-id="d8702-387">Vývojáři připravují nejlepší formát adres URL, příkazů HTTP a kódů odpovědí.</span><span class="sxs-lookup"><span data-stu-id="d8702-387">Developers debate the best format of URLs, HTTP verbs, and response codes.</span></span>

<span data-ttu-id="d8702-388">[Specifikace gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) je přednastavená na formát, který musí služba gRPC sledovat.</span><span class="sxs-lookup"><span data-stu-id="d8702-388">The [gRPC specification](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) is prescriptive about the format a gRPC service must follow.</span></span> <span data-ttu-id="d8702-389">gRPC eliminuje diskusi a šetří čas vývojáře, protože gRPC je konzistentní napříč platformami a implementacemi.</span><span class="sxs-lookup"><span data-stu-id="d8702-389">gRPC eliminates debate and saves developer time because gRPC is consistent across platforms and implementations.</span></span>

### <a name="streaming"></a><span data-ttu-id="d8702-390">Streamování</span><span class="sxs-lookup"><span data-stu-id="d8702-390">Streaming</span></span>

<span data-ttu-id="d8702-391">HTTP/2 poskytuje základ pro dlouhodobé streamy komunikace v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="d8702-391">HTTP/2 provides a foundation for long-lived, real-time communication streams.</span></span> <span data-ttu-id="d8702-392">gRPC poskytuje prvotřídní podporu pro streamování prostřednictvím HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d8702-392">gRPC provides first-class support for streaming through HTTP/2.</span></span>

<span data-ttu-id="d8702-393">Služba gRPC podporuje všechny kombinace streamování:</span><span class="sxs-lookup"><span data-stu-id="d8702-393">A gRPC service supports all streaming combinations:</span></span>

* <span data-ttu-id="d8702-394">Unární (žádné streamování)</span><span class="sxs-lookup"><span data-stu-id="d8702-394">Unary (no streaming)</span></span>
* <span data-ttu-id="d8702-395">Streamování serveru na klienta</span><span class="sxs-lookup"><span data-stu-id="d8702-395">Server to client streaming</span></span>
* <span data-ttu-id="d8702-396">Streamování klienta na server</span><span class="sxs-lookup"><span data-stu-id="d8702-396">Client to server streaming</span></span>
* <span data-ttu-id="d8702-397">Obousměrné streamování</span><span class="sxs-lookup"><span data-stu-id="d8702-397">Bi-directional streaming</span></span>

### <a name="deadlinetimeouts-and-cancellation"></a><span data-ttu-id="d8702-398">Konečný termín, vypršení časového limitu a zrušení</span><span class="sxs-lookup"><span data-stu-id="d8702-398">Deadline/timeouts and cancellation</span></span>

<span data-ttu-id="d8702-399">gRPC umožňuje klientům určit, jak dlouho mají být ochotni počkat na dokončení vzdáleného volání procedur (RPC).</span><span class="sxs-lookup"><span data-stu-id="d8702-399">gRPC allows clients to specify how long they are willing to wait for an RPC to complete.</span></span> <span data-ttu-id="d8702-400">[Termín](https://grpc.io/blog/deadlines) se pošle na server a server se může rozhodnout, jakou akci chcete provést, pokud překročí konečný termín.</span><span class="sxs-lookup"><span data-stu-id="d8702-400">The [deadline](https://grpc.io/blog/deadlines) is sent to the server, and the server can decide what action to take if it exceeds the deadline.</span></span> <span data-ttu-id="d8702-401">Server může například zrušit probíhající požadavky gRPC/HTTP/Database na vypršení časového limitu.</span><span class="sxs-lookup"><span data-stu-id="d8702-401">For example, the server might cancel in-progress gRPC/HTTP/database requests on timeout.</span></span>

<span data-ttu-id="d8702-402">Rozšiřování konečného termínu a zrušení prostřednictvím podřízených volání gRPC pomáhá vymáhat limity využití prostředků.</span><span class="sxs-lookup"><span data-stu-id="d8702-402">Propagating the deadline and cancellation through child gRPC calls helps enforce resource usage limits.</span></span>

## <a name="grpc-recommended-scenarios"></a><span data-ttu-id="d8702-403">Doporučené scénáře pro gRPC</span><span class="sxs-lookup"><span data-stu-id="d8702-403">gRPC recommended scenarios</span></span>

<span data-ttu-id="d8702-404">gRPC je vhodný pro následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="d8702-404">gRPC is well suited to the following scenarios:</span></span>

* <span data-ttu-id="d8702-405">**Mikroslužby**: gRPC je navržená pro zajištění nízké latence a komunikace s vysokou propustností.</span><span class="sxs-lookup"><span data-stu-id="d8702-405">**Microservices**: gRPC is designed for low latency and high throughput communication.</span></span> <span data-ttu-id="d8702-406">gRPC je ideální pro odlehčené mikroslužby, kde je efektivita nejdůležitější.</span><span class="sxs-lookup"><span data-stu-id="d8702-406">gRPC is great for lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="d8702-407">**Komunikace Point-to-Point v reálném čase**: gRPC má skvělou podporu pro obousměrné streamování.</span><span class="sxs-lookup"><span data-stu-id="d8702-407">**Point-to-point real-time communication**: gRPC has excellent support for bi-directional streaming.</span></span> <span data-ttu-id="d8702-408">služby gRPC mohou nabízet zprávy v reálném čase bez cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="d8702-408">gRPC services can push messages in real-time without polling.</span></span>
* <span data-ttu-id="d8702-409">**Prostředí Polyglot**: nástroje gRPC podporují všechny oblíbené vývojové jazyky, což gRPC vhodnou volbou pro prostředí s více jazyky.</span><span class="sxs-lookup"><span data-stu-id="d8702-409">**Polyglot environments**: gRPC tooling supports all popular development languages, making gRPC a good choice for multi-language environments.</span></span>
* <span data-ttu-id="d8702-410">**Omezená prostředí sítě**: zprávy gRPC jsou serializovány pomocí Protobuf, což je odlehčený formát zprávy.</span><span class="sxs-lookup"><span data-stu-id="d8702-410">**Network constrained environments**: gRPC messages are serialized with Protobuf, a lightweight message format.</span></span> <span data-ttu-id="d8702-411">Zpráva gRPC je vždy menší než ekvivalentní zpráva JSON.</span><span class="sxs-lookup"><span data-stu-id="d8702-411">A gRPC message is always smaller than an equivalent JSON message.</span></span>

## <a name="grpc-weaknesses"></a><span data-ttu-id="d8702-412">gRPC slabé stránky</span><span class="sxs-lookup"><span data-stu-id="d8702-412">gRPC weaknesses</span></span>

### <a name="limited-browser-support"></a><span data-ttu-id="d8702-413">Omezená podpora prohlížeče</span><span class="sxs-lookup"><span data-stu-id="d8702-413">Limited browser support</span></span>

<span data-ttu-id="d8702-414">V dnešní době není možné přímo volat službu gRPC z prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d8702-414">It's impossible to directly call a gRPC service from a browser today.</span></span> <span data-ttu-id="d8702-415">gRPC intenzivně používá funkce HTTP/2 a bez prohlížeče poskytuje úroveň řízení požadovaná pro webové požadavky na podporu klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="d8702-415">gRPC heavily uses HTTP/2 features and no browser provides the level of control required over web requests to support a gRPC client.</span></span> <span data-ttu-id="d8702-416">Například prohlížeče neumožňují volajícímu vyžadovat, aby byl použit HTTP/2 nebo poskytoval přístup k podkladovým rámcům HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d8702-416">For example, browsers do not allow a caller to require that HTTP/2 be used, or provide access to underlying HTTP/2 frames.</span></span>

<span data-ttu-id="d8702-417">[gRPC-web](https://grpc.io/docs/tutorials/basic/web.html) je doplňková technologie od týmu gRPC, která poskytuje omezené podpory gRPC v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="d8702-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) is an additional technology from the gRPC team that provides limited gRPC support in the browser.</span></span> <span data-ttu-id="d8702-418">gRPC-web se skládá ze dvou částí: JavaScriptový klient, který podporuje všechny moderní prohlížeče, a webový proxy server gRPC na serveru.</span><span class="sxs-lookup"><span data-stu-id="d8702-418">gRPC-Web consists of two parts: a JavaScript client that supports all modern browsers, and a gRPC-Web proxy on the server.</span></span> <span data-ttu-id="d8702-419">GRPC – webový klient volá proxy server a proxy server přepošle žádosti gRPC na server gRPC.</span><span class="sxs-lookup"><span data-stu-id="d8702-419">The gRPC-Web client calls the proxy and the proxy will forward on the gRPC requests to the gRPC server.</span></span>

<span data-ttu-id="d8702-420">GRPC-web nepodporuje všechny funkce gRPC.</span><span class="sxs-lookup"><span data-stu-id="d8702-420">Not all of gRPC's features are supported by gRPC-Web.</span></span> <span data-ttu-id="d8702-421">Klient a obousměrné streamování se nepodporuje a je omezená podpora pro streamování serveru.</span><span class="sxs-lookup"><span data-stu-id="d8702-421">Client and bi-directional streaming isn't supported, and there is limited support for server streaming.</span></span>

> [!TIP]
> <span data-ttu-id="d8702-422">.NET Core má experimentální podporu pro gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="d8702-422">.NET Core has experimental support for gRPC-Web.</span></span> <span data-ttu-id="d8702-423">Další informace najdete na webu <xref:grpc/browser> .</span><span class="sxs-lookup"><span data-stu-id="d8702-423">Visit <xref:grpc/browser> for more information.</span></span>

### <a name="not-human-readable"></a><span data-ttu-id="d8702-424">Nečitelný člověk</span><span class="sxs-lookup"><span data-stu-id="d8702-424">Not human readable</span></span>

<span data-ttu-id="d8702-425">Požadavky HTTP API se odesílají jako text a můžou je číst a vytvářet člověkem.</span><span class="sxs-lookup"><span data-stu-id="d8702-425">HTTP API requests are sent as text and can be read and created by humans.</span></span>

<span data-ttu-id="d8702-426">zprávy gRPC jsou ve výchozím nastavení kódované pomocí Protobuf.</span><span class="sxs-lookup"><span data-stu-id="d8702-426">gRPC messages are encoded with Protobuf by default.</span></span> <span data-ttu-id="d8702-427">I když je Protobuf efektivní pro posílání a přijímání, jeho binární formát není čitelný lidmi.</span><span class="sxs-lookup"><span data-stu-id="d8702-427">While Protobuf is efficient to send and receive, its binary format isn't human readable.</span></span> <span data-ttu-id="d8702-428">Protobuf vyžaduje, aby byl popis rozhraní zprávy zadaný v souboru *.* proč správně deserializován.</span><span class="sxs-lookup"><span data-stu-id="d8702-428">Protobuf requires the message's interface description specified in the *.proto* file to properly deserialize.</span></span> <span data-ttu-id="d8702-429">K analýze datových částí Protobuf na lince a k vytváření požadavků rukou je potřeba další nástroje.</span><span class="sxs-lookup"><span data-stu-id="d8702-429">Additional tooling is required to analyze Protobuf payloads on the wire and to compose requests by hand.</span></span>

<span data-ttu-id="d8702-430">Funkce jako [reflexe serveru](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) a [Nástroj příkazového řádku gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) existují pro pomoc s binárními Protobuf zprávami.</span><span class="sxs-lookup"><span data-stu-id="d8702-430">Features such as [server reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) and the [gRPC command line tool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) exist to assist with binary Protobuf messages.</span></span> <span data-ttu-id="d8702-431">Také zprávy Protobuf podporují [Převod do formátu JSON a z](https://developers.google.com/protocol-buffers/docs/proto3#json)něj.</span><span class="sxs-lookup"><span data-stu-id="d8702-431">Also, Protobuf messages support [conversion to and from JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span></span> <span data-ttu-id="d8702-432">Integrovaný převod JSON poskytuje efektivní způsob, jak převést zprávy Protobuf do nebo z lidského čitelného formuláře při ladění.</span><span class="sxs-lookup"><span data-stu-id="d8702-432">The built-in JSON conversion provides an efficient way to convert Protobuf messages to and from human readable form when debugging.</span></span>

## <a name="alternative-framework-scenarios"></a><span data-ttu-id="d8702-433">Scénáře alternativních platforem</span><span class="sxs-lookup"><span data-stu-id="d8702-433">Alternative framework scenarios</span></span>

<span data-ttu-id="d8702-434">Další architektury se doporučují přes gRPC v následujících scénářích:</span><span class="sxs-lookup"><span data-stu-id="d8702-434">Other frameworks are recommended over gRPC in the following scenarios:</span></span>

* <span data-ttu-id="d8702-435">**Rozhraní API pro přístup přes prohlížeč**: v prohlížeči se nepodporuje plně gRPC.</span><span class="sxs-lookup"><span data-stu-id="d8702-435">**Browser accessible APIs**: gRPC isn't fully supported in the browser.</span></span> <span data-ttu-id="d8702-436">gRPC-web může nabízet podporu prohlížeče, ale má omezení a zavádí proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="d8702-436">gRPC-Web can offer browser support, but it has limitations and introduces a server proxy.</span></span>
* <span data-ttu-id="d8702-437">**Komunikace v reálném čase**: gRPC podporuje komunikaci v reálném čase prostřednictvím streamování, ale koncept vysílání zprávy do registrovaných připojení neexistuje.</span><span class="sxs-lookup"><span data-stu-id="d8702-437">**Broadcast real-time communication**: gRPC supports real-time communication via streaming, but the concept of broadcasting a message out to registered connections doesn't exist.</span></span> <span data-ttu-id="d8702-438">Například ve scénáři chatovací místnosti, kde mají být nové zprávy chatu odesílány všem klientům v chatovací místnosti, každé volání gRPC je požadováno k samostatnému streamování nových zpráv o konverzaci do klienta.</span><span class="sxs-lookup"><span data-stu-id="d8702-438">For example in a chat room scenario where new chat messages should be sent to all clients in the chat room, each gRPC call is required to individually stream new chat messages to the client.</span></span> <span data-ttu-id="d8702-439">[SignalR](xref:signalr/introduction)je užitečnou architekturou pro tento scénář.</span><span class="sxs-lookup"><span data-stu-id="d8702-439">[SignalR](xref:signalr/introduction) is a useful framework for this scenario.</span></span> SignalR<span data-ttu-id="d8702-440">má koncept trvalých připojení a integrovanou podporu pro vysílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="d8702-440"> has the concept of persistent connections and built-in support for broadcasting messages.</span></span>
* <span data-ttu-id="d8702-441">**Komunikace mezi procesy**: proces musí HOSTOVAT Server HTTP/2, aby přijímal příchozí volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="d8702-441">**Inter-process communication**: A process must host an HTTP/2 server to accept incoming gRPC calls.</span></span> <span data-ttu-id="d8702-442">Pro systém Windows je mezi procesy komunikačních [kanálů](/dotnet/standard/io/pipe-operations) rychlá a odlehčená metoda komunikace.</span><span class="sxs-lookup"><span data-stu-id="d8702-442">For Windows, inter-process communication [pipes](/dotnet/standard/io/pipe-operations) is a fast, lightweight method of communication.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8702-443">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d8702-443">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
