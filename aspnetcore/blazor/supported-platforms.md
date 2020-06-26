---
title: ASP.NET Core Blazor podporované platformy
author: guardrex
description: Přečtěte si o podporovaných platformách pro ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: adf27fa84acb3929a1639b561c728c2db29723f6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401932"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="089ba-103">ASP.NET Core Blazor podporované platformy</span><span class="sxs-lookup"><span data-stu-id="089ba-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="089ba-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="089ba-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="089ba-105">Požadavky na prohlížeč</span><span class="sxs-lookup"><span data-stu-id="089ba-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="089ba-106">Prohlížeč</span><span class="sxs-lookup"><span data-stu-id="089ba-106">Browser</span></span>                          | <span data-ttu-id="089ba-107">Verze</span><span class="sxs-lookup"><span data-stu-id="089ba-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="089ba-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="089ba-108">Microsoft Edge</span></span>                   | <span data-ttu-id="089ba-109">Current</span><span class="sxs-lookup"><span data-stu-id="089ba-109">Current</span></span>               |
| <span data-ttu-id="089ba-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="089ba-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="089ba-111">Current</span><span class="sxs-lookup"><span data-stu-id="089ba-111">Current</span></span>               |
| <span data-ttu-id="089ba-112">Google Chrome, včetně Androidu</span><span class="sxs-lookup"><span data-stu-id="089ba-112">Google Chrome, including Android</span></span> | <span data-ttu-id="089ba-113">Current</span><span class="sxs-lookup"><span data-stu-id="089ba-113">Current</span></span>               |
| <span data-ttu-id="089ba-114">Safari, včetně iOS</span><span class="sxs-lookup"><span data-stu-id="089ba-114">Safari, including iOS</span></span>            | <span data-ttu-id="089ba-115">Current</span><span class="sxs-lookup"><span data-stu-id="089ba-115">Current</span></span>               |
| <span data-ttu-id="089ba-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="089ba-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="089ba-117">Nepodporováno&dagger;</span><span class="sxs-lookup"><span data-stu-id="089ba-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="089ba-118">&dagger;Aplikace Microsoft Internet Explorer nepodporuje [WebAssembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="089ba-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="089ba-119">Prohlížeč</span><span class="sxs-lookup"><span data-stu-id="089ba-119">Browser</span></span>                          | <span data-ttu-id="089ba-120">Verze</span><span class="sxs-lookup"><span data-stu-id="089ba-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="089ba-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="089ba-121">Microsoft Edge</span></span>                   | <span data-ttu-id="089ba-122">Current</span><span class="sxs-lookup"><span data-stu-id="089ba-122">Current</span></span>    |
| <span data-ttu-id="089ba-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="089ba-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="089ba-124">Current</span><span class="sxs-lookup"><span data-stu-id="089ba-124">Current</span></span>    |
| <span data-ttu-id="089ba-125">Google Chrome, včetně Androidu</span><span class="sxs-lookup"><span data-stu-id="089ba-125">Google Chrome, including Android</span></span> | <span data-ttu-id="089ba-126">Current</span><span class="sxs-lookup"><span data-stu-id="089ba-126">Current</span></span>    |
| <span data-ttu-id="089ba-127">Safari, včetně iOS</span><span class="sxs-lookup"><span data-stu-id="089ba-127">Safari, including iOS</span></span>            | <span data-ttu-id="089ba-128">Current</span><span class="sxs-lookup"><span data-stu-id="089ba-128">Current</span></span>    |
| <span data-ttu-id="089ba-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="089ba-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="089ba-130">odst&dagger;</span><span class="sxs-lookup"><span data-stu-id="089ba-130">11&dagger;</span></span> |

<span data-ttu-id="089ba-131">&dagger;Jsou vyžadovány další obslužné nabídky (například příslibů lze přidat prostřednictvím [`Polyfill.io`](https://polyfill.io/v3/) sady prostředků).</span><span class="sxs-lookup"><span data-stu-id="089ba-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="089ba-132">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="089ba-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
