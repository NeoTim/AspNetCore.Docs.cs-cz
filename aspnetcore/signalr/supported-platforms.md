---
title: ASP.NET Core SignalR podporované platformy
author: bradygaster
description: Přečtěte si o podporovaných platformách SignalRpro ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 70a05dabb95aaf561aa78d5c8b24b430c51bd973
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772602"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="a71f6-103">Podporované platformy pro přijímače ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a71f6-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="a71f6-104">Požadavky na systém serveru</span><span class="sxs-lookup"><span data-stu-id="a71f6-104">Server system requirements</span></span>

<span data-ttu-id="a71f6-105">Návěstí pro ASP.NET Core podporuje jakoukoli serverovou platformu, která ASP.NET Core podporuje.</span><span class="sxs-lookup"><span data-stu-id="a71f6-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="a71f6-106">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="a71f6-106">JavaScript client</span></span>

<span data-ttu-id="a71f6-107">[Klient jazyka JavaScript](xref:signalr/javascript-client) běží na NodeJS 8 a novějších verzích a v následujících prohlížečích:</span><span class="sxs-lookup"><span data-stu-id="a71f6-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="a71f6-108">Prohlížeč</span><span class="sxs-lookup"><span data-stu-id="a71f6-108">Browser</span></span>                         | <span data-ttu-id="a71f6-109">Verze</span><span class="sxs-lookup"><span data-stu-id="a71f6-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="a71f6-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="a71f6-110">Microsoft Edge</span></span>                  | <span data-ttu-id="a71f6-111">Aktivní&dagger;</span><span class="sxs-lookup"><span data-stu-id="a71f6-111">Current&dagger;</span></span> |
| <span data-ttu-id="a71f6-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="a71f6-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="a71f6-113">Aktivní&dagger;</span><span class="sxs-lookup"><span data-stu-id="a71f6-113">Current&dagger;</span></span> |
| <span data-ttu-id="a71f6-114">Google Chrome; zahrnuje Android</span><span class="sxs-lookup"><span data-stu-id="a71f6-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="a71f6-115">Aktivní&dagger;</span><span class="sxs-lookup"><span data-stu-id="a71f6-115">Current&dagger;</span></span> |
| <span data-ttu-id="a71f6-116">Prohlížeče zahrnuje iOS</span><span class="sxs-lookup"><span data-stu-id="a71f6-116">Safari; includes iOS</span></span>            | <span data-ttu-id="a71f6-117">Aktivní&dagger;</span><span class="sxs-lookup"><span data-stu-id="a71f6-117">Current&dagger;</span></span> |
| <span data-ttu-id="a71f6-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="a71f6-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="a71f6-119">11</span><span class="sxs-lookup"><span data-stu-id="a71f6-119">11</span></span>              |

<span data-ttu-id="a71f6-120">&dagger;*Aktuální* odkazuje na nejnovější verzi prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a71f6-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="a71f6-121">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="a71f6-121">.NET client</span></span>

<span data-ttu-id="a71f6-122">[Klient .NET](xref:signalr/dotnet-client) běží na libovolné platformě podporované nástrojem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a71f6-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="a71f6-123">[Vývojáři Xamarin můžou například SignalR použít](https://github.com/aspnet/Announcements/issues/305) k sestavování aplikací pro Android pomocí Xamarin. Android 8.4.0.1 a novějších aplikací pro iOS pomocí Xamarin. iOS 11.14.0.4 a novějších.</span><span class="sxs-lookup"><span data-stu-id="a71f6-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="a71f6-124">Pokud server používá službu IIS, přenos pomocí protokolu WebSockets vyžaduje službu IIS 8,0 nebo novější v systému Windows Server 2012 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="a71f6-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="a71f6-125">Další přenosy jsou podporované na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="a71f6-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="a71f6-126">Klient Java</span><span class="sxs-lookup"><span data-stu-id="a71f6-126">Java client</span></span>

<span data-ttu-id="a71f6-127">[Klient Java](xref:signalr/java-client) podporuje Java 8 a novější verze.</span><span class="sxs-lookup"><span data-stu-id="a71f6-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="a71f6-128">Nepodporované klienty</span><span class="sxs-lookup"><span data-stu-id="a71f6-128">Unsupported clients</span></span>

<span data-ttu-id="a71f6-129">Následující klienti jsou k dispozici, ale jsou experimentální nebo neoficiální.</span><span class="sxs-lookup"><span data-stu-id="a71f6-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="a71f6-130">Nejsou aktuálně podporované a nemusí být nikdy.</span><span class="sxs-lookup"><span data-stu-id="a71f6-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="a71f6-131">[Klient C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="a71f6-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="a71f6-132">[Klient SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="a71f6-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
