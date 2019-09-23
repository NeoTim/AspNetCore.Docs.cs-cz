---
title: Funkce klienta nástroje Signal
author: bradygaster
description: Seznamte se s funkcemi, které různé klienti signalizace ASP.NET Core podporují.
monikerRange: '>= aspnetcore-3.0'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 55086673e0c9f9b73f07730ea25c3fa322f7fd98
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187465"
---
# <a name="aspnet-core-signalr-client-features"></a><span data-ttu-id="e9499-103">Klientské funkce nástroje ASP.NET Core Signal</span><span class="sxs-lookup"><span data-stu-id="e9499-103">ASP.NET Core SignalR client features</span></span>

## <a name="feature-distribution"></a><span data-ttu-id="e9499-104">Distribuce funkcí</span><span class="sxs-lookup"><span data-stu-id="e9499-104">Feature distribution</span></span>

<span data-ttu-id="e9499-105">V následující tabulce jsou uvedené funkce a podpora pro klienty, kteří nabízejí podporu v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="e9499-105">The table below shows the features and support for the clients that offer real-time support.</span></span>

| <span data-ttu-id="e9499-106">Funkce</span><span class="sxs-lookup"><span data-stu-id="e9499-106">Feature</span></span> | <span data-ttu-id="e9499-107">.NET Core</span><span class="sxs-lookup"><span data-stu-id="e9499-107">.NET Core</span></span> | <span data-ttu-id="e9499-108">JavaScript</span><span class="sxs-lookup"><span data-stu-id="e9499-108">JavaScript</span></span> | <span data-ttu-id="e9499-109">Java</span><span class="sxs-lookup"><span data-stu-id="e9499-109">Java</span></span> |
| ---- | :-: | :-: | :-: |
| [<span data-ttu-id="e9499-110">Streamování ze serveru na klienta</span><span class="sxs-lookup"><span data-stu-id="e9499-110">Server-to-client Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="e9499-111">✔</span><span class="sxs-lookup"><span data-stu-id="e9499-111">✔</span></span>|<span data-ttu-id="e9499-112">✔</span><span class="sxs-lookup"><span data-stu-id="e9499-112">✔</span></span>|<span data-ttu-id="e9499-113">✔</span><span class="sxs-lookup"><span data-stu-id="e9499-113">✔</span></span>|
| [<span data-ttu-id="e9499-114">Streamování klienta na server</span><span class="sxs-lookup"><span data-stu-id="e9499-114">Client-to-server Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="e9499-115">✔</span><span class="sxs-lookup"><span data-stu-id="e9499-115">✔</span></span>|<span data-ttu-id="e9499-116">✔</span><span class="sxs-lookup"><span data-stu-id="e9499-116">✔</span></span>|<span data-ttu-id="e9499-117">✔</span><span class="sxs-lookup"><span data-stu-id="e9499-117">✔</span></span>|
| <span data-ttu-id="e9499-118">Automatické opětovné připojení ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span><span class="sxs-lookup"><span data-stu-id="e9499-118">Automatic Reconnection ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span></span>          |<span data-ttu-id="e9499-119">✔</span><span class="sxs-lookup"><span data-stu-id="e9499-119">✔</span></span>|<span data-ttu-id="e9499-120">✔</span><span class="sxs-lookup"><span data-stu-id="e9499-120">✔</span></span>| |

## <a name="additional-resources"></a><span data-ttu-id="e9499-121">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e9499-121">Additional resources</span></span>

* [<span data-ttu-id="e9499-122">Začínáme se službou Signal pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e9499-122">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="e9499-123">Podporované platformy</span><span class="sxs-lookup"><span data-stu-id="e9499-123">Supported platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="e9499-124">Centra</span><span class="sxs-lookup"><span data-stu-id="e9499-124">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="e9499-125">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="e9499-125">JavaScript client</span></span>](xref:signalr/javascript-client)
