---
title: Funkce SignalR technologie ASP.NET Core podporované platformy
author: tdykstra
description: Další informace o podporovaných platforem pro funkci SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 10/31/2018
uid: signalr/supported-platforms
ms.openlocfilehash: 773f6c020dbb2982911e177b55855473c750d52a
ms.sourcegitcommit: fc2486ddbeb15ab4969168d99b3fe0fbe91e8661
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50758177"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="b228d-103">Funkce SignalR technologie ASP.NET Core podporované platformy</span><span class="sxs-lookup"><span data-stu-id="b228d-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="b228d-104">Požadavky na systém Server</span><span class="sxs-lookup"><span data-stu-id="b228d-104">Server system requirements</span></span>

<span data-ttu-id="b228d-105">Funkce SignalR technologie ASP.NET Core podporuje serverová platforma, která podporuje ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b228d-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="b228d-106">Javascriptový klient</span><span class="sxs-lookup"><span data-stu-id="b228d-106">JavaScript client</span></span>

<span data-ttu-id="b228d-107">[Javascriptový klient](https://www.npmjs.com/package/@aspnet/signalr) běží v prostředí NodeJS 8 a novějších verzí a následujících prohlížečů:</span><span class="sxs-lookup"><span data-stu-id="b228d-107">The [JavaScript client](https://www.npmjs.com/package/@aspnet/signalr) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="b228d-108">Prohlížeč</span><span class="sxs-lookup"><span data-stu-id="b228d-108">Browser</span></span>                         | <span data-ttu-id="b228d-109">Version</span><span class="sxs-lookup"><span data-stu-id="b228d-109">Version</span></span> |
| ------------------------------- | ------- |
| <span data-ttu-id="b228d-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="b228d-110">Microsoft Edge</span></span>                  | <span data-ttu-id="b228d-111">aktuální</span><span class="sxs-lookup"><span data-stu-id="b228d-111">current</span></span> |
| <span data-ttu-id="b228d-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="b228d-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="b228d-113">aktuální</span><span class="sxs-lookup"><span data-stu-id="b228d-113">current</span></span> |
| <span data-ttu-id="b228d-114">Google Chrome; zahrnuje Android</span><span class="sxs-lookup"><span data-stu-id="b228d-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="b228d-115">aktuální</span><span class="sxs-lookup"><span data-stu-id="b228d-115">current</span></span> |
| <span data-ttu-id="b228d-116">Safari; zahrnuje iOS</span><span class="sxs-lookup"><span data-stu-id="b228d-116">Safari; includes iOS</span></span>            | <span data-ttu-id="b228d-117">aktuální</span><span class="sxs-lookup"><span data-stu-id="b228d-117">current</span></span> |
| <span data-ttu-id="b228d-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="b228d-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="b228d-119">11</span><span class="sxs-lookup"><span data-stu-id="b228d-119">11</span></span>      |
 
## <a name="net-client"></a><span data-ttu-id="b228d-120">.NET client</span><span class="sxs-lookup"><span data-stu-id="b228d-120">.NET client</span></span>

<span data-ttu-id="b228d-121">[Klienta .NET](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) běží na libovolné platformě server podporuje ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b228d-121">The [.NET client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) runs on any server platform supported by ASP.NET Core.</span></span>

<span data-ttu-id="b228d-122">Pokud na serveru běží služby IIS, přenosové protokoly Websocket vyžaduje službu IIS 8.0 nebo vyšší na Windows Server 2012 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="b228d-122">If the server runs IIS, the WebSockets transport requires IIS 8.0 or higher on Windows Server 2012 or higher.</span></span> <span data-ttu-id="b228d-123">Jiné přenosy jsou podporované na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="b228d-123">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="b228d-124">Klientskou sadou Java</span><span class="sxs-lookup"><span data-stu-id="b228d-124">Java client</span></span>

<span data-ttu-id="b228d-125">[Klientskou sadou Java](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) podporuje Javou 8 a novější verze.</span><span class="sxs-lookup"><span data-stu-id="b228d-125">The [Java client](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="b228d-126">Nepodporovaná klientů</span><span class="sxs-lookup"><span data-stu-id="b228d-126">Unsupported clients</span></span>

<span data-ttu-id="b228d-127">Následující klienti jsou k dispozici, ale jsou experimentální nebo neoficiální.</span><span class="sxs-lookup"><span data-stu-id="b228d-127">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="b228d-128">Tyto nejsou aktuálně podporovány a mohou být nikdy.</span><span class="sxs-lookup"><span data-stu-id="b228d-128">They aren't currently supported and may never be.</span></span>

* [<span data-ttu-id="b228d-129">Kódu klienta jazyka C++</span><span class="sxs-lookup"><span data-stu-id="b228d-129">C++ client</span></span>](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [<span data-ttu-id="b228d-130">SWIFT klienta</span><span class="sxs-lookup"><span data-stu-id="b228d-130">Swift client</span></span>](https://github.com/moozzyk/SignalR-Client-Swift)
