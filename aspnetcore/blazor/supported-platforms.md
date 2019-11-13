---
title: Podporované platformy ASP.NET Core Blazor
author: guardrex
description: Přečtěte si o podporovaných platformách pro ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
uid: blazor/supported-platforms
ms.openlocfilehash: de51296cc8785474e1c1406cfd5d4e5bd4050172
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962733"
---
# <a name="aspnet-core-opno-locblazor-supported-platforms"></a><span data-ttu-id="fc203-103">Podporované platformy ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="fc203-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="fc203-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fc203-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a><span data-ttu-id="fc203-105">Požadavky prohlížeče</span><span class="sxs-lookup"><span data-stu-id="fc203-105">Browser requirements</span></span>

### <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="fc203-106"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="fc203-106"> WebAssembly</span></span>

| <span data-ttu-id="fc203-107">Prohlížeee</span><span class="sxs-lookup"><span data-stu-id="fc203-107">Browser</span></span>                          | <span data-ttu-id="fc203-108">Version</span><span class="sxs-lookup"><span data-stu-id="fc203-108">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="fc203-109">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="fc203-109">Microsoft Edge</span></span>                   | <span data-ttu-id="fc203-110">Aktivní</span><span class="sxs-lookup"><span data-stu-id="fc203-110">Current</span></span>               |
| <span data-ttu-id="fc203-111">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="fc203-111">Mozilla Firefox</span></span>                  | <span data-ttu-id="fc203-112">Aktivní</span><span class="sxs-lookup"><span data-stu-id="fc203-112">Current</span></span>               |
| <span data-ttu-id="fc203-113">Google Chrome, včetně Androidu</span><span class="sxs-lookup"><span data-stu-id="fc203-113">Google Chrome, including Android</span></span> | <span data-ttu-id="fc203-114">Aktivní</span><span class="sxs-lookup"><span data-stu-id="fc203-114">Current</span></span>               |
| <span data-ttu-id="fc203-115">Safari, včetně iOS</span><span class="sxs-lookup"><span data-stu-id="fc203-115">Safari, including iOS</span></span>            | <span data-ttu-id="fc203-116">Aktivní</span><span class="sxs-lookup"><span data-stu-id="fc203-116">Current</span></span>               |
| <span data-ttu-id="fc203-117">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="fc203-117">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="fc203-118">Nepodporováno&dagger;</span><span class="sxs-lookup"><span data-stu-id="fc203-118">Not Supported&dagger;</span></span> |

<span data-ttu-id="fc203-119">&dagger;Microsoft Internet Explorer nepodporuje [WebAssembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="fc203-119">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### <a name="opno-locblazor-server"></a><span data-ttu-id="fc203-120">Server Blazor</span><span class="sxs-lookup"><span data-stu-id="fc203-120">Blazor Server</span></span>

| <span data-ttu-id="fc203-121">Prohlížeee</span><span class="sxs-lookup"><span data-stu-id="fc203-121">Browser</span></span>                          | <span data-ttu-id="fc203-122">Version</span><span class="sxs-lookup"><span data-stu-id="fc203-122">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="fc203-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="fc203-123">Microsoft Edge</span></span>                   | <span data-ttu-id="fc203-124">Aktivní</span><span class="sxs-lookup"><span data-stu-id="fc203-124">Current</span></span>    |
| <span data-ttu-id="fc203-125">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="fc203-125">Mozilla Firefox</span></span>                  | <span data-ttu-id="fc203-126">Aktivní</span><span class="sxs-lookup"><span data-stu-id="fc203-126">Current</span></span>    |
| <span data-ttu-id="fc203-127">Google Chrome, včetně Androidu</span><span class="sxs-lookup"><span data-stu-id="fc203-127">Google Chrome, including Android</span></span> | <span data-ttu-id="fc203-128">Aktivní</span><span class="sxs-lookup"><span data-stu-id="fc203-128">Current</span></span>    |
| <span data-ttu-id="fc203-129">Safari, včetně iOS</span><span class="sxs-lookup"><span data-stu-id="fc203-129">Safari, including iOS</span></span>            | <span data-ttu-id="fc203-130">Aktivní</span><span class="sxs-lookup"><span data-stu-id="fc203-130">Current</span></span>    |
| <span data-ttu-id="fc203-131">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="fc203-131">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="fc203-132">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="fc203-132">11&dagger;</span></span> |

<span data-ttu-id="fc203-133">&dagger;vyžadovány další obslužné nabídky (například příslibů lze přidat prostřednictvím [Polyfill.IO](https://polyfill.io/v3/) sady).</span><span class="sxs-lookup"><span data-stu-id="fc203-133">&dagger;Additional polyfills are required (for example, promises can be added via a [Polyfill.io](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fc203-134">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fc203-134">Additional resources</span></span>

* <xref:blazor/hosting-models>
