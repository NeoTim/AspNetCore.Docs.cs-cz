---
title: Podporované platformy ASP.NET Core SignalR
author: bradygaster
description: Přečtěte si o podporovaných platformách pro ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 054965921c87c1a9be27e5ddaa8a87b0fa1f4113
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146495"
---
# <a name="aspnet-core-opno-locsignalr-supported-platforms"></a><span data-ttu-id="79939-103">Podporované platformy ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="79939-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="79939-104">Požadavky na systém serveru</span><span class="sxs-lookup"><span data-stu-id="79939-104">Server system requirements</span></span>

SignalR<span data-ttu-id="79939-105"> pro ASP.NET Core podporuje jakoukoli serverovou platformu, která ASP.NET Core podporuje.</span><span class="sxs-lookup"><span data-stu-id="79939-105"> for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="79939-106">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="79939-106">JavaScript client</span></span>

<span data-ttu-id="79939-107">[Klient jazyka JavaScript](xref:signalr/javascript-client) běží na NodeJS 8 a novějších verzích a v následujících prohlížečích:</span><span class="sxs-lookup"><span data-stu-id="79939-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="79939-108">Prohlížeč</span><span class="sxs-lookup"><span data-stu-id="79939-108">Browser</span></span>                         | <span data-ttu-id="79939-109">Version</span><span class="sxs-lookup"><span data-stu-id="79939-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="79939-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="79939-110">Microsoft Edge</span></span>                  | <span data-ttu-id="79939-111">Aktuální&dagger;</span><span class="sxs-lookup"><span data-stu-id="79939-111">Current&dagger;</span></span> |
| <span data-ttu-id="79939-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="79939-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="79939-113">Aktuální&dagger;</span><span class="sxs-lookup"><span data-stu-id="79939-113">Current&dagger;</span></span> |
| <span data-ttu-id="79939-114">Google Chrome; zahrnuje Android</span><span class="sxs-lookup"><span data-stu-id="79939-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="79939-115">Aktuální&dagger;</span><span class="sxs-lookup"><span data-stu-id="79939-115">Current&dagger;</span></span> |
| <span data-ttu-id="79939-116">Prohlížeče zahrnuje iOS</span><span class="sxs-lookup"><span data-stu-id="79939-116">Safari; includes iOS</span></span>            | <span data-ttu-id="79939-117">Aktuální&dagger;</span><span class="sxs-lookup"><span data-stu-id="79939-117">Current&dagger;</span></span> |
| <span data-ttu-id="79939-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="79939-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="79939-119">11</span><span class="sxs-lookup"><span data-stu-id="79939-119">11</span></span>              |

<span data-ttu-id="79939-120">&dagger;*aktuální* odkazuje na nejnovější verzi prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="79939-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="79939-121">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="79939-121">.NET client</span></span>

<span data-ttu-id="79939-122">[Klient .NET](xref:signalr/dotnet-client) běží na libovolné platformě podporované nástrojem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="79939-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="79939-123">[Vývojáři Xamarin můžou například použít SignalR](https://github.com/aspnet/Announcements/issues/305) pro sestavování aplikací pro Android pomocí aplikací Xamarin. Android 8.4.0.1 a novějších a iOS s použitím Xamarin. iOS 11.14.0.4 a novějších.</span><span class="sxs-lookup"><span data-stu-id="79939-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="79939-124">Pokud server používá službu IIS, přenos pomocí protokolu WebSockets vyžaduje službu IIS 8,0 nebo novější v systému Windows Server 2012 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="79939-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="79939-125">Další přenosy jsou podporované na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="79939-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="79939-126">Klient Java</span><span class="sxs-lookup"><span data-stu-id="79939-126">Java client</span></span>

<span data-ttu-id="79939-127">[Klient Java](xref:signalr/java-client) podporuje Java 8 a novější verze.</span><span class="sxs-lookup"><span data-stu-id="79939-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="79939-128">Nepodporované klienty</span><span class="sxs-lookup"><span data-stu-id="79939-128">Unsupported clients</span></span>

<span data-ttu-id="79939-129">Následující klienti jsou k dispozici, ale jsou experimentální nebo neoficiální.</span><span class="sxs-lookup"><span data-stu-id="79939-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="79939-130">Nejsou aktuálně podporované a nemusí být nikdy.</span><span class="sxs-lookup"><span data-stu-id="79939-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="79939-131">[C++Služba](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="79939-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="79939-132">[Klient SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="79939-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
