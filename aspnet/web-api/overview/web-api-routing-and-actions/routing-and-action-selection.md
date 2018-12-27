---
uid: web-api/overview/web-api-routing-and-actions/routing-and-action-selection
title: Směrování a výběr akce v rozhraní ASP.NET Web API | Dokumentace Microsoftu
author: MikeWasson
description: ''
ms.author: riande
ms.date: 12/14/2018
ms.assetid: bcf2d223-cb7f-411e-be05-f43e96a14015
msc.legacyurl: /web-api/overview/web-api-routing-and-actions/routing-and-action-selection
msc.type: authoredcontent
ms.openlocfilehash: ce54181996376cb5dde3b91c10c16f33b3c6a570
ms.sourcegitcommit: 6548c19f345850ee22b50f7ef9fca732895d9e08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53425169"
---
<a name="routing-and-action-selection-in-aspnet-web-api"></a><span data-ttu-id="c4cd4-102">Směrování a výběr akce v rozhraní ASP.NET Web API</span><span class="sxs-lookup"><span data-stu-id="c4cd4-102">Routing and Action Selection in ASP.NET Web API</span></span>
====================
<span data-ttu-id="c4cd4-103">podle [Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="c4cd4-103">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

<span data-ttu-id="c4cd4-104">Tento článek popisuje, jak rozhraní ASP.NET Web API směruje požadavek HTTP na provedení určité akce v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-104">This article describes how ASP.NET Web API routes an HTTP request to a particular action on a controller.</span></span>

> [!NOTE]
> <span data-ttu-id="c4cd4-105">Přehled směrování, naleznete v tématu [směrování v rozhraní ASP.NET Web API](routing-in-aspnet-web-api.md).</span><span class="sxs-lookup"><span data-stu-id="c4cd4-105">For a high-level overview of routing, see [Routing in ASP.NET Web API](routing-in-aspnet-web-api.md).</span></span>


<span data-ttu-id="c4cd4-106">Tento článek ukazuje podrobnosti o procesu směrování.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-106">This article looks at the details of the routing process.</span></span> <span data-ttu-id="c4cd4-107">Pokud vytváříte projekt webového rozhraní API a zjistíte, že některé požadavky Nezískávat směrovány očekávaným způsobem, snad Tento článek vám pomůže.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-107">If you create a Web API project and find that some requests don't get routed the way you expect, hopefully this article will help.</span></span>

<span data-ttu-id="c4cd4-108">Směrování má tři hlavní fáze:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-108">Routing has three main phases:</span></span>

1. <span data-ttu-id="c4cd4-109">Shodujícím se identifikátoru URI pro šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-109">Matching the URI to a route template.</span></span>
2. <span data-ttu-id="c4cd4-110">Výběr kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-110">Selecting a controller.</span></span>
3. <span data-ttu-id="c4cd4-111">Výběr akce.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-111">Selecting an action.</span></span>

<span data-ttu-id="c4cd4-112">Některé části procesu můžete nahradit vlastní vlastní chování.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-112">You can replace some parts of the process with your own custom behaviors.</span></span> <span data-ttu-id="c4cd4-113">V tomto článku popisují I výchozí chování.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-113">In this article, I describe the default behavior.</span></span> <span data-ttu-id="c4cd4-114">Na konci můžu mějte na paměti na místech, kde můžete přizpůsobit chování.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-114">At the end, I note the places where you can customize the behavior.</span></span>

## <a name="route-templates"></a><span data-ttu-id="c4cd4-115">Šablony trasy</span><span class="sxs-lookup"><span data-stu-id="c4cd4-115">Route Templates</span></span>

<span data-ttu-id="c4cd4-116">Šablona trasy vypadá podobně jako na cestu URI, ale může mít hodnoty zástupných symbolů se složených závorek:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-116">A route template looks similar to a URI path, but it can have placeholder values, indicated with curly braces:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample1.ps1)]

<span data-ttu-id="c4cd4-117">Při vytváření trasy můžete zadat výchozí hodnoty pro některé nebo všechny zástupné symboly:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-117">When you create a route, you can provide default values for some or all of the placeholders:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample2.cs)]

<span data-ttu-id="c4cd4-118">Můžete také zadat omezení, které omezují jak segment identifikátoru URI může odpovídat zástupný symbol:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-118">You can also provide constraints, which restrict how a URI segment can match a placeholder:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample3.js)]

<span data-ttu-id="c4cd4-119">Rozhraní se pokusí vyhledat segmentů v cestě identifikátor URI v šabloně.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-119">The framework tries to match the segments in the URI path to the template.</span></span> <span data-ttu-id="c4cd4-120">Literály v šabloně musí přesně odpovídat.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-120">Literals in the template must match exactly.</span></span> <span data-ttu-id="c4cd4-121">Zástupný text odpovídá libovolné hodnotě, není-li zadat omezení.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-121">A placeholder matches any value, unless you specify constraints.</span></span> <span data-ttu-id="c4cd4-122">Rozhraní se neshoduje s jiné části identifikátoru URI, jako je název hostitele nebo parametry dotazu.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-122">The framework does not match other parts of the URI, such as the host name or the query parameters.</span></span> <span data-ttu-id="c4cd4-123">Rozhraní framework vybere první trasy ve směrovací tabulce, která odpovídá identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-123">The framework selects the first route in the route table that matches the URI.</span></span>

<span data-ttu-id="c4cd4-124">Existují dva speciální zástupné znaky: "{controller}" a "{action}".</span><span class="sxs-lookup"><span data-stu-id="c4cd4-124">There are two special placeholders: "{controller}" and "{action}".</span></span>

- <span data-ttu-id="c4cd4-125">"{controller}" obsahuje název kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-125">"{controller}" provides the name of the controller.</span></span>
- <span data-ttu-id="c4cd4-126">"{action} obsahuje název akce.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-126">"{action}" provides the name of the action.</span></span> <span data-ttu-id="c4cd4-127">V rozhraní Web API je "{action} vynechejte běžné konvence.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-127">In Web API, the usual convention is to omit "{action}".</span></span>

### <a name="defaults"></a><span data-ttu-id="c4cd4-128">Ve výchozím nastavení</span><span class="sxs-lookup"><span data-stu-id="c4cd4-128">Defaults</span></span>

<span data-ttu-id="c4cd4-129">Pokud zadáte výchozí hodnoty, bude odpovídat trasy identifikátor URI, který neobsahuje tyto segmenty.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-129">If you provide defaults, the route will match a URI that is missing those segments.</span></span> <span data-ttu-id="c4cd4-130">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-130">For example:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample4.cs)]

