---
title: IP adresa klienta Safelist pro ASP.NET Core
author: damienbod
description: Naučte se psát middleware nebo filtry akcí pro ověření vzdálených IP adres pro seznam schválených IP adres.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/ip-safelist
ms.openlocfilehash: 75c1ea3a6087f89a200d1f73b1ff65080c819ccd
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021767"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="b4b35-103">IP adresa klienta Safelist pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b4b35-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="b4b35-104">[Damien Bowden](https://twitter.com/damien_bod) a [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="b4b35-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="b4b35-105">Tento článek ukazuje tři způsoby implementace IP adresy Safelist (označované také jako seznam povolených) v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b4b35-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="b4b35-106">Doprovodné ukázková aplikace předvádí všechny tři přístupy.</span><span class="sxs-lookup"><span data-stu-id="b4b35-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="b4b35-107">Můžete použít:</span><span class="sxs-lookup"><span data-stu-id="b4b35-107">You can use:</span></span>

* <span data-ttu-id="b4b35-108">Middleware pro kontrolu vzdálené IP adresy každého požadavku.</span><span class="sxs-lookup"><span data-stu-id="b4b35-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="b4b35-109">Filtry akcí MVC pro kontrolu vzdálené IP adresy žádostí o konkrétní řadiče nebo metody akcí.</span><span class="sxs-lookup"><span data-stu-id="b4b35-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="b4b35-110">RazorStránky filtrují, aby kontrolovaly vzdálené IP adresy žádostí o Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="b4b35-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="b4b35-111">V každém případě je řetězec, který obsahuje schválené IP adresy klienta, uložen v nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4b35-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="b4b35-112">Middleware nebo filtr:</span><span class="sxs-lookup"><span data-stu-id="b4b35-112">The middleware or filter:</span></span>

* <span data-ttu-id="b4b35-113">Analyzuje řetězec do pole.</span><span class="sxs-lookup"><span data-stu-id="b4b35-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="b4b35-114">Kontroluje, zda v poli existuje vzdálená IP adresa.</span><span class="sxs-lookup"><span data-stu-id="b4b35-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="b4b35-115">Přístup je povolený, pokud pole obsahuje IP adresu.</span><span class="sxs-lookup"><span data-stu-id="b4b35-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="b4b35-116">V opačném případě se vrátí stavový kód HTTP 403 zakázáno.</span><span class="sxs-lookup"><span data-stu-id="b4b35-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="b4b35-117">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b4b35-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="b4b35-118">Safelist IP adres</span><span class="sxs-lookup"><span data-stu-id="b4b35-118">IP address safelist</span></span>

