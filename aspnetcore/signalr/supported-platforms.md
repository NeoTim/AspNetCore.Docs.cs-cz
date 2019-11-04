---
title: Podporované platformy pro přijímače ASP.NET Core
author: bradygaster
description: Přečtěte si o podporovaných platformách pro ASP.NET Core Signal.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/01/2019
uid: signalr/supported-platforms
ms.openlocfilehash: 1be7a307710e6e522c0088fd1ca01da11a13eda1
ms.sourcegitcommit: 77c8be22d5e88dd710f42c739748869f198865dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426969"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="df033-103">Podporované platformy pro přijímače ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="df033-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="df033-104">Požadavky na systém serveru</span><span class="sxs-lookup"><span data-stu-id="df033-104">Server system requirements</span></span>

<span data-ttu-id="df033-105">Návěstí pro ASP.NET Core podporuje jakoukoli serverovou platformu, která ASP.NET Core podporuje.</span><span class="sxs-lookup"><span data-stu-id="df033-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="df033-106">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="df033-106">JavaScript client</span></span>

<span data-ttu-id="df033-107">[Klient jazyka JavaScript](https://www.npmjs.com/package/@aspnet/signalr) běží na NodeJS 8 a novějších verzích a v následujících prohlížečích:</span><span class="sxs-lookup"><span data-stu-id="df033-107">The [JavaScript client](https://www.npmjs.com/package/@aspnet/signalr) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="df033-108">Prohlížeee</span><span class="sxs-lookup"><span data-stu-id="df033-108">Browser</span></span>                         | <span data-ttu-id="df033-109">Version</span><span class="sxs-lookup"><span data-stu-id="df033-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="df033-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="df033-110">Microsoft Edge</span></span>                  | <span data-ttu-id="df033-111">Aktuální&dagger;</span><span class="sxs-lookup"><span data-stu-id="df033-111">Current&dagger;</span></span> |
| <span data-ttu-id="df033-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="df033-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="df033-113">Aktuální&dagger;</span><span class="sxs-lookup"><span data-stu-id="df033-113">Current&dagger;</span></span> |
| <span data-ttu-id="df033-114">Google Chrome; zahrnuje Android</span><span class="sxs-lookup"><span data-stu-id="df033-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="df033-115">Aktuální&dagger;</span><span class="sxs-lookup"><span data-stu-id="df033-115">Current&dagger;</span></span> |
| <span data-ttu-id="df033-116">Prohlížeče zahrnuje iOS</span><span class="sxs-lookup"><span data-stu-id="df033-116">Safari; includes iOS</span></span>            | <span data-ttu-id="df033-117">Aktuální&dagger;</span><span class="sxs-lookup"><span data-stu-id="df033-117">Current&dagger;</span></span> |
| <span data-ttu-id="df033-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="df033-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="df033-119">odst</span><span class="sxs-lookup"><span data-stu-id="df033-119">11</span></span>              |

<span data-ttu-id="df033-120">&dagger;*aktuální* odkazuje na nejnovější verzi prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="df033-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="df033-121">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="df033-121">.NET client</span></span>

<span data-ttu-id="df033-122">[Klient .NET](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) běží na libovolné platformě podporované nástrojem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="df033-122">The [.NET client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="df033-123">[Vývojáři Xamarin můžou například použít signalizaci](https://github.com/aspnet/Announcements/issues/305) pro sestavování aplikací pro Android pomocí Xamarin. Android 8.4.0.1 a novějších aplikací pro iOS pomocí Xamarin. iOS 11.14.0.4 a novějších.</span><span class="sxs-lookup"><span data-stu-id="df033-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="df033-124">Pokud server používá službu IIS, přenos pomocí protokolu WebSockets vyžaduje službu IIS 8,0 nebo novější v systému Windows Server 2012 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="df033-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="df033-125">Další přenosy jsou podporované na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="df033-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="df033-126">Klient Java</span><span class="sxs-lookup"><span data-stu-id="df033-126">Java client</span></span>

<span data-ttu-id="df033-127">[Klient Java](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) podporuje Java 8 a novější verze.</span><span class="sxs-lookup"><span data-stu-id="df033-127">The [Java client](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="df033-128">Nepodporované klienty</span><span class="sxs-lookup"><span data-stu-id="df033-128">Unsupported clients</span></span>

<span data-ttu-id="df033-129">Následující klienti jsou k dispozici, ale jsou experimentální nebo neoficiální.</span><span class="sxs-lookup"><span data-stu-id="df033-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="df033-130">Nejsou aktuálně podporované a nemusí být nikdy.</span><span class="sxs-lookup"><span data-stu-id="df033-130">They aren't currently supported and may never be.</span></span>

* [<span data-ttu-id="df033-131">C++Služba</span><span class="sxs-lookup"><span data-stu-id="df033-131">C++ client</span></span>](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [<span data-ttu-id="df033-132">Klient SWIFT</span><span class="sxs-lookup"><span data-stu-id="df033-132">Swift client</span></span>](https://github.com/moozzyk/SignalR-Client-Swift)