<span data-ttu-id="c4cd4-131">Identifikátory URI `http://localhost/api/products/all` a `http://localhost/api/products` odpovídají předchozím postupu.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-131">The URIs `http://localhost/api/products/all` and `http://localhost/api/products` match the preceding route.</span></span> <span data-ttu-id="c4cd4-132">V druhém URI chybějící `{category}` segmentu je přiřazen výchozí hodnota `all`.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-132">In the latter URI, the missing `{category}` segment is assigned the default value `all`.</span></span>

### <a name="route-dictionary"></a><span data-ttu-id="c4cd4-133">Slovníku trasy</span><span class="sxs-lookup"><span data-stu-id="c4cd4-133">Route Dictionary</span></span>

<span data-ttu-id="c4cd4-134">Pokud rozhraní najde shodu pro identifikátor URI, vytvoří slovník, který obsahuje hodnotu pro každý zástupný symbol.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-134">If the framework finds a match for a URI, it creates a dictionary that contains the value for each placeholder.</span></span> <span data-ttu-id="c4cd4-135">Klíče jsou zástupné názvy, bez složených závorek.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-135">The keys are the placeholder names, not including the curly braces.</span></span> <span data-ttu-id="c4cd4-136">Hodnoty pocházejí z cesty identifikátoru URI nebo z výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-136">The values are taken from the URI path or from the defaults.</span></span> <span data-ttu-id="c4cd4-137">Slovník je uložen v **IHttpRouteData** objektu.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-137">The dictionary is stored in the **IHttpRouteData** object.</span></span>

<span data-ttu-id="c4cd4-138">V této fázi odpovídající trasy speciální "{controller}" a "{action} zástupné symboly zachází stejně jako jiné zástupné symboly.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-138">During this route-matching phase, the special "{controller}" and "{action}" placeholders are treated just like the other placeholders.</span></span> <span data-ttu-id="c4cd4-139">Jednoduše se ukládají ve slovníku s jiné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-139">They are simply stored in the dictionary with the other values.</span></span>

<span data-ttu-id="c4cd4-140">Výchozí může mít zvláštní hodnota **RouteParameter.Optional**.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-140">A default can have the special value **RouteParameter.Optional**.</span></span> <span data-ttu-id="c4cd4-141">Pokud zástupný symbol získá přiřadí tuto hodnotu, hodnota není přidán do slovníku trasy.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-141">If a placeholder gets assigned this value, the value is not added to the route dictionary.</span></span> <span data-ttu-id="c4cd4-142">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-142">For example:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample5.cs)]

<span data-ttu-id="c4cd4-143">Pro identifikátor URI cesty "/ produktů s rozhraním api" bude obsahovat slovníku trasy:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-143">For the URI path "api/products", the route dictionary will contain:</span></span>