<span data-ttu-id="b4b35-119">V ukázkové aplikaci se IP adresa Safelist:</span><span class="sxs-lookup"><span data-stu-id="b4b35-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="b4b35-120">Definováno `AdminSafeList` vlastností v souboru *appsettings.js* .</span><span class="sxs-lookup"><span data-stu-id="b4b35-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="b4b35-121">Řetězec oddělený středníkem, který může obsahovat adresy [Internet Protocol verze 4 (IPv4)](https://wikipedia.org/wiki/IPv4) i [Internet Protocol verze 6 (IPv6)](https://wikipedia.org/wiki/IPv6) .</span><span class="sxs-lookup"><span data-stu-id="b4b35-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="b4b35-122">V předchozím příkladu `127.0.0.1` jsou povoleny adresy IPv4 a `192.168.1.5` Adresa zpětné smyčky IPv6 `::1` (komprimovaný formát pro `0:0:0:0:0:0:0:1` ).</span><span class="sxs-lookup"><span data-stu-id="b4b35-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="b4b35-123">Middleware</span><span class="sxs-lookup"><span data-stu-id="b4b35-123">Middleware</span></span>

<span data-ttu-id="b4b35-124">`Startup.Configure`Metoda přidá vlastní `AdminSafeListMiddleware` typ middlewaru do kanálu požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4b35-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="b4b35-125">Safelist se načte pomocí poskytovatele konfigurace .NET Core a předává se jako parametr konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="b4b35-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="b4b35-126">Middleware analyzuje řetězec do pole a vyhledá vzdálenou IP adresu v poli.</span><span class="sxs-lookup"><span data-stu-id="b4b35-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="b4b35-127">Pokud se vzdálená IP adresa nenajde, middleware vrátí HTTP 403 zakázáno.</span><span class="sxs-lookup"><span data-stu-id="b4b35-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="b4b35-128">Tento proces ověřování se pro požadavky HTTP GET nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="b4b35-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="b4b35-129">Filtr akcí</span><span class="sxs-lookup"><span data-stu-id="b4b35-129">Action filter</span></span>

<span data-ttu-id="b4b35-130">Pokud chcete řízení přístupu řízenému Safelist pro konkrétní řadiče MVC nebo metody akcí, použijte filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="b4b35-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="b4b35-131">Například:</span><span class="sxs-lookup"><span data-stu-id="b4b35-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="b4b35-132">V `Startup.ConfigureServices` přidejte filtr akcí do kolekce filtry MVC.</span><span class="sxs-lookup"><span data-stu-id="b4b35-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="b4b35-133">V následujícím příkladu `ClientIpCheckActionFilter` je přidán filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="b4b35-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="b4b35-134">Safelist a instance protokolovacího nástroje jsou předány jako parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="b4b35-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="b4b35-135">Filtr akce lze potom použít pro metodu kontroleru nebo akce s atributem [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) :</span><span class="sxs-lookup"><span data-stu-id="b4b35-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="b4b35-136">V ukázkové aplikaci se filtr akcí aplikuje na `Get` metodu akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="b4b35-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="b4b35-137">Při testování aplikace odesláním:</span><span class="sxs-lookup"><span data-stu-id="b4b35-137">When you test the app by sending:</span></span>

* <span data-ttu-id="b4b35-138">Požadavek HTTP GET `[ServiceFilter]` OVĚŘÍ IP adresu klienta.</span><span class="sxs-lookup"><span data-stu-id="b4b35-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="b4b35-139">Pokud je povolen přístup k `Get` metodě Action, variace následujícího výstupu konzoly je vytvořena metodou Action Filter a Action:</span><span class="sxs-lookup"><span data-stu-id="b4b35-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="b4b35-140">Příkaz žádosti HTTP jiný než GET, `AdminSafeListMiddleware` middleware OVĚŘÍ IP adresu klienta.</span><span class="sxs-lookup"><span data-stu-id="b4b35-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="no-locrazor-pages-filter"></a><span data-ttu-id="b4b35-141">RazorFiltr stránek</span><span class="sxs-lookup"><span data-stu-id="b4b35-141">Razor Pages filter</span></span>

<span data-ttu-id="b4b35-142">Pokud chcete ovládací prvek přístupu řízený Safelist pro Razor aplikaci Pages, použijte Razor filtr stránky.</span><span class="sxs-lookup"><span data-stu-id="b4b35-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="b4b35-143">Například:</span><span class="sxs-lookup"><span data-stu-id="b4b35-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="b4b35-144">V nástroji `Startup.ConfigureServices` Povolte Razor filtr stránky přidáním do kolekce filtry MVC.</span><span class="sxs-lookup"><span data-stu-id="b4b35-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="b4b35-145">V následujícím příkladu `ClientIpCheckPageFilter` Razor je přidán filtr stránky.</span><span class="sxs-lookup"><span data-stu-id="b4b35-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="b4b35-146">Safelist a instance protokolovacího nástroje jsou předány jako parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="b4b35-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="b4b35-147">Po vyžádání stránky *indexu* ukázkové aplikace Razor Razor ověří filtr stránek IP adresu klienta.</span><span class="sxs-lookup"><span data-stu-id="b4b35-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="b4b35-148">Filtr vytváří variaci následujícího výstupu konzoly:</span><span class="sxs-lookup"><span data-stu-id="b4b35-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="b4b35-149">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b4b35-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="b4b35-150">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="b4b35-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
