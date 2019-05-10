---
title: Funkce SignalR technologie ASP.NET Core podporované platformy
author: bradygaster
description: Další informace o podporovaných platforem pro funkci SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/06/2019
uid: signalr/supported-platforms
ms.openlocfilehash: fefaaf97de3f1fabf8f3154bf5b4ccb37195ccff
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898623"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="89c30-103">Funkce SignalR technologie ASP.NET Core podporované platformy</span><span class="sxs-lookup"><span data-stu-id="89c30-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="89c30-104">Požadavky na systém Server</span><span class="sxs-lookup"><span data-stu-id="89c30-104">Server system requirements</span></span>

<span data-ttu-id="89c30-105">Funkce SignalR technologie ASP.NET Core podporuje serverová platforma, která podporuje ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="89c30-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="89c30-106">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="89c30-106">JavaScript client</span></span>

<span data-ttu-id="89c30-107">[Javascriptový klient](https://www.npmjs.com/package/@aspnet/signalr) běží v prostředí NodeJS 8 a novějších verzí a následujících prohlížečů:</span><span class="sxs-lookup"><span data-stu-id="89c30-107">The [JavaScript client](https://www.npmjs.com/package/@aspnet/signalr) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="89c30-108">Prohlížeč</span><span class="sxs-lookup"><span data-stu-id="89c30-108">Browser</span></span>                         | <span data-ttu-id="89c30-109">Version</span><span class="sxs-lookup"><span data-stu-id="89c30-109">Version</span></span> |
| ------------------------------- | ------- |
| <span data-ttu-id="89c30-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="89c30-110">Microsoft Edge</span></span>                  | <span data-ttu-id="89c30-111">aktuální</span><span class="sxs-lookup"><span data-stu-id="89c30-111">current</span></span> |
| <span data-ttu-id="89c30-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="89c30-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="89c30-113">aktuální</span><span class="sxs-lookup"><span data-stu-id="89c30-113">current</span></span> |
| <span data-ttu-id="89c30-114">Google Chrome; zahrnuje Android</span><span class="sxs-lookup"><span data-stu-id="89c30-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="89c30-115">aktuální</span><span class="sxs-lookup"><span data-stu-id="89c30-115">current</span></span> |
| <span data-ttu-id="89c30-116">Safari; zahrnuje iOS</span><span class="sxs-lookup"><span data-stu-id="89c30-116">Safari; includes iOS</span></span>            | <span data-ttu-id="89c30-117">aktuální</span><span class="sxs-lookup"><span data-stu-id="89c30-117">current</span></span> |
| <span data-ttu-id="89c30-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="89c30-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="89c30-119">11</span><span class="sxs-lookup"><span data-stu-id="89c30-119">11</span></span>      |
 
## <a name="net-client"></a><span data-ttu-id="89c30-120">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="89c30-120">.NET client</span></span>

<span data-ttu-id="89c30-121">[Klienta .NET](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) běží na žádnou platformu podporovanou nástrojem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="89c30-121">The [.NET client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="89c30-122">Například [Xamarin mohou vývojáři SignalR](https://github.com/aspnet/Announcements/issues/305) pro vytváření aplikací pro Android pomocí Xamarin.Android 8.4.0.1 a později a aplikace pro iOS pomocí Xamarin.iOS 11.14.0.4 a novější.</span><span class="sxs-lookup"><span data-stu-id="89c30-122">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="89c30-123">Pokud na serveru běží služby IIS, vyžaduje přenos objekty Websocket služby IIS 8.0 nebo novější na systému Windows Server 2012 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="89c30-123">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="89c30-124">Jiné přenosy jsou podporované na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="89c30-124">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="89c30-125">Klient Java</span><span class="sxs-lookup"><span data-stu-id="89c30-125">Java client</span></span>

<span data-ttu-id="89c30-126">[Klientskou sadou Java](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) podporuje Javou 8 a novější verze.</span><span class="sxs-lookup"><span data-stu-id="89c30-126">The [Java client](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="89c30-127">Nepodporovaná klientů</span><span class="sxs-lookup"><span data-stu-id="89c30-127">Unsupported clients</span></span>

<span data-ttu-id="89c30-128">Následující klienti jsou k dispozici, ale jsou experimentální nebo neoficiální.</span><span class="sxs-lookup"><span data-stu-id="89c30-128">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="89c30-129">Tyto nejsou aktuálně podporovány a mohou být nikdy.</span><span class="sxs-lookup"><span data-stu-id="89c30-129">They aren't currently supported and may never be.</span></span>

* [<span data-ttu-id="89c30-130">Kódu klienta jazyka C++</span><span class="sxs-lookup"><span data-stu-id="89c30-130">C++ client</span></span>](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [<span data-ttu-id="89c30-131">SWIFT klienta</span><span class="sxs-lookup"><span data-stu-id="89c30-131">Swift client</span></span>](https://github.com/moozzyk/SignalR-Client-Swift)
