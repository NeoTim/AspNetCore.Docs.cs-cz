---
title: ASP.NET Blazor platformy podporované core
author: guardrex
description: Další informace o podporovaných Blazorplatformách pro ASP.NET Core .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658856"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="930f6-103">platformy podporované ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="930f6-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="930f6-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="930f6-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a><span data-ttu-id="930f6-105">Požadavky na prohlížeč</span><span class="sxs-lookup"><span data-stu-id="930f6-105">Browser requirements</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="930f6-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="930f6-106">Blazor WebAssembly</span></span>

| <span data-ttu-id="930f6-107">Prohlížeč</span><span class="sxs-lookup"><span data-stu-id="930f6-107">Browser</span></span>                          | <span data-ttu-id="930f6-108">Version</span><span class="sxs-lookup"><span data-stu-id="930f6-108">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="930f6-109">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="930f6-109">Microsoft Edge</span></span>                   | <span data-ttu-id="930f6-110">Current</span><span class="sxs-lookup"><span data-stu-id="930f6-110">Current</span></span>               |
| <span data-ttu-id="930f6-111">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="930f6-111">Mozilla Firefox</span></span>                  | <span data-ttu-id="930f6-112">Current</span><span class="sxs-lookup"><span data-stu-id="930f6-112">Current</span></span>               |
| <span data-ttu-id="930f6-113">Google Chrome, včetně Androidu</span><span class="sxs-lookup"><span data-stu-id="930f6-113">Google Chrome, including Android</span></span> | <span data-ttu-id="930f6-114">Current</span><span class="sxs-lookup"><span data-stu-id="930f6-114">Current</span></span>               |
| <span data-ttu-id="930f6-115">Safari, včetně iOS</span><span class="sxs-lookup"><span data-stu-id="930f6-115">Safari, including iOS</span></span>            | <span data-ttu-id="930f6-116">Current</span><span class="sxs-lookup"><span data-stu-id="930f6-116">Current</span></span>               |
| <span data-ttu-id="930f6-117">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="930f6-117">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="930f6-118">Nepodporuje se&dagger;</span><span class="sxs-lookup"><span data-stu-id="930f6-118">Not Supported&dagger;</span></span> |

<span data-ttu-id="930f6-119">&dagger;Aplikace Microsoft Internet Explorer nepodporuje [webovou sestavu](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="930f6-119">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### <a name="blazor-server"></a><span data-ttu-id="930f6-120">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="930f6-120">Blazor Server</span></span>

| <span data-ttu-id="930f6-121">Prohlížeč</span><span class="sxs-lookup"><span data-stu-id="930f6-121">Browser</span></span>                          | <span data-ttu-id="930f6-122">Version</span><span class="sxs-lookup"><span data-stu-id="930f6-122">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="930f6-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="930f6-123">Microsoft Edge</span></span>                   | <span data-ttu-id="930f6-124">Current</span><span class="sxs-lookup"><span data-stu-id="930f6-124">Current</span></span>    |
| <span data-ttu-id="930f6-125">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="930f6-125">Mozilla Firefox</span></span>                  | <span data-ttu-id="930f6-126">Current</span><span class="sxs-lookup"><span data-stu-id="930f6-126">Current</span></span>    |
| <span data-ttu-id="930f6-127">Google Chrome, včetně Androidu</span><span class="sxs-lookup"><span data-stu-id="930f6-127">Google Chrome, including Android</span></span> | <span data-ttu-id="930f6-128">Current</span><span class="sxs-lookup"><span data-stu-id="930f6-128">Current</span></span>    |
| <span data-ttu-id="930f6-129">Safari, včetně iOS</span><span class="sxs-lookup"><span data-stu-id="930f6-129">Safari, including iOS</span></span>            | <span data-ttu-id="930f6-130">Current</span><span class="sxs-lookup"><span data-stu-id="930f6-130">Current</span></span>    |
| <span data-ttu-id="930f6-131">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="930f6-131">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="930f6-132">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="930f6-132">11&dagger;</span></span> |

<span data-ttu-id="930f6-133">&dagger;Jsou vyžadovány další polyfilly (například sliby lze přidat prostřednictvím [Polyfill.io](https://polyfill.io/v3/) svazku).</span><span class="sxs-lookup"><span data-stu-id="930f6-133">&dagger;Additional polyfills are required (for example, promises can be added via a [Polyfill.io](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="930f6-134">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="930f6-134">Additional resources</span></span>

* <xref:blazor/hosting-models>
