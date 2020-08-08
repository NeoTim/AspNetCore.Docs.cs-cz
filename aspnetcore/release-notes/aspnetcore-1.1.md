---
title: Co je nového v ASP.NET Core 1,1
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 1,1.
ms.author: riande
ms.date: 12/18/2018
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
uid: aspnetcore-1.1
ms.openlocfilehash: 9b3e0a368fdcd9e1044cfe6bf6a13ca11d3039cc
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020662"
---
# <a name="whats-new-in-aspnet-core-11"></a><span data-ttu-id="25c1a-103">Co je nového v ASP.NET Core 1,1</span><span class="sxs-lookup"><span data-stu-id="25c1a-103">What's new in ASP.NET Core 1.1</span></span>

<span data-ttu-id="25c1a-104">ASP.NET Core 1,1 obsahuje následující nové funkce:</span><span class="sxs-lookup"><span data-stu-id="25c1a-104">ASP.NET Core 1.1 includes the following new features:</span></span>

- [<span data-ttu-id="25c1a-105">Middleware pro přepis adres URL</span><span class="sxs-lookup"><span data-stu-id="25c1a-105">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting)
- [<span data-ttu-id="25c1a-106">Middleware pro ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="25c1a-106">Response Caching Middleware</span></span>](xref:performance/caching/middleware)
- [<span data-ttu-id="25c1a-107">Zobrazit součásti jako pomocníky značek</span><span class="sxs-lookup"><span data-stu-id="25c1a-107">View Components as Tag Helpers</span></span>](xref:mvc/views/view-components#invoking-a-view-component-as-a-tag-helper)
- [<span data-ttu-id="25c1a-108">Middleware jako filtry MVC</span><span class="sxs-lookup"><span data-stu-id="25c1a-108">Middleware as MVC filters</span></span>](xref:mvc/controllers/filters#using-middleware-in-the-filter-pipeline)
- [<span data-ttu-id="25c1a-109">CookieTempData poskytovatel – based</span><span class="sxs-lookup"><span data-stu-id="25c1a-109">Cookie-based TempData provider</span></span>](xref:fundamentals/app-state#tempdata)
- [<span data-ttu-id="25c1a-110">Zprostředkovatel protokolování Azure App Service</span><span class="sxs-lookup"><span data-stu-id="25c1a-110">Azure App Service logging provider</span></span>](xref:fundamentals/logging/index#azure-app-service-provider)
- [<span data-ttu-id="25c1a-111">Poskytovatel konfigurace Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="25c1a-111">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
- [<span data-ttu-id="25c1a-112">Úložiště klíčů pro ochranu dat úložiště Azure a Redis</span><span class="sxs-lookup"><span data-stu-id="25c1a-112">Azure and Redis Storage Data Protection Key Repositories</span></span>](xref:security/data-protection/implementation/key-storage-providers)
- <span data-ttu-id="25c1a-113">Server weblisten pro Windows</span><span class="sxs-lookup"><span data-stu-id="25c1a-113">WebListener Server for Windows</span></span>
- [<span data-ttu-id="25c1a-114">Podpora WebSockets</span><span class="sxs-lookup"><span data-stu-id="25c1a-114">WebSockets support</span></span>](xref:fundamentals/websockets)

## <a name="choosing-between-versions-10-and-11-of-aspnet-core"></a><span data-ttu-id="25c1a-115">Volba mezi verzemi 1,0 a 1,1 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="25c1a-115">Choosing between versions 1.0 and 1.1 of ASP.NET Core</span></span>

<span data-ttu-id="25c1a-116">ASP.NET Core 1,1 obsahuje více funkcí než ASP.NET Core 1,0.</span><span class="sxs-lookup"><span data-stu-id="25c1a-116">ASP.NET Core 1.1 has more features than ASP.NET Core 1.0.</span></span> <span data-ttu-id="25c1a-117">Obecně doporučujeme, abyste používali nejnovější verzi.</span><span class="sxs-lookup"><span data-stu-id="25c1a-117">In general, we recommend you use the latest version.</span></span>

## <a name="additional-information"></a><span data-ttu-id="25c1a-118">Další informace</span><span class="sxs-lookup"><span data-stu-id="25c1a-118">Additional Information</span></span>

- [<span data-ttu-id="25c1a-119">Poznámky k verzi ASP.NET Core 1.1.0</span><span class="sxs-lookup"><span data-stu-id="25c1a-119">ASP.NET Core 1.1.0 Release Notes</span></span>](https://github.com/dotnet/aspnetcore/releases/tag/1.1.0)
- <span data-ttu-id="25c1a-120">Pokud se chcete připojit k vývoji a plánům vývojového týmu ASP.NET Core, prolaďte si [rychlou schůzku komunitu ASP.NET](https://live.asp.net/).</span><span class="sxs-lookup"><span data-stu-id="25c1a-120">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
