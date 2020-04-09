---
title: Seznam bezpečných IP adres klientů pro ASP.NET Core
author: damienbod
description: Přečtěte si, jak psát middleware nebo akční filtry pro ověření vzdálených IP adres podle seznamu schválených IP adres.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471790"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="14be5-103">Seznam bezpečných IP adres klientů pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="14be5-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="14be5-104">Damien [Bowden](https://twitter.com/damien_bod) a [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="14be5-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="14be5-105">Tento článek ukazuje tři způsoby implementace seznamu bezpečných adres IP (označovaný také jako seznam povolených adres) v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="14be5-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="14be5-106">Doprovodná ukázková aplikace demonstruje všechny tři přístupy.</span><span class="sxs-lookup"><span data-stu-id="14be5-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="14be5-107">Můžete použít:</span><span class="sxs-lookup"><span data-stu-id="14be5-107">You can use:</span></span>

* <span data-ttu-id="14be5-108">Middleware pro kontrolu vzdálené IP adresy každého požadavku.</span><span class="sxs-lookup"><span data-stu-id="14be5-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="14be5-109">Filtry akcí MVC pro kontrolu vzdálené IP adresy požadavků na konkrétní řadiče nebo metody akce.</span><span class="sxs-lookup"><span data-stu-id="14be5-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="14be5-110">Razor Pages filtry pro kontrolu vzdálené IP adresy žádostí o razor stránek.</span><span class="sxs-lookup"><span data-stu-id="14be5-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="14be5-111">V každém případě je řetězec obsahující schválené IP adresy klienta uložen v nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="14be5-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="14be5-112">Middleware nebo filtr:</span><span class="sxs-lookup"><span data-stu-id="14be5-112">The middleware or filter:</span></span>

* <span data-ttu-id="14be5-113">Analyzuje řetězec do pole.</span><span class="sxs-lookup"><span data-stu-id="14be5-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="14be5-114">Zkontroluje, zda v poli existuje vzdálená adresa IP.</span><span class="sxs-lookup"><span data-stu-id="14be5-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="14be5-115">Přístup je povolen, pokud pole obsahuje adresu IP.</span><span class="sxs-lookup"><span data-stu-id="14be5-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="14be5-116">V opačném případě je vrácen zakázaný stavový kód HTTP 403.</span><span class="sxs-lookup"><span data-stu-id="14be5-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="14be5-117">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="14be5-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="14be5-118">Seznam bezpečných ADRES IP</span><span class="sxs-lookup"><span data-stu-id="14be5-118">IP address safelist</span></span>

<span data-ttu-id="14be5-119">V ukázkové aplikaci je seznam bezpečných IP adres:</span><span class="sxs-lookup"><span data-stu-id="14be5-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="14be5-120">Definováno `AdminSafeList` vlastností v souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="14be5-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="14be5-121">Řetězec oddělený středníkem, který může obsahovat adresy [protokolu IP verze 4 (IPv4)](https://wikipedia.org/wiki/IPv4) i protokolu [IPv6.](https://wikipedia.org/wiki/IPv6)</span><span class="sxs-lookup"><span data-stu-id="14be5-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="14be5-122">V předchozím příkladu jsou povoleny adresy `127.0.0.1` IPv4 a `192.168.1.5` a adresa `::1` zpětné smyčky IPv6 (komprimovaný formát pro). `0:0:0:0:0:0:0:1`</span><span class="sxs-lookup"><span data-stu-id="14be5-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="14be5-123">Middleware</span><span class="sxs-lookup"><span data-stu-id="14be5-123">Middleware</span></span>

<span data-ttu-id="14be5-124">Metoda `Startup.Configure` přidá vlastní `AdminSafeListMiddleware` middleware typ kanálu žádosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="14be5-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="14be5-125">Safelist je načten s poskytovatelem konfigurace .NET Core a je předán jako parametr konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="14be5-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="14be5-126">Middleware analyzuje řetězec do pole a hledá vzdálené IP adresy v poli.</span><span class="sxs-lookup"><span data-stu-id="14be5-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="14be5-127">Pokud není nalezena vzdálená IP adresa, middleware vrátí HTTP 403 Forbidden.</span><span class="sxs-lookup"><span data-stu-id="14be5-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="14be5-128">Tento proces ověření je vynechán pro požadavky HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="14be5-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="14be5-129">Filtr akce</span><span class="sxs-lookup"><span data-stu-id="14be5-129">Action filter</span></span>

<span data-ttu-id="14be5-130">Pokud chcete bezpečné řízení přístupu řízené bezpečným seznamem pro konkrétní řadiče MVC nebo metody akce, použijte filtr akce.</span><span class="sxs-lookup"><span data-stu-id="14be5-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="14be5-131">Příklad:</span><span class="sxs-lookup"><span data-stu-id="14be5-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="14be5-132">V `Startup.ConfigureServices`aplikacích přidejte filtr akcí do kolekce filtrů MVC.</span><span class="sxs-lookup"><span data-stu-id="14be5-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="14be5-133">V následujícím příkladu `ClientIpCheckActionFilter` je přidán filtr akce.</span><span class="sxs-lookup"><span data-stu-id="14be5-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="14be5-134">Safelist a instance protokolování konzoly jsou předány jako parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="14be5-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="14be5-135">Filtr akce pak lze použít na řadič nebo metodu akce s atributem [[ServiceFilter]:](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute)</span><span class="sxs-lookup"><span data-stu-id="14be5-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="14be5-136">V ukázkové aplikaci se filtr akce použije `Get` na metodu akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="14be5-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="14be5-137">Při testování aplikace odesláním:</span><span class="sxs-lookup"><span data-stu-id="14be5-137">When you test the app by sending:</span></span>

* <span data-ttu-id="14be5-138">Požadavek HTTP GET, `[ServiceFilter]` atribut ověří IP adresu klienta.</span><span class="sxs-lookup"><span data-stu-id="14be5-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="14be5-139">Pokud je povolen `Get` přístup k metodě akce, je filtrem akce a metodou akce vytvořena varianta následujícího výstupu konzoly:</span><span class="sxs-lookup"><span data-stu-id="14be5-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="14be5-140">Sloveso požadavku HTTP jiné `AdminSafeListMiddleware` než GET, middleware ověří IP adresu klienta.</span><span class="sxs-lookup"><span data-stu-id="14be5-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="14be5-141">Holicí strojek stránky, filtr</span><span class="sxs-lookup"><span data-stu-id="14be5-141">Razor Pages filter</span></span>

<span data-ttu-id="14be5-142">Pokud chcete pro aplikaci Razor Pages řídit přístup řízený bezpečným seznamem, použijte filtr Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="14be5-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="14be5-143">Příklad:</span><span class="sxs-lookup"><span data-stu-id="14be5-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="14be5-144">V `Startup.ConfigureServices`, povolte filtr Razor Pages přidáním do kolekce filtrů MVC.</span><span class="sxs-lookup"><span data-stu-id="14be5-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="14be5-145">V následujícím příkladu `ClientIpCheckPageFilter` je přidán filtr Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="14be5-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="14be5-146">Safelist a instance protokolování konzoly jsou předány jako parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="14be5-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="14be5-147">Když je požadována stránka *Index Razor* ukázkové aplikace, filtr Razor Pages ověří IP adresu klienta.</span><span class="sxs-lookup"><span data-stu-id="14be5-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="14be5-148">Filtr vytváří variaci následujícího výstupu konzoly:</span><span class="sxs-lookup"><span data-stu-id="14be5-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="14be5-149">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="14be5-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="14be5-150">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="14be5-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
