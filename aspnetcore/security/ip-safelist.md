---
title: IP adresa klienta Safelist pro ASP.NET Core
author: damienbod
description: Naučte se psát middleware nebo filtry akcí pro ověření vzdálených IP adres pro seznam schválených IP adres.
ms.author: tdykstra
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: ca05989efabea3a71c6912e98055a6746e0f5966
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223932"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="2a7b4-103">IP adresa klienta Safelist pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2a7b4-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="2a7b4-104">[Damien Bowden](https://twitter.com/damien_bod) a [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="2a7b4-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="2a7b4-105">Tento článek ukazuje tři způsoby implementace IP Safelist (označované také jako seznam povolených) v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-105">This article shows three ways to implement an IP safelist (also known as a whitelist) in an ASP.NET Core app.</span></span> <span data-ttu-id="2a7b4-106">Můžete použít:</span><span class="sxs-lookup"><span data-stu-id="2a7b4-106">You can use:</span></span>

* <span data-ttu-id="2a7b4-107">Middleware pro kontrolu vzdálené IP adresy každého požadavku.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-107">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="2a7b4-108">Filtry akcí pro kontrolu vzdálené IP adresy žádostí o konkrétní řadiče nebo metody akcí.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-108">Action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="2a7b4-109">Razor Pages filtry pro kontrolu vzdálené IP adresy žádostí o stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-109">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="2a7b4-110">V každém případě je řetězec, který obsahuje schválené IP adresy klienta, uložen v nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-110">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="2a7b4-111">Middleware nebo filtr analyzuje řetězec na seznam a kontroluje, jestli je vzdálená IP adresa v seznamu.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-111">The middleware or filter parses the string into a list and checks if the remote IP is in the list.</span></span> <span data-ttu-id="2a7b4-112">V takovém případě se vrátí stavový kód HTTP 403 zakázáno.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-112">If not, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="2a7b4-113">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2a7b4-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="the-safelist"></a><span data-ttu-id="2a7b4-114">Safelist</span><span class="sxs-lookup"><span data-stu-id="2a7b4-114">The safelist</span></span>

<span data-ttu-id="2a7b4-115">Seznam je nakonfigurovaný v souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="2a7b4-115">The list is configured in the *appsettings.json* file.</span></span> <span data-ttu-id="2a7b4-116">Jedná se o středníkem oddělený seznam a může obsahovat adresy IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-116">It's a semicolon-delimited list and can contain IPv4 and IPv6 addresses.</span></span>

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a><span data-ttu-id="2a7b4-117">Middleware</span><span class="sxs-lookup"><span data-stu-id="2a7b4-117">Middleware</span></span>

<span data-ttu-id="2a7b4-118">`Configure` Metoda přidá middleware a předá do něj řetězec Safelist v parametru konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-118">The `Configure` method adds the middleware and passes the safelist string to it in a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="2a7b4-119">Middleware analyzuje řetězec do pole a hledá v poli vzdálenou IP adresu.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-119">The middleware parses the string into an array and looks for the remote IP address in the array.</span></span> <span data-ttu-id="2a7b4-120">Pokud se vzdálená IP adresa nenajde, middleware vrátí HTTP 401 zakázáno.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-120">If the remote IP address is not found, the middleware returns HTTP 401 Forbidden.</span></span> <span data-ttu-id="2a7b4-121">Tento proces ověřování se pro požadavky HTTP GET nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-121">This validation process is bypassed for HTTP Get requests.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="2a7b4-122">Filtr akcí</span><span class="sxs-lookup"><span data-stu-id="2a7b4-122">Action filter</span></span>

<span data-ttu-id="2a7b4-123">Pokud chcete Safelist jenom pro konkrétní řadiče nebo metody akcí, použijte filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-123">If you want a safelist only for specific controllers or action methods, use an action filter.</span></span> <span data-ttu-id="2a7b4-124">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="2a7b4-124">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

<span data-ttu-id="2a7b4-125">Filtr akce se přidá do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-125">The action filter is added to the services container.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="2a7b4-126">Filtr je pak možné použít na řadiči nebo metodě akce.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-126">The filter can then be used on a controller or action method.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

<span data-ttu-id="2a7b4-127">V ukázkové aplikaci se filtr aplikuje na `Get` metodu.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-127">In the sample app, the filter is applied to the `Get` method.</span></span> <span data-ttu-id="2a7b4-128">Takže když aplikaci otestujete odesláním `Get` požadavku rozhraní API, atribut ověří IP adresu klienta.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-128">So when you test the app by sending a `Get` API request, the attribute is validating the client IP address.</span></span> <span data-ttu-id="2a7b4-129">Při testování voláním rozhraní API pomocí jakékoli jiné metody HTTP middleware ověřuje IP adresu klienta.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-129">When you test by calling the API with any other HTTP method, the middleware is validating the client IP.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="2a7b4-130">Filtr Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2a7b4-130">Razor Pages filter</span></span> 

<span data-ttu-id="2a7b4-131">Pokud chcete Safelist pro aplikaci Razor Pages, použijte filtr Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-131">If you want a safelist for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="2a7b4-132">Tady je příklad:</span><span class="sxs-lookup"><span data-stu-id="2a7b4-132">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

<span data-ttu-id="2a7b4-133">Tento filtr je povolen přidáním do kolekce filtry MVC.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-133">This filter is enabled by adding it to the MVC Filters collection.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

<span data-ttu-id="2a7b4-134">Když aplikaci spustíte a požádáte o stránku Razor, filtr Razor Pages ověřuje IP adresu klienta.</span><span class="sxs-lookup"><span data-stu-id="2a7b4-134">When you run the app and request a Razor page, the Razor Pages filter is validating the client IP.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2a7b4-135">Další postup</span><span class="sxs-lookup"><span data-stu-id="2a7b4-135">Next steps</span></span>

<span data-ttu-id="2a7b4-136">[Přečtěte si další informace o ASP.NET Core middlewaru](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="2a7b4-136">[Learn more about ASP.NET Core Middleware](xref:fundamentals/middleware/index).</span></span>
