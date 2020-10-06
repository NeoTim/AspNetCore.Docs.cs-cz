---
title: ASP.NET Core SignalR podporované platformy
author: bradygaster
description: Přečtěte si o podporovaných platformách pro ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- ASP.NET Core Identity
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
ms.openlocfilehash: a342dd787eceadd22ac26b57a3615a6b0b21f461
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754499"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="73cec-103">ASP.NET Core SignalR podporované platformy</span><span class="sxs-lookup"><span data-stu-id="73cec-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="73cec-104">Požadavky na systém serveru</span><span class="sxs-lookup"><span data-stu-id="73cec-104">Server system requirements</span></span>

<span data-ttu-id="73cec-105">SignalR pro ASP.NET Core podporuje jakoukoli serverovou platformu, která ASP.NET Core podporuje.</span><span class="sxs-lookup"><span data-stu-id="73cec-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="73cec-106">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="73cec-106">JavaScript client</span></span>

<span data-ttu-id="73cec-107">[Klient jazyka JavaScript](xref:signalr/javascript-client) běží na NodeJS 8 a novějších verzích a v následujících prohlížečích:</span><span class="sxs-lookup"><span data-stu-id="73cec-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="73cec-108">Prohlížeč</span><span class="sxs-lookup"><span data-stu-id="73cec-108">Browser</span></span>                          | <span data-ttu-id="73cec-109">Verze</span><span class="sxs-lookup"><span data-stu-id="73cec-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="73cec-110">Apple Safari, včetně iOS</span><span class="sxs-lookup"><span data-stu-id="73cec-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="73cec-111">Aktivní&dagger;</span><span class="sxs-lookup"><span data-stu-id="73cec-111">Current&dagger;</span></span> |
| <span data-ttu-id="73cec-112">Google Chrome, včetně Androidu</span><span class="sxs-lookup"><span data-stu-id="73cec-112">Google Chrome, including Android</span></span> | <span data-ttu-id="73cec-113">Aktivní&dagger;</span><span class="sxs-lookup"><span data-stu-id="73cec-113">Current&dagger;</span></span> |
| <span data-ttu-id="73cec-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="73cec-114">Microsoft Edge</span></span>                   | <span data-ttu-id="73cec-115">Aktivní&dagger;</span><span class="sxs-lookup"><span data-stu-id="73cec-115">Current&dagger;</span></span> |
| <span data-ttu-id="73cec-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="73cec-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="73cec-117">Aktivní&dagger;</span><span class="sxs-lookup"><span data-stu-id="73cec-117">Current&dagger;</span></span> |

<span data-ttu-id="73cec-118">&dagger;*Aktuální* odkazuje na nejnovější verzi prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="73cec-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="73cec-119">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="73cec-119">.NET client</span></span>

<span data-ttu-id="73cec-120">[Klient .NET](xref:signalr/dotnet-client) běží na libovolné platformě podporované nástrojem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="73cec-120">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="73cec-121">[Vývojáři Xamarin můžou například SignalR použít](https://github.com/aspnet/Announcements/issues/305) k sestavování aplikací pro Android pomocí Xamarin. Android 8.4.0.1 a novějších aplikací pro iOS pomocí Xamarin. iOS 11.14.0.4 a novějších.</span><span class="sxs-lookup"><span data-stu-id="73cec-121">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="73cec-122">Pokud server používá službu IIS, přenos pomocí protokolu WebSockets vyžaduje službu IIS 8,0 nebo novější v systému Windows Server 2012 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="73cec-122">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="73cec-123">Další přenosy jsou podporované na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="73cec-123">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="73cec-124">Klient Java</span><span class="sxs-lookup"><span data-stu-id="73cec-124">Java client</span></span>

<span data-ttu-id="73cec-125">[Klient Java](xref:signalr/java-client) podporuje Java 8 a novější verze.</span><span class="sxs-lookup"><span data-stu-id="73cec-125">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="73cec-126">Nepodporované klienty</span><span class="sxs-lookup"><span data-stu-id="73cec-126">Unsupported clients</span></span>

<span data-ttu-id="73cec-127">Následující klienti jsou k dispozici, ale jsou experimentální nebo neoficiální.</span><span class="sxs-lookup"><span data-stu-id="73cec-127">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="73cec-128">Nejsou aktuálně podporované a nemusí být nikdy.</span><span class="sxs-lookup"><span data-stu-id="73cec-128">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="73cec-129">[Klient C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="73cec-129">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="73cec-130">[Klient SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="73cec-130">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
