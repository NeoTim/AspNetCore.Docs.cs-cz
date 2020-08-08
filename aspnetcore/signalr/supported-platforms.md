---
title: ASP.NET Core SignalR podporované platformy
author: bradygaster
description: Přečtěte si o podporovaných platformách pro ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/supported-platforms
ms.openlocfilehash: e6989f431d0d72241a6e1b94759585c02ef7398f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021546"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="26b6e-103">ASP.NET Core SignalR podporované platformy</span><span class="sxs-lookup"><span data-stu-id="26b6e-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="26b6e-104">Požadavky na systém serveru</span><span class="sxs-lookup"><span data-stu-id="26b6e-104">Server system requirements</span></span>

<span data-ttu-id="26b6e-105">SignalRpro ASP.NET Core podporuje jakoukoli serverovou platformu, která ASP.NET Core podporuje.</span><span class="sxs-lookup"><span data-stu-id="26b6e-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="26b6e-106">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="26b6e-106">JavaScript client</span></span>

<span data-ttu-id="26b6e-107">[Klient jazyka JavaScript](xref:signalr/javascript-client) běží na NodeJS 8 a novějších verzích a v následujících prohlížečích:</span><span class="sxs-lookup"><span data-stu-id="26b6e-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="26b6e-108">Prohlížeč</span><span class="sxs-lookup"><span data-stu-id="26b6e-108">Browser</span></span>                         | <span data-ttu-id="26b6e-109">Verze</span><span class="sxs-lookup"><span data-stu-id="26b6e-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="26b6e-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="26b6e-110">Microsoft Edge</span></span>                  | <span data-ttu-id="26b6e-111">Aktivní&dagger;</span><span class="sxs-lookup"><span data-stu-id="26b6e-111">Current&dagger;</span></span> |
| <span data-ttu-id="26b6e-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="26b6e-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="26b6e-113">Aktivní&dagger;</span><span class="sxs-lookup"><span data-stu-id="26b6e-113">Current&dagger;</span></span> |
| <span data-ttu-id="26b6e-114">Google Chrome; zahrnuje Android</span><span class="sxs-lookup"><span data-stu-id="26b6e-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="26b6e-115">Aktivní&dagger;</span><span class="sxs-lookup"><span data-stu-id="26b6e-115">Current&dagger;</span></span> |
| <span data-ttu-id="26b6e-116">Prohlížeče zahrnuje iOS</span><span class="sxs-lookup"><span data-stu-id="26b6e-116">Safari; includes iOS</span></span>            | <span data-ttu-id="26b6e-117">Aktivní&dagger;</span><span class="sxs-lookup"><span data-stu-id="26b6e-117">Current&dagger;</span></span> |
| <span data-ttu-id="26b6e-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="26b6e-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="26b6e-119">11</span><span class="sxs-lookup"><span data-stu-id="26b6e-119">11</span></span>              |

<span data-ttu-id="26b6e-120">&dagger;*Aktuální* odkazuje na nejnovější verzi prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="26b6e-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="26b6e-121">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="26b6e-121">.NET client</span></span>

<span data-ttu-id="26b6e-122">[Klient .NET](xref:signalr/dotnet-client) běží na libovolné platformě podporované nástrojem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="26b6e-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="26b6e-123">[Vývojáři Xamarin můžou například SignalR použít](https://github.com/aspnet/Announcements/issues/305) k sestavování aplikací pro Android pomocí Xamarin. Android 8.4.0.1 a novějších aplikací pro iOS pomocí Xamarin. iOS 11.14.0.4 a novějších.</span><span class="sxs-lookup"><span data-stu-id="26b6e-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="26b6e-124">Pokud server používá službu IIS, přenos pomocí protokolu WebSockets vyžaduje službu IIS 8,0 nebo novější v systému Windows Server 2012 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="26b6e-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="26b6e-125">Další přenosy jsou podporované na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="26b6e-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="26b6e-126">Klient Java</span><span class="sxs-lookup"><span data-stu-id="26b6e-126">Java client</span></span>

<span data-ttu-id="26b6e-127">[Klient Java](xref:signalr/java-client) podporuje Java 8 a novější verze.</span><span class="sxs-lookup"><span data-stu-id="26b6e-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="26b6e-128">Nepodporované klienty</span><span class="sxs-lookup"><span data-stu-id="26b6e-128">Unsupported clients</span></span>

<span data-ttu-id="26b6e-129">Následující klienti jsou k dispozici, ale jsou experimentální nebo neoficiální.</span><span class="sxs-lookup"><span data-stu-id="26b6e-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="26b6e-130">Nejsou aktuálně podporované a nemusí být nikdy.</span><span class="sxs-lookup"><span data-stu-id="26b6e-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="26b6e-131">[Klient C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="26b6e-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="26b6e-132">[Klient SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="26b6e-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
