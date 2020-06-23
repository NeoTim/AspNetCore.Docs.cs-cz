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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 99f14486711c2dd2a634bc51b27a8e3891deee1a
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243223"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="73166-103">ASP.NET Core Blazor podporované platformy</span><span class="sxs-lookup"><span data-stu-id="73166-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="73166-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="73166-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="73166-105">Požadavky na prohlížeč</span><span class="sxs-lookup"><span data-stu-id="73166-105">Browser requirements</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="73166-106">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="73166-106"> WebAssembly</span></span>

| <span data-ttu-id="73166-107">Prohlížeč</span><span class="sxs-lookup"><span data-stu-id="73166-107">Browser</span></span>                          | <span data-ttu-id="73166-108">Verze</span><span class="sxs-lookup"><span data-stu-id="73166-108">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="73166-109">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="73166-109">Microsoft Edge</span></span>                   | <span data-ttu-id="73166-110">Current</span><span class="sxs-lookup"><span data-stu-id="73166-110">Current</span></span>               |
| <span data-ttu-id="73166-111">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="73166-111">Mozilla Firefox</span></span>                  | <span data-ttu-id="73166-112">Current</span><span class="sxs-lookup"><span data-stu-id="73166-112">Current</span></span>               |
| <span data-ttu-id="73166-113">Google Chrome, včetně Androidu</span><span class="sxs-lookup"><span data-stu-id="73166-113">Google Chrome, including Android</span></span> | <span data-ttu-id="73166-114">Current</span><span class="sxs-lookup"><span data-stu-id="73166-114">Current</span></span>               |
| <span data-ttu-id="73166-115">Safari, včetně iOS</span><span class="sxs-lookup"><span data-stu-id="73166-115">Safari, including iOS</span></span>            | <span data-ttu-id="73166-116">Current</span><span class="sxs-lookup"><span data-stu-id="73166-116">Current</span></span>               |
| <span data-ttu-id="73166-117">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="73166-117">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="73166-118">Nepodporováno&dagger;</span><span class="sxs-lookup"><span data-stu-id="73166-118">Not Supported&dagger;</span></span> |

<span data-ttu-id="73166-119">&dagger;Aplikace Microsoft Internet Explorer nepodporuje [WebAssembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="73166-119">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### <a name="blazor-server"></a>Blazor<span data-ttu-id="73166-120">WebServer</span><span class="sxs-lookup"><span data-stu-id="73166-120"> Server</span></span>

| <span data-ttu-id="73166-121">Prohlížeč</span><span class="sxs-lookup"><span data-stu-id="73166-121">Browser</span></span>                          | <span data-ttu-id="73166-122">Verze</span><span class="sxs-lookup"><span data-stu-id="73166-122">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="73166-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="73166-123">Microsoft Edge</span></span>                   | <span data-ttu-id="73166-124">Current</span><span class="sxs-lookup"><span data-stu-id="73166-124">Current</span></span>    |
| <span data-ttu-id="73166-125">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="73166-125">Mozilla Firefox</span></span>                  | <span data-ttu-id="73166-126">Current</span><span class="sxs-lookup"><span data-stu-id="73166-126">Current</span></span>    |
| <span data-ttu-id="73166-127">Google Chrome, včetně Androidu</span><span class="sxs-lookup"><span data-stu-id="73166-127">Google Chrome, including Android</span></span> | <span data-ttu-id="73166-128">Current</span><span class="sxs-lookup"><span data-stu-id="73166-128">Current</span></span>    |
| <span data-ttu-id="73166-129">Safari, včetně iOS</span><span class="sxs-lookup"><span data-stu-id="73166-129">Safari, including iOS</span></span>            | <span data-ttu-id="73166-130">Current</span><span class="sxs-lookup"><span data-stu-id="73166-130">Current</span></span>    |
| <span data-ttu-id="73166-131">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="73166-131">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="73166-132">odst&dagger;</span><span class="sxs-lookup"><span data-stu-id="73166-132">11&dagger;</span></span> |

<span data-ttu-id="73166-133">&dagger;Jsou vyžadovány další obslužné nabídky (například příslibů lze přidat prostřednictvím [`Polyfill.io`](https://polyfill.io/v3/) sady prostředků).</span><span class="sxs-lookup"><span data-stu-id="73166-133">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73166-134">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="73166-134">Additional resources</span></span>

* <xref:blazor/hosting-models>
