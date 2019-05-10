---
title: Úvod do gRPC v ASP.NET Core
author: juntaoluo
description: Další informace o službách gRPC Kestrel serveru a ASP.NET Core zásobník.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 02/26/2019
uid: grpc/index
ms.openlocfilehash: dd1c42744bfda965df91ea1fcc0b71814317b969
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085543"
---
# <a name="introduction-to-grpc-on-aspnet-core"></a><span data-ttu-id="ca6a7-103">Úvod do gRPC v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ca6a7-103">Introduction to gRPC on ASP.NET Core</span></span>

<span data-ttu-id="ca6a7-104">Podle [Luo Jan](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="ca6a7-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="ca6a7-105">[gRPC](https://grpc.io/docs/guides/) je jazyk nezávislá, vysoce výkonné rozhraní vzdáleného volání procedur (RPC).</span><span class="sxs-lookup"><span data-stu-id="ca6a7-105">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span> <span data-ttu-id="ca6a7-106">Další informace o gRPC základy, najdete v článku [stránky dokumentace gRPC](https://grpc.io/docs/).</span><span class="sxs-lookup"><span data-stu-id="ca6a7-106">For more on gRPC fundamentals, see the [gRPC documentation page](https://grpc.io/docs/).</span></span>

<span data-ttu-id="ca6a7-107">Hlavní výhody gRPC jsou:</span><span class="sxs-lookup"><span data-stu-id="ca6a7-107">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="ca6a7-108">Moderní zjednodušené RPC architektura s vysokým výkonem.</span><span class="sxs-lookup"><span data-stu-id="ca6a7-108">Modern high-performance lightweight RPC framework.</span></span>
* <span data-ttu-id="ca6a7-109">Vývoj rozhraní API kontraktem, ve výchozím nastavení, umožňující bez implementace jazyka pomocí Protocol Buffers.</span><span class="sxs-lookup"><span data-stu-id="ca6a7-109">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="ca6a7-110">Nástroje dostupné pro řadu jazyků pro generování silného typu serverů a klientů.</span><span class="sxs-lookup"><span data-stu-id="ca6a7-110">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="ca6a7-111">Podporuje klient, server a obousměrné streamování volání.</span><span class="sxs-lookup"><span data-stu-id="ca6a7-111">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="ca6a7-112">Snížená síťová využití pomocí Protobuf binární serializace.</span><span class="sxs-lookup"><span data-stu-id="ca6a7-112">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="ca6a7-113">Mezi tyto výhody proveďte gRPC ideální pro:</span><span class="sxs-lookup"><span data-stu-id="ca6a7-113">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="ca6a7-114">Jednoduché mikroslužby, kde efektivita je velmi důležité.</span><span class="sxs-lookup"><span data-stu-id="ca6a7-114">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="ca6a7-115">Polyglot systémy, kde se vyžadují pro vývoj pro více jazyků.</span><span class="sxs-lookup"><span data-stu-id="ca6a7-115">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="ca6a7-116">Point-to-Point v reálném čase služby, které je třeba pro zpracování datových proudů žádostí a odpovědí.</span><span class="sxs-lookup"><span data-stu-id="ca6a7-116">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

<span data-ttu-id="ca6a7-117">Zatímco C# implementace je teď dostupná pro official je přínosné pro [gRPC stránky](https://grpc.io/docs/quickstart/csharp.html), aktuální implementace závisí na nativní knihovny napsané v jazyce C (gRPC [C – jádro](https://grpc.io/blog/grpc-stacks)).</span><span class="sxs-lookup"><span data-stu-id="ca6a7-117">While a C# implementation is currently available on the official [gRPC page](https://grpc.io/docs/quickstart/csharp.html), the current implementation relies on the native library written in C (gRPC [C-core](https://grpc.io/blog/grpc-stacks)).</span></span> <span data-ttu-id="ca6a7-118">Práce probíhá aktuálně k dispozici nové implementace založené na server Kestrel HTTP a ASP.NET Core zásobníku, která je plně spravovaná.</span><span class="sxs-lookup"><span data-stu-id="ca6a7-118">Work is currently in progress to provide a new implementation based on the Kestrel HTTP server and the ASP.NET Core stack that is fully managed.</span></span> <span data-ttu-id="ca6a7-119">Následující dokumenty poskytují úvod do vytváření gRPC služeb s touto novou implementací.</span><span class="sxs-lookup"><span data-stu-id="ca6a7-119">The following documents provide an introduction to building gRPC services with this new implementation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ca6a7-120">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ca6a7-120">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>