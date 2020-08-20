---
title: ASP.NET Core Blazor podporované platformy
author: guardrex
description: Přečtěte si o podporovaných platformách pro ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 692ab63bb48dbfa29021d59cdf035e9549d3039c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625944"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="e1f82-103">ASP.NET Core Blazor podporované platformy</span><span class="sxs-lookup"><span data-stu-id="e1f82-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="e1f82-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e1f82-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="e1f82-105">Požadavky na prohlížeč</span><span class="sxs-lookup"><span data-stu-id="e1f82-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="e1f82-106">Prohlížeč</span><span class="sxs-lookup"><span data-stu-id="e1f82-106">Browser</span></span>                          | <span data-ttu-id="e1f82-107">Verze</span><span class="sxs-lookup"><span data-stu-id="e1f82-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="e1f82-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="e1f82-108">Microsoft Edge</span></span>                   | <span data-ttu-id="e1f82-109">Current</span><span class="sxs-lookup"><span data-stu-id="e1f82-109">Current</span></span>               |
| <span data-ttu-id="e1f82-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="e1f82-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="e1f82-111">Current</span><span class="sxs-lookup"><span data-stu-id="e1f82-111">Current</span></span>               |
| <span data-ttu-id="e1f82-112">Google Chrome, včetně Androidu</span><span class="sxs-lookup"><span data-stu-id="e1f82-112">Google Chrome, including Android</span></span> | <span data-ttu-id="e1f82-113">Current</span><span class="sxs-lookup"><span data-stu-id="e1f82-113">Current</span></span>               |
| <span data-ttu-id="e1f82-114">Safari, včetně iOS</span><span class="sxs-lookup"><span data-stu-id="e1f82-114">Safari, including iOS</span></span>            | <span data-ttu-id="e1f82-115">Current</span><span class="sxs-lookup"><span data-stu-id="e1f82-115">Current</span></span>               |
| <span data-ttu-id="e1f82-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="e1f82-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="e1f82-117">Nepodporováno&dagger;</span><span class="sxs-lookup"><span data-stu-id="e1f82-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="e1f82-118">&dagger;Aplikace Microsoft Internet Explorer nepodporuje [WebAssembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="e1f82-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="e1f82-119">Prohlížeč</span><span class="sxs-lookup"><span data-stu-id="e1f82-119">Browser</span></span>                          | <span data-ttu-id="e1f82-120">Verze</span><span class="sxs-lookup"><span data-stu-id="e1f82-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="e1f82-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="e1f82-121">Microsoft Edge</span></span>                   | <span data-ttu-id="e1f82-122">Current</span><span class="sxs-lookup"><span data-stu-id="e1f82-122">Current</span></span>    |
| <span data-ttu-id="e1f82-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="e1f82-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="e1f82-124">Current</span><span class="sxs-lookup"><span data-stu-id="e1f82-124">Current</span></span>    |
| <span data-ttu-id="e1f82-125">Google Chrome, včetně Androidu</span><span class="sxs-lookup"><span data-stu-id="e1f82-125">Google Chrome, including Android</span></span> | <span data-ttu-id="e1f82-126">Current</span><span class="sxs-lookup"><span data-stu-id="e1f82-126">Current</span></span>    |
| <span data-ttu-id="e1f82-127">Safari, včetně iOS</span><span class="sxs-lookup"><span data-stu-id="e1f82-127">Safari, including iOS</span></span>            | <span data-ttu-id="e1f82-128">Current</span><span class="sxs-lookup"><span data-stu-id="e1f82-128">Current</span></span>    |
| <span data-ttu-id="e1f82-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="e1f82-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="e1f82-130">odst&dagger;</span><span class="sxs-lookup"><span data-stu-id="e1f82-130">11&dagger;</span></span> |

<span data-ttu-id="e1f82-131">&dagger;Jsou vyžadovány další obslužné nabídky (například příslibů lze přidat prostřednictvím [`Polyfill.io`](https://polyfill.io/v3/) sady prostředků).</span><span class="sxs-lookup"><span data-stu-id="e1f82-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e1f82-132">Další materiály</span><span class="sxs-lookup"><span data-stu-id="e1f82-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