- <span data-ttu-id="c4cd4-144">kontroler: "produktů"</span><span class="sxs-lookup"><span data-stu-id="c4cd4-144">controller: "products"</span></span>
- <span data-ttu-id="c4cd4-145">kategorie: "vše"</span><span class="sxs-lookup"><span data-stu-id="c4cd4-145">category: "all"</span></span>

<span data-ttu-id="c4cd4-146">"Api/produkty/toys/123" ale slovníku trasy obsahovat:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-146">For "api/products/toys/123", however, the route dictionary will contain:</span></span>

- <span data-ttu-id="c4cd4-147">kontroler: "produktů"</span><span class="sxs-lookup"><span data-stu-id="c4cd4-147">controller: "products"</span></span>
- <span data-ttu-id="c4cd4-148">kategorie: "toys"</span><span class="sxs-lookup"><span data-stu-id="c4cd4-148">category: "toys"</span></span>
- <span data-ttu-id="c4cd4-149">ID: "123"</span><span class="sxs-lookup"><span data-stu-id="c4cd4-149">id: "123"</span></span>

<span data-ttu-id="c4cd4-150">Výchozí hodnoty může také obsahovat hodnotu, která se nezobrazí kdekoli v šabloně trasy.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-150">The defaults can also include a value that does not appear anywhere in the route template.</span></span> <span data-ttu-id="c4cd4-151">Pokud trasa odpovídá, tato hodnota je uložena ve slovníku.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-151">If the route matches, that value is stored in the dictionary.</span></span> <span data-ttu-id="c4cd4-152">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-152">For example:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample6.cs)]

<span data-ttu-id="c4cd4-153">Pokud cesta k identifikátoru URI je "rozhraní api/root/8", slovníku obsahovat dvě hodnoty:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-153">If the URI path is "api/root/8", the dictionary will contain two values:</span></span>

- <span data-ttu-id="c4cd4-154">kontroler: "zákazníci"</span><span class="sxs-lookup"><span data-stu-id="c4cd4-154">controller: "customers"</span></span>
- <span data-ttu-id="c4cd4-155">ID: "8"</span><span class="sxs-lookup"><span data-stu-id="c4cd4-155">id: "8"</span></span>

## <a name="selecting-a-controller"></a><span data-ttu-id="c4cd4-156">Vyberte kontroler</span><span class="sxs-lookup"><span data-stu-id="c4cd4-156">Selecting a Controller</span></span>

<span data-ttu-id="c4cd4-157">Výběr řadiče se zpracovává souborem **IHttpControllerSelector.SelectController** metody.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-157">Controller selection is handled by the **IHttpControllerSelector.SelectController** method.</span></span> <span data-ttu-id="c4cd4-158">Tato metoda přebírá **HttpRequestMessage** instance a vrátí **HttpControllerDescriptor**.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-158">This method takes an **HttpRequestMessage** instance and returns an **HttpControllerDescriptor**.</span></span> <span data-ttu-id="c4cd4-159">Poskytuje výchozí implementaci **DefaultHttpControllerSelector** třídy.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-159">The default implementation is provided by the **DefaultHttpControllerSelector** class.</span></span> <span data-ttu-id="c4cd4-160">Tato třída používá jednoduché algoritmus:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-160">This class uses a straightforward algorithm:</span></span>

1. <span data-ttu-id="c4cd4-161">Podívejte se ve slovníku trasy pro klíč "controller".</span><span class="sxs-lookup"><span data-stu-id="c4cd4-161">Look in the route dictionary for the key "controller".</span></span>
2. <span data-ttu-id="c4cd4-162">Přijmout hodnotu pro tento klíč a připojte řetězec "Controller" získat tak název typu kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-162">Take the value for this key and append the string "Controller" to get the controller type name.</span></span>
3. <span data-ttu-id="c4cd4-163">Vyhledejte kontroler Web API s tímto názvem typu.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-163">Look for a Web API controller with this type name.</span></span>

<span data-ttu-id="c4cd4-164">Například pokud slovníku trasy obsahuje pár klíč hodnota "controller" = "produktů", "ProductsController" je typ kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-164">For example, if the route dictionary contains the key-value pair "controller" = "products", then the controller type is "ProductsController".</span></span> <span data-ttu-id="c4cd4-165">Pokud neexistuje žádný odpovídající typ nebo více shod, rozhraní chybovou zprávu do klienta.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-165">If there is no matching type, or multiple matches, the framework returns an error to the client.</span></span>

