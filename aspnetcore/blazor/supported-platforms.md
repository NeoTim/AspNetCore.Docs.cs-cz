---
title: ASP.NET Core Blazor podporované platformy
author: guardrex
description: Přečtěte si o podporovaných platformách pro ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 67896bcdd5d99ff2c9cf22e3902262f9513eb4f6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013525"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="53afe-103">ASP.NET Core Blazor podporované platformy</span><span class="sxs-lookup"><span data-stu-id="53afe-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="53afe-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="53afe-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="53afe-105">Požadavky na prohlížeč</span><span class="sxs-lookup"><span data-stu-id="53afe-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="53afe-106">Prohlížeč</span><span class="sxs-lookup"><span data-stu-id="53afe-106">Browser</span></span>                          | <span data-ttu-id="53afe-107">Verze</span><span class="sxs-lookup"><span data-stu-id="53afe-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="53afe-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="53afe-108">Microsoft Edge</span></span>                   | <span data-ttu-id="53afe-109">Current</span><span class="sxs-lookup"><span data-stu-id="53afe-109">Current</span></span>               |
| <span data-ttu-id="53afe-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="53afe-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="53afe-111">Current</span><span class="sxs-lookup"><span data-stu-id="53afe-111">Current</span></span>               |
| <span data-ttu-id="53afe-112">Google Chrome, včetně Androidu</span><span class="sxs-lookup"><span data-stu-id="53afe-112">Google Chrome, including Android</span></span> | <span data-ttu-id="53afe-113">Current</span><span class="sxs-lookup"><span data-stu-id="53afe-113">Current</span></span>               |
| <span data-ttu-id="53afe-114">Safari, včetně iOS</span><span class="sxs-lookup"><span data-stu-id="53afe-114">Safari, including iOS</span></span>            | <span data-ttu-id="53afe-115">Current</span><span class="sxs-lookup"><span data-stu-id="53afe-115">Current</span></span>               |
| <span data-ttu-id="53afe-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="53afe-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="53afe-117">Nepodporováno&dagger;</span><span class="sxs-lookup"><span data-stu-id="53afe-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="53afe-118">&dagger;Aplikace Microsoft Internet Explorer nepodporuje [WebAssembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="53afe-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="53afe-119">Prohlížeč</span><span class="sxs-lookup"><span data-stu-id="53afe-119">Browser</span></span>                          | <span data-ttu-id="53afe-120">Verze</span><span class="sxs-lookup"><span data-stu-id="53afe-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="53afe-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="53afe-121">Microsoft Edge</span></span>                   | <span data-ttu-id="53afe-122">Current</span><span class="sxs-lookup"><span data-stu-id="53afe-122">Current</span></span>    |
| <span data-ttu-id="53afe-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="53afe-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="53afe-124">Current</span><span class="sxs-lookup"><span data-stu-id="53afe-124">Current</span></span>    |
| <span data-ttu-id="53afe-125">Google Chrome, včetně Androidu</span><span class="sxs-lookup"><span data-stu-id="53afe-125">Google Chrome, including Android</span></span> | <span data-ttu-id="53afe-126">Current</span><span class="sxs-lookup"><span data-stu-id="53afe-126">Current</span></span>    |
| <span data-ttu-id="53afe-127">Safari, včetně iOS</span><span class="sxs-lookup"><span data-stu-id="53afe-127">Safari, including iOS</span></span>            | <span data-ttu-id="53afe-128">Current</span><span class="sxs-lookup"><span data-stu-id="53afe-128">Current</span></span>    |
| <span data-ttu-id="53afe-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="53afe-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="53afe-130">odst&dagger;</span><span class="sxs-lookup"><span data-stu-id="53afe-130">11&dagger;</span></span> |

<span data-ttu-id="53afe-131">&dagger;Jsou vyžadovány další obslužné nabídky (například příslibů lze přidat prostřednictvím [`Polyfill.io`](https://polyfill.io/v3/) sady prostředků).</span><span class="sxs-lookup"><span data-stu-id="53afe-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="53afe-132">Další materiály</span><span class="sxs-lookup"><span data-stu-id="53afe-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
