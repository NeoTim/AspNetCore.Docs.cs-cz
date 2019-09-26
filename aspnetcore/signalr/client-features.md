---
title: Funkce klienta SignalR
author: bradygaster
description: Seznamte se s funkcemi, které různé klienti signalizace ASP.NET Core podporují.
monikerRange: '>= aspnetcore-3.0'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 2d6759a5484c37aee6db3d22b3127414231605ae
ms.sourcegitcommit: 14b25156e34c82ed0495b4aff5776ac5b1950b5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301193"
---
# <a name="aspnet-core-signalr-client-features"></a><span data-ttu-id="3a638-103">Klientské funkce nástroje ASP.NET Core Signal</span><span class="sxs-lookup"><span data-stu-id="3a638-103">ASP.NET Core SignalR client features</span></span>

## <a name="feature-distribution"></a><span data-ttu-id="3a638-104">Distribuce funkcí</span><span class="sxs-lookup"><span data-stu-id="3a638-104">Feature distribution</span></span>

<span data-ttu-id="3a638-105">V následující tabulce jsou uvedené funkce a podpora pro klienty, kteří nabízejí podporu v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="3a638-105">The table below shows the features and support for the clients that offer real-time support.</span></span>

| <span data-ttu-id="3a638-106">Funkce</span><span class="sxs-lookup"><span data-stu-id="3a638-106">Feature</span></span> | <span data-ttu-id="3a638-107">.NET</span><span class="sxs-lookup"><span data-stu-id="3a638-107">.NET</span></span> | <span data-ttu-id="3a638-108">JavaScript</span><span class="sxs-lookup"><span data-stu-id="3a638-108">JavaScript</span></span> | <span data-ttu-id="3a638-109">Java</span><span class="sxs-lookup"><span data-stu-id="3a638-109">Java</span></span> |
| ---- | :-: | :-: | :-: |
| <span data-ttu-id="3a638-110">Podpora služby signalizace Azure</span><span class="sxs-lookup"><span data-stu-id="3a638-110">Azure SignalR Service Support</span></span> |<span data-ttu-id="3a638-111">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-111">✔</span></span>|<span data-ttu-id="3a638-112">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-112">✔</span></span>|<span data-ttu-id="3a638-113">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-113">✔</span></span>|
| [<span data-ttu-id="3a638-114">Streamování ze serveru na klienta</span><span class="sxs-lookup"><span data-stu-id="3a638-114">Server-to-client Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="3a638-115">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-115">✔</span></span>|<span data-ttu-id="3a638-116">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-116">✔</span></span>|<span data-ttu-id="3a638-117">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-117">✔</span></span>|
| [<span data-ttu-id="3a638-118">Streamování klienta na server</span><span class="sxs-lookup"><span data-stu-id="3a638-118">Client-to-server Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="3a638-119">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-119">✔</span></span>|<span data-ttu-id="3a638-120">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-120">✔</span></span>|<span data-ttu-id="3a638-121">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-121">✔</span></span>|
| <span data-ttu-id="3a638-122">Automatické opětovné připojení ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span><span class="sxs-lookup"><span data-stu-id="3a638-122">Automatic Reconnection ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span></span>          |<span data-ttu-id="3a638-123">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-123">✔</span></span>|<span data-ttu-id="3a638-124">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-124">✔</span></span>| |
| <span data-ttu-id="3a638-125">Přenos přes WebSockets</span><span class="sxs-lookup"><span data-stu-id="3a638-125">WebSockets Transport</span></span> |<span data-ttu-id="3a638-126">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-126">✔</span></span>|<span data-ttu-id="3a638-127">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-127">✔</span></span>|<span data-ttu-id="3a638-128">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-128">✔</span></span>|
| <span data-ttu-id="3a638-129">Přenos událostí odeslaných serverem</span><span class="sxs-lookup"><span data-stu-id="3a638-129">Server-Sent Events Transport</span></span> |<span data-ttu-id="3a638-130">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-130">✔</span></span>|<span data-ttu-id="3a638-131">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-131">✔</span></span>| |
| <span data-ttu-id="3a638-132">Přenos dlouhého cyklického dotazování</span><span class="sxs-lookup"><span data-stu-id="3a638-132">Long Polling Transport</span></span> |<span data-ttu-id="3a638-133">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-133">✔</span></span>|<span data-ttu-id="3a638-134">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-134">✔</span></span>|<span data-ttu-id="3a638-135">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-135">✔</span></span>|
| <span data-ttu-id="3a638-136">Protokol centra JSON</span><span class="sxs-lookup"><span data-stu-id="3a638-136">JSON Hub Protocol</span></span> |<span data-ttu-id="3a638-137">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-137">✔</span></span>|<span data-ttu-id="3a638-138">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-138">✔</span></span>|<span data-ttu-id="3a638-139">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-139">✔</span></span>|
| <span data-ttu-id="3a638-140">Protokol centra MessagePack</span><span class="sxs-lookup"><span data-stu-id="3a638-140">MessagePack Hub Protocol</span></span> |<span data-ttu-id="3a638-141">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-141">✔</span></span>|<span data-ttu-id="3a638-142">✔</span><span class="sxs-lookup"><span data-stu-id="3a638-142">✔</span></span>| |

<span data-ttu-id="3a638-143">Podpora automatického opětovného připojení v klientovi Java je sledována v [našem sledování problémů](https://github.com/aspnet/AspNetCore/issues/8711).</span><span class="sxs-lookup"><span data-stu-id="3a638-143">Support for automatic reconnect in the Java client is tracked in [our issue tracker](https://github.com/aspnet/AspNetCore/issues/8711).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3a638-144">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3a638-144">Additional resources</span></span>

* [<span data-ttu-id="3a638-145">Začínáme se službou Signal pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a638-145">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="3a638-146">Podporované platformy</span><span class="sxs-lookup"><span data-stu-id="3a638-146">Supported platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="3a638-147">Centra</span><span class="sxs-lookup"><span data-stu-id="3a638-147">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="3a638-148">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="3a638-148">JavaScript client</span></span>](xref:signalr/javascript-client)