<span data-ttu-id="c4cd4-166">Krok 3 **DefaultHttpControllerSelector** používá **IHttpControllerTypeResolver** rozhraní se získat seznam typy kontrolerů webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-166">For step 3, **DefaultHttpControllerSelector** uses the **IHttpControllerTypeResolver** interface to get the list of Web API controller types.</span></span> <span data-ttu-id="c4cd4-167">Výchozí implementace **IHttpControllerTypeResolver** vrátí všechny veřejné třídy, které implementují (a) **IHttpController**, (b) není abstraktní a (c) mít název, který končí na "Controller".</span><span class="sxs-lookup"><span data-stu-id="c4cd4-167">The default implementation of **IHttpControllerTypeResolver** returns all public classes that (a) implement **IHttpController**, (b) are not abstract, and (c) have a name that ends in "Controller".</span></span>

## <a name="action-selection"></a><span data-ttu-id="c4cd4-168">Výběr akce</span><span class="sxs-lookup"><span data-stu-id="c4cd4-168">Action Selection</span></span>

<span data-ttu-id="c4cd4-169">Po výběru kontroler rozhraní vybere akci voláním **IHttpActionSelector.SelectAction** metody.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-169">After selecting the controller, the framework selects the action by calling the **IHttpActionSelector.SelectAction** method.</span></span> <span data-ttu-id="c4cd4-170">Tato metoda přebírá **HttpControllerContext** a vrátí **HttpActionDescriptor**.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-170">This method takes an **HttpControllerContext** and returns an **HttpActionDescriptor**.</span></span>

<span data-ttu-id="c4cd4-171">Poskytuje výchozí implementaci **ApiControllerActionSelector** třídy.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-171">The default implementation is provided by the **ApiControllerActionSelector** class.</span></span> <span data-ttu-id="c4cd4-172">Pokud chcete vybrat akci, dohlíží na následující:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-172">To select an action, it looks at the following:</span></span>

- <span data-ttu-id="c4cd4-173">Metoda HTTP požadavku.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-173">The HTTP method of the request.</span></span>
- <span data-ttu-id="c4cd4-174">Zástupný text "{action}" v šabloně trasy, pokud jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-174">The "{action}" placeholder in the route template, if present.</span></span>
- <span data-ttu-id="c4cd4-175">Parametry akce v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-175">The parameters of the actions on the controller.</span></span>

<span data-ttu-id="c4cd4-176">Před zobrazením algoritmus výběru, Nejdřív musíte seznámit pár věcí o akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-176">Before looking at the selection algorithm, we need to understand some things about controller actions.</span></span>

<span data-ttu-id="c4cd4-177">**Které metody na řadiči, jsou považovány za "akce"?**</span><span class="sxs-lookup"><span data-stu-id="c4cd4-177">**Which methods on the controller are considered "actions"?**</span></span> <span data-ttu-id="c4cd4-178">Když vyberete akci, rozhraní pouze prohledá veřejné instanci metody ve kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-178">When selecting an action, the framework only looks at public instance methods on the controller.</span></span> <span data-ttu-id="c4cd4-179">Také, vyloučí ["speciální název"](https://msdn.microsoft.com/library/system.reflection.methodbase.isspecialname) metod (konstruktorů, události, přetížení operátoru a tak dále) a metod zděděných z **objektu ApiController** třídy.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-179">Also, it excludes ["special name"](https://msdn.microsoft.com/library/system.reflection.methodbase.isspecialname) methods (constructors, events, operator overloads, and so forth), and methods inherited from the **ApiController** class.</span></span>

<span data-ttu-id="c4cd4-180">**Metody HTTP.**</span><span class="sxs-lookup"><span data-stu-id="c4cd4-180">**HTTP Methods.**</span></span> <span data-ttu-id="c4cd4-181">Rozhraní framework vybírá pouze akce, které odpovídají metoda HTTP požadavku stanoven následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-181">The framework only chooses actions that match the HTTP method of the request, determined as follows:</span></span>

1. <span data-ttu-id="c4cd4-182">Můžete určit metodu HTTP s atributem: **AcceptVerbs**, **HttpDelete**, **HttpGet**, **HttpHead**, **httpoptions měl**, **HttpPatch**, **HttpPost**, nebo **HttpPut**.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-182">You can specify the HTTP method with an attribute: **AcceptVerbs**, **HttpDelete**, **HttpGet**, **HttpHead**, **HttpOptions**, **HttpPatch**, **HttpPost**, or **HttpPut**.</span></span>
2. <span data-ttu-id="c4cd4-183">Jinak Pokud název metody kontroleru začíná řetězcem "Get", "Post", "Vložit", "Odstranit", "Head", "Options" nebo "Opravnou", pak podle konvence akci podporuje metody HTTP.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-183">Otherwise, if the name of the controller method starts with "Get", "Post", "Put", "Delete", "Head", "Options", or "Patch", then by convention the action supports that HTTP method.</span></span>
3. <span data-ttu-id="c4cd4-184">Pokud žádná z výše uvedených podporuje metodu POST.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-184">If none of the above, the method supports POST.</span></span>

<span data-ttu-id="c4cd4-185">**Vazby parametru.**</span><span class="sxs-lookup"><span data-stu-id="c4cd4-185">**Parameter Bindings.**</span></span> <span data-ttu-id="c4cd4-186">Vazby parametru je způsob, jak vytvořit hodnotu pro parametr webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-186">A parameter binding is how Web API creates a value for a parameter.</span></span> <span data-ttu-id="c4cd4-187">Tady je výchozí pravidlo pro vazbu parametru:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-187">Here is the default rule for parameter binding:</span></span>

- <span data-ttu-id="c4cd4-188">Jednoduché typy pocházejí z identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-188">Simple types are taken from the URI.</span></span>
- <span data-ttu-id="c4cd4-189">Komplexní typy pocházejí z textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-189">Complex types are taken from the request body.</span></span>

<span data-ttu-id="c4cd4-190">Jednoduché typy zahrnují všechny [primitivní typy rozhraní .NET Framework](https://msdn.microsoft.com/library/system.type.isprimitive), plus **data a času**, **desítkové**, **Guid**, **řetězec** , a **TimeSpan**.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-190">Simple types include all of the [.NET Framework primitive types](https://msdn.microsoft.com/library/system.type.isprimitive), plus **DateTime**, **Decimal**, **Guid**, **String**, and **TimeSpan**.</span></span> <span data-ttu-id="c4cd4-191">Pro každou akci můžete přečíst maximálně jeden parametr těla požadavku.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-191">For each action, at most one parameter can read the request body.</span></span>

> [!NOTE]
> <span data-ttu-id="c4cd4-192">Je možné přepsat výchozí pravidla pro vazbu.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-192">It is possible to override the default binding rules.</span></span> <span data-ttu-id="c4cd4-193">Zobrazit [vazbu parametru WebAPI pod pokličkou](https://blogs.msdn.com/b/jmstall/archive/2012/05/11/webapi-parameter-binding-under-the-hood.aspx).</span><span class="sxs-lookup"><span data-stu-id="c4cd4-193">See [WebAPI Parameter binding under the hood](https://blogs.msdn.com/b/jmstall/archive/2012/05/11/webapi-parameter-binding-under-the-hood.aspx).</span></span>


<span data-ttu-id="c4cd4-194">Tedy tady je výběr algoritmu akce.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-194">With that background, here is the action selection algorithm.</span></span>

1. <span data-ttu-id="c4cd4-195">Vytvoří seznam všech akcí na řadiči, které odpovídají metoda požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-195">Create a list of all actions on the controller that match the HTTP request method.</span></span>
2. <span data-ttu-id="c4cd4-196">Pokud slovníku trasy má záznam "action", odeberte akce, jejíž název se neshoduje s Tato hodnota.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-196">If the route dictionary has an "action" entry, remove actions whose name does not match this value.</span></span>
3. <span data-ttu-id="c4cd4-197">Vyzkoušejte tak, aby odpovídaly parametry akce na identifikátor URI, následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-197">Try to match action parameters to the URI, as follows:</span></span> 

    1. <span data-ttu-id="c4cd4-198">Pro každou akci Získejte seznam parametrů, které jsou jednoduchý typ, pokud vazba získá parametr z identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-198">For each action, get a list of the parameters that are a simple type, where the binding gets the parameter from the URI.</span></span> <span data-ttu-id="c4cd4-199">Vylučte volitelné parametry.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-199">Exclude optional parameters.</span></span>
    2. <span data-ttu-id="c4cd4-200">Z tohoto seznamu pokuste se najít shodu pro každý název parametru ve slovníku trasy nebo v řetězci dotazu identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-200">From this list, try to find a match for each parameter name, either in the route dictionary or in the URI query string.</span></span> <span data-ttu-id="c4cd4-201">Shody jsou malá a velká písmena a nezávisí na pořadí parametrů.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-201">Matches are case insensitive and do not depend on the parameter order.</span></span>
    3. <span data-ttu-id="c4cd4-202">Vyberte akci, kde každý parametr v seznamu existuje shoda v identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-202">Select an action where every parameter in the list has a match in the URI.</span></span>
    4. <span data-ttu-id="c4cd4-203">Pokud více tuto jednu akci se bude splňovat tyto podmínky, vyberte si jednu s nejvíce odpovídá parametru.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-203">If more that one action meets these criteria, pick the one with the most parameter matches.</span></span>
4. <span data-ttu-id="c4cd4-204">Ignorovat akce s **[NonAction]** atribut.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-204">Ignore actions with the **[NonAction]** attribute.</span></span>

<span data-ttu-id="c4cd4-205">Krok #3 je pravděpodobně matoucí nejvíce.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-205">Step #3 is probably the most confusing.</span></span> <span data-ttu-id="c4cd4-206">Základní myšlenka je, že parametr dostat jeho hodnotu z identifikátoru URI, z textu požadavku nebo z vlastní vazby.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-206">The basic idea is that a parameter can get its value either from the URI, from the request body, or from a custom binding.</span></span> <span data-ttu-id="c4cd4-207">Pro parametry, které pochází z identifikátoru URI chceme se ujistit, že identifikátor URI ve skutečnosti obsahuje hodnotu pro tento parametr, a to buď v cestě (pomocí slovníku trasy), nebo v řetězci dotazu.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-207">For parameters that come from the URI, we want to ensure that the URI actually contains a value for that parameter, either in the path (via the route dictionary) or in the query string.</span></span>

<span data-ttu-id="c4cd4-208">Představte si třeba následující akce:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-208">For example, consider the following action:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample7.cs)]

<span data-ttu-id="c4cd4-209">*Id* vazby parametru na identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-209">The *id* parameter binds to the URI.</span></span> <span data-ttu-id="c4cd4-210">Proto tato akce může odpovídat pouze identifikátor URI, který obsahuje hodnotu pro "id" ve slovníku trasy nebo v řetězci dotazu.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-210">Therefore, this action can only match a URI that contains a value for "id", either in the route dictionary or in the query string.</span></span>

<span data-ttu-id="c4cd4-211">Volitelné parametry jsou výjimku, protože to jsou volitelné.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-211">Optional parameters are an exception, because they are optional.</span></span> <span data-ttu-id="c4cd4-212">Pro volitelný parametr je OK, pokud vazby nelze získat hodnotu z identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-212">For an optional parameter, it's OK if the binding can't get the value from the URI.</span></span>

<span data-ttu-id="c4cd4-213">Komplexní typy jsou výjimky pro jiný důvod.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-213">Complex types are an exception for a different reason.</span></span> <span data-ttu-id="c4cd4-214">Komplexní typ může být svázán pouze k identifikátoru URI prostřednictvím vlastní vazby.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-214">A complex type can only bind to the URI through a custom binding.</span></span> <span data-ttu-id="c4cd4-215">Ale v takovém případě rozhraní nemůže předem vědět, jestli parametr by svázat s konkrétní identifikátorem URI.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-215">But in that case, the framework cannot know in advance whether the parameter would bind to a particular URI.</span></span> <span data-ttu-id="c4cd4-216">Pokud chcete zjistit, je nutné vyvolat vazby.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-216">To find out, it would need to invoke the binding.</span></span> <span data-ttu-id="c4cd4-217">Cílem algoritmus výběru je ze statické popis před vyvoláním žádné vazby. Vyberte akci.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-217">The goal of the selection algorithm is to select an action from the static description, before invoking any bindings.</span></span> <span data-ttu-id="c4cd4-218">Proto komplexní typy jsou vyloučeny z porovnávací algoritmus.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-218">Therefore, complex types are excluded from the matching algorithm.</span></span>

<span data-ttu-id="c4cd4-219">Po výběru akce jsou vyvolány všechny vazby parametru.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-219">After the action is selected, all parameter bindings are invoked.</span></span>

<span data-ttu-id="c4cd4-220">Shrnutí:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-220">Summary:</span></span>

- <span data-ttu-id="c4cd4-221">Metoda HTTP požadavku musí odpovídat akce.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-221">The action must match the HTTP method of the request.</span></span>
- <span data-ttu-id="c4cd4-222">Název akce musí odpovídat "action" položky ve slovníku trasy, pokud jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-222">The action name must match the "action" entry in the route dictionary, if present.</span></span>
- <span data-ttu-id="c4cd4-223">Pro každý parametr akce Pokud tento parametr je převzata z identifikátoru URI, pak název parametru musí najít ve slovníku trasy nebo v řetězci dotazu identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-223">For every parameter of the action, if the parameter is taken from the URI, then the parameter name must be found either in the route dictionary or in the URI query string.</span></span> <span data-ttu-id="c4cd4-224">(Volitelné parametry a parametry s komplexní typy jsou vyloučeny.)</span><span class="sxs-lookup"><span data-stu-id="c4cd4-224">(Optional parameters and parameters with complex types are excluded.)</span></span>
- <span data-ttu-id="c4cd4-225">Pokuste se shodovat s největším počtem parametrů.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-225">Try to match the most number of parameters.</span></span> <span data-ttu-id="c4cd4-226">Nejlepší shody může být metoda bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-226">The best match might be a method with no parameters.</span></span>

## <a name="extended-example"></a><span data-ttu-id="c4cd4-227">Rozšířený příklad</span><span class="sxs-lookup"><span data-stu-id="c4cd4-227">Extended Example</span></span>

<span data-ttu-id="c4cd4-228">Trasy:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-228">Routes:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample8.cs)]

<span data-ttu-id="c4cd4-229">Kontroler:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-229">Controller:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample9.cs)]

<span data-ttu-id="c4cd4-230">Požadavek protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-230">HTTP request:</span></span>

[!code-console[Main](routing-and-action-selection/samples/sample10.cmd)]

### <a name="route-matching"></a><span data-ttu-id="c4cd4-231">Odpovídající trasy</span><span class="sxs-lookup"><span data-stu-id="c4cd4-231">Route Matching</span></span>

<span data-ttu-id="c4cd4-232">Identifikátor URI odpovídá trasa s názvem "DefaultApi".</span><span class="sxs-lookup"><span data-stu-id="c4cd4-232">The URI matches the route named "DefaultApi".</span></span> <span data-ttu-id="c4cd4-233">Slovníku trasy obsahuje následující položky:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-233">The route dictionary contains the following entries:</span></span>

- <span data-ttu-id="c4cd4-234">kontroler: "produktů"</span><span class="sxs-lookup"><span data-stu-id="c4cd4-234">controller: "products"</span></span>
- <span data-ttu-id="c4cd4-235">ID: "1"</span><span class="sxs-lookup"><span data-stu-id="c4cd4-235">id: "1"</span></span>

<span data-ttu-id="c4cd4-236">Slovníku trasy neobsahuje parametry řetězce dotazu, "verze" a "details", ale ty budou mít nadále za během výběr akce.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-236">The route dictionary does not contain the query string parameters, "version" and "details", but these will still be considered during action selection.</span></span>

### <a name="controller-selection"></a><span data-ttu-id="c4cd4-237">Výběr řadiče</span><span class="sxs-lookup"><span data-stu-id="c4cd4-237">Controller Selection</span></span>

<span data-ttu-id="c4cd4-238">Z položky "controller" ve slovníku trasy, je typ kontroleru `ProductsController`.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-238">From the "controller" entry in the route dictionary, the controller type is `ProductsController`.</span></span>

### <a name="action-selection"></a><span data-ttu-id="c4cd4-239">Výběr akce</span><span class="sxs-lookup"><span data-stu-id="c4cd4-239">Action Selection</span></span>

<span data-ttu-id="c4cd4-240">Požadavek HTTP je požadavek GET.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-240">The HTTP request is a GET request.</span></span> <span data-ttu-id="c4cd4-241">Jsou akce kontroleru, které podporují GET `GetAll`, `GetById`, a `FindProductsByName`.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-241">The controller actions that support GET are `GetAll`, `GetById`, and `FindProductsByName`.</span></span> <span data-ttu-id="c4cd4-242">Slovníku trasy neobsahuje položku pro "action", proto nepotřebujeme tak, aby odpovídaly názvu akce.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-242">The route dictionary does not contain an entry for "action", so we don't need to match the action name.</span></span>

<span data-ttu-id="c4cd4-243">Dále pokusíme se porovnat názvy parametrů pro akce, pouze na akce GET.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-243">Next, we try to match parameter names for the actions, looking only at the GET actions.</span></span>

| <span data-ttu-id="c4cd4-244">Akce</span><span class="sxs-lookup"><span data-stu-id="c4cd4-244">Action</span></span> | <span data-ttu-id="c4cd4-245">Parametry shody</span><span class="sxs-lookup"><span data-stu-id="c4cd4-245">Parameters to Match</span></span> |
| --- | --- |
| `GetAll` | <span data-ttu-id="c4cd4-246">žádná</span><span class="sxs-lookup"><span data-stu-id="c4cd4-246">none</span></span> |
| `GetById` | <span data-ttu-id="c4cd4-247">"id"</span><span class="sxs-lookup"><span data-stu-id="c4cd4-247">"id"</span></span> |
| `FindProductsByName` | <span data-ttu-id="c4cd4-248">"název"</span><span class="sxs-lookup"><span data-stu-id="c4cd4-248">"name"</span></span> |

<span data-ttu-id="c4cd4-249">Všimněte si, *verze* parametr `GetById` nepovažuje, protože se jedná volitelný parametr.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-249">Notice that the *version* parameter of `GetById` is not considered, because it is an optional parameter.</span></span>

<span data-ttu-id="c4cd4-250">`GetAll` Metoda odpovídá triviálně.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-250">The `GetAll` method matches trivially.</span></span> <span data-ttu-id="c4cd4-251">`GetById` Metoda také odpovídající, protože obsahuje "id" slovníku trasy.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-251">The `GetById` method also matches, because the route dictionary contains "id".</span></span> <span data-ttu-id="c4cd4-252">`FindProductsByName` Metoda se neshoduje.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-252">The `FindProductsByName` method does not match.</span></span>

<span data-ttu-id="c4cd4-253">`GetById` Metoda služby wins, protože odpovídá jeden parametr, a bez parametrů pro `GetAll`.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-253">The `GetById` method wins, because it matches one parameter, versus no parameters for `GetAll`.</span></span> <span data-ttu-id="c4cd4-254">Metoda je volána s následující hodnoty parametrů:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-254">The method is invoked with the following parameter values:</span></span>

- <span data-ttu-id="c4cd4-255">*ID* = 1</span><span class="sxs-lookup"><span data-stu-id="c4cd4-255">*id* = 1</span></span>
- <span data-ttu-id="c4cd4-256">*verze* = 1.5</span><span class="sxs-lookup"><span data-stu-id="c4cd4-256">*version* = 1.5</span></span>

<span data-ttu-id="c4cd4-257">Všimněte si, že i když *verze* nebyl použit algoritmus výběru pochází hodnotu parametru řetězce dotazu URI.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-257">Notice that even though *version* was not used in the selection algorithm, the value of the parameter comes from the URI query string.</span></span>

## <a name="extension-points"></a><span data-ttu-id="c4cd4-258">Rozšiřovací body</span><span class="sxs-lookup"><span data-stu-id="c4cd4-258">Extension Points</span></span>

<span data-ttu-id="c4cd4-259">Webové rozhraní API poskytuje Rozšiřovací body pro některé části procesu směrování.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-259">Web API provides extension points for some parts of the routing process.</span></span>

| <span data-ttu-id="c4cd4-260">Rozhraní</span><span class="sxs-lookup"><span data-stu-id="c4cd4-260">Interface</span></span> | <span data-ttu-id="c4cd4-261">Popis</span><span class="sxs-lookup"><span data-stu-id="c4cd4-261">Description</span></span> |
| --- | --- |
| <span data-ttu-id="c4cd4-262">**IHttpControllerSelector**</span><span class="sxs-lookup"><span data-stu-id="c4cd4-262">**IHttpControllerSelector**</span></span> | <span data-ttu-id="c4cd4-263">Vybere kontroler.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-263">Selects the controller.</span></span> |
| <span data-ttu-id="c4cd4-264">**IHttpControllerTypeResolver**</span><span class="sxs-lookup"><span data-stu-id="c4cd4-264">**IHttpControllerTypeResolver**</span></span> | <span data-ttu-id="c4cd4-265">Získá seznam typů řadičů.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-265">Gets the list of controller types.</span></span> <span data-ttu-id="c4cd4-266">**DefaultHttpControllerSelector** z tohoto seznamu zvolí typ kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-266">The **DefaultHttpControllerSelector** chooses the controller type from this list.</span></span> |
| <span data-ttu-id="c4cd4-267">**IAssembliesResolver**</span><span class="sxs-lookup"><span data-stu-id="c4cd4-267">**IAssembliesResolver**</span></span> | <span data-ttu-id="c4cd4-268">Získá seznam sestavení projektu.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-268">Gets the list of project assemblies.</span></span> <span data-ttu-id="c4cd4-269">**IHttpControllerTypeResolver** rozhraní používá tento seznam k nalezení typů řadičů.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-269">The **IHttpControllerTypeResolver** interface uses this list to find the controller types.</span></span> |
| <span data-ttu-id="c4cd4-270">**IHttpControllerActivator**</span><span class="sxs-lookup"><span data-stu-id="c4cd4-270">**IHttpControllerActivator**</span></span> | <span data-ttu-id="c4cd4-271">Vytvoří nové instance kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-271">Creates new controller instances.</span></span> |
| <span data-ttu-id="c4cd4-272">**IHttpActionSelector**</span><span class="sxs-lookup"><span data-stu-id="c4cd4-272">**IHttpActionSelector**</span></span> | <span data-ttu-id="c4cd4-273">Vybere akci.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-273">Selects the action.</span></span> |
| <span data-ttu-id="c4cd4-274">**IHttpActionInvoker**</span><span class="sxs-lookup"><span data-stu-id="c4cd4-274">**IHttpActionInvoker**</span></span> | <span data-ttu-id="c4cd4-275">Vyvolá akci.</span><span class="sxs-lookup"><span data-stu-id="c4cd4-275">Invokes the action.</span></span> |

<span data-ttu-id="c4cd4-276">Pokud chcete poskytnout vlastní implementaci pro kterékoli z těchto rozhraní, použijte **služby** kolekce na **HttpConfiguration** objektu:</span><span class="sxs-lookup"><span data-stu-id="c4cd4-276">To provide your own implementation for any of these interfaces, use the **Services** collection on the **HttpConfiguration** object:</span></span>

[!code-csharp[Main](routing-and-action-selection/samples/sample11.cs)]
