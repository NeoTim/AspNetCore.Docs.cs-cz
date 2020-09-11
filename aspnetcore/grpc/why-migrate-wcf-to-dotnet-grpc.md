---
title: Proč migrovat WCF na ASP.NET Core gRPC
author: markrendle
description: Tento článek poskytuje přehled důvodů, proč je ASP.NET Core gRPC vhodný pro vývojáře Windows Communication Foundation (WCF), kteří chtějí migrovat na moderní architektury a platformy.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 811e6037b058b26fcf91063123d04d448a9a28a8
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90012756"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a><span data-ttu-id="1b9da-103">gRPC pro vývojáře pro Windows Communication Foundation (WCF)</span><span class="sxs-lookup"><span data-stu-id="1b9da-103">gRPC for Windows Communication Foundation (WCF) developers</span></span>

<span data-ttu-id="1b9da-104">Tento článek poskytuje přehled důvodů, proč je ASP.NET Core gRPC vhodný pro vývojáře Windows Communication Foundation (WCF), kteří chtějí migrovat na moderní architektury a platformy.</span><span class="sxs-lookup"><span data-stu-id="1b9da-104">This article provides a summary of why ASP.NET Core gRPC is a good fit for Windows Communication Foundation (WCF) developers who want to migrate to modern architectures and platforms.</span></span>

## <a name="comparison-to-wcf"></a><span data-ttu-id="1b9da-105">Porovnání s WCF</span><span class="sxs-lookup"><span data-stu-id="1b9da-105">Comparison to WCF</span></span>

<span data-ttu-id="1b9da-106">I když je implementace a přístup odlišná pro gRPC, možnosti vývoje a využívání služeb s gRPC by měly být intuitivní pro vývojáře WCF.</span><span class="sxs-lookup"><span data-stu-id="1b9da-106">Although the implementation and approach are different for gRPC, the experience of developing and consuming services with gRPC should be intuitive for WCF developers.</span></span> <span data-ttu-id="1b9da-107">WCF a gRPC jsou architektury RPC (Remote Procedure Call) se stejnými cíli:</span><span class="sxs-lookup"><span data-stu-id="1b9da-107">WCF and gRPC are RPC (remote procedure call) frameworks with the same goals:</span></span>

* <span data-ttu-id="1b9da-108">Umožňuje psaní kódu, jako by klient a server byly na stejné platformě.</span><span class="sxs-lookup"><span data-stu-id="1b9da-108">Make it possible to code as though the client and server are on the same platform.</span></span>
* <span data-ttu-id="1b9da-109">Poskytněte zjednodušené přenosné síťové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1b9da-109">Provide a simplified portable networking API.</span></span>

<span data-ttu-id="1b9da-110">Obě platformy sdílejí požadavky na deklaraci a implementaci rozhraní, i když proces pro deklaraci rozhraní je jiný.</span><span class="sxs-lookup"><span data-stu-id="1b9da-110">Both platforms share the requirement of declaring and implementing an interface, although the process for declaring the interface is different.</span></span> <span data-ttu-id="1b9da-111">Mnoho typů volání RPC, které gRPC podporuje, je dobře mapované na vazby, které jsou dostupné pro služby WCF.</span><span class="sxs-lookup"><span data-stu-id="1b9da-111">The many types of RPC calls that gRPC supports map well to the bindings available to WCF services.</span></span> <span data-ttu-id="1b9da-112">Další informace a příklady najdete v tématu [migrace řešení WCF do gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span><span class="sxs-lookup"><span data-stu-id="1b9da-112">For more information and examples, see [Migrate a WCF solution to gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span></span>

## <a name="benefits-of-grpc"></a><span data-ttu-id="1b9da-113">Výhody gRPC</span><span class="sxs-lookup"><span data-stu-id="1b9da-113">Benefits of gRPC</span></span>

<span data-ttu-id="1b9da-114">gRPC poskytuje lepší rámec než jiné přístupy z následujících důvodů.</span><span class="sxs-lookup"><span data-stu-id="1b9da-114">gRPC provides a better framework than other approaches for the following reasons.</span></span>

### <a name="performance"></a><span data-ttu-id="1b9da-115">Výkon</span><span class="sxs-lookup"><span data-stu-id="1b9da-115">Performance</span></span>

<span data-ttu-id="1b9da-116">gRPC používá protokol HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="1b9da-116">gRPC uses HTTP/2.</span></span> <span data-ttu-id="1b9da-117">Na rozdíl od HTTP/1.1, HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="1b9da-117">In contrast to HTTP/1.1, HTTP/2:</span></span>

* <span data-ttu-id="1b9da-118">Je menší a rychlejší binární protokol.</span><span class="sxs-lookup"><span data-stu-id="1b9da-118">Is a smaller, faster binary protocol.</span></span>
* <span data-ttu-id="1b9da-119">Je efektivnější pro počítače k analýze.</span><span class="sxs-lookup"><span data-stu-id="1b9da-119">Is more efficient for computers to parse.</span></span>
* <span data-ttu-id="1b9da-120">Podporuje multiplexování požadavků v rámci jednoho připojení.</span><span class="sxs-lookup"><span data-stu-id="1b9da-120">Supports multiplexing requests over a single connection.</span></span> <span data-ttu-id="1b9da-121">Multiplexování umožňuje odesílání více požadavků přes jedno připojení bez nutnosti vzájemného blokování požadavků.</span><span class="sxs-lookup"><span data-stu-id="1b9da-121">Multiplexing enables multiple requests to be sent over one connection without requests blocking each other.</span></span> <span data-ttu-id="1b9da-122">V HTTP/1.1 se blokování nazývá "blokování na konci řádku (HOL)".</span><span class="sxs-lookup"><span data-stu-id="1b9da-122">In HTTP/1.1, the blocking is known as "head-of-line (HOL) blocking."</span></span>

<span data-ttu-id="1b9da-123">gRPC používá ke serializaci zpráv Protobuf efektivní binární formát.</span><span class="sxs-lookup"><span data-stu-id="1b9da-123">gRPC uses Protobuf, an efficient binary format, to serialize messages.</span></span> <span data-ttu-id="1b9da-124">Protobuf zprávy:</span><span class="sxs-lookup"><span data-stu-id="1b9da-124">Protobuf messages are:</span></span>
* <span data-ttu-id="1b9da-125">Rychlá serializace a deserializace.</span><span class="sxs-lookup"><span data-stu-id="1b9da-125">Fast to serialize and deserialize.</span></span>
* <span data-ttu-id="1b9da-126">Používejte méně šířky pásma než textové formáty.</span><span class="sxs-lookup"><span data-stu-id="1b9da-126">Use less bandwidth than text-based formats.</span></span> 

<span data-ttu-id="1b9da-127">gRPC je vhodné řešení pro mobilní zařízení a sítě s omezeními šířky pásma.</span><span class="sxs-lookup"><span data-stu-id="1b9da-127">gRPC is a good solution for mobile devices and networks with bandwidth restrictions.</span></span>

### <a name="interoperability"></a><span data-ttu-id="1b9da-128">Interoperabilita</span><span class="sxs-lookup"><span data-stu-id="1b9da-128">Interoperability</span></span>

<span data-ttu-id="1b9da-129">Existují nástroje a knihovny gRPC pro všechny hlavní programovací jazyky a platformy, včetně .NET, Java, Python, přejít, C++, Node.js, SWIFT, DART, Ruby a PHP.</span><span class="sxs-lookup"><span data-stu-id="1b9da-129">There are gRPC tools and libraries for all major programming languages and platforms, including .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby, and PHP.</span></span> <span data-ttu-id="1b9da-130">Díky Protobuf binárnímu formátu a efektivnímu generování kódu pro každou platformu můžou vývojáři vytvářet aplikace pro různé platformy a provádět aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b9da-130">Thanks to the Protobuf binary wire format and the efficient code generation for each platform, developers can build cross-platform, performant apps.</span></span>

### <a name="usability-and-productivity"></a><span data-ttu-id="1b9da-131">Použitelnost a produktivita</span><span class="sxs-lookup"><span data-stu-id="1b9da-131">Usability and productivity</span></span>

<span data-ttu-id="1b9da-132">gRPC je komplexní řešení RPC.</span><span class="sxs-lookup"><span data-stu-id="1b9da-132">gRPC is a comprehensive RPC solution.</span></span> <span data-ttu-id="1b9da-133">Funguje konzistentně napříč různými jazyky a platformami.</span><span class="sxs-lookup"><span data-stu-id="1b9da-133">It works consistently across multiple languages and platforms.</span></span> <span data-ttu-id="1b9da-134">Nabízí také skvělé nástroje, které jsou automaticky generovány velkým často používaným kódem.</span><span class="sxs-lookup"><span data-stu-id="1b9da-134">It also provides excellent tooling, with much of the boilerplate code automatically generated.</span></span> <span data-ttu-id="1b9da-135">Podobně jako WCF, gRPC automaticky generuje zprávy a klienta silného typu.</span><span class="sxs-lookup"><span data-stu-id="1b9da-135">Like WCF, gRPC automatically generates messages and a strongly typed client.</span></span> <span data-ttu-id="1b9da-136">Čas vývojáře je zdarma zaměřen na obchodní logiku.</span><span class="sxs-lookup"><span data-stu-id="1b9da-136">Developer time is freed up to focus on business logic.</span></span>

### <a name="streaming"></a><span data-ttu-id="1b9da-137">Streamování</span><span class="sxs-lookup"><span data-stu-id="1b9da-137">Streaming</span></span>

<span data-ttu-id="1b9da-138">gRPC má plný obousměrný Stream, který poskytuje podobné funkce jako plně duplexní služby WCF.</span><span class="sxs-lookup"><span data-stu-id="1b9da-138">gRPC has full bidirectional streaming, which provides similar functionality to WCF's full duplex services.</span></span> <span data-ttu-id="1b9da-139">gRPC streaming může pracovat přes běžná připojení k Internetu, nástroje pro vyrovnávání zatížení a sítě.</span><span class="sxs-lookup"><span data-stu-id="1b9da-139">gRPC streaming can operate over regular internet connections, load balancers, and service meshes.</span></span>

### <a name="deadlines-timeouts-and-cancellation"></a><span data-ttu-id="1b9da-140">Konečné termíny, časové limity a zrušení</span><span class="sxs-lookup"><span data-stu-id="1b9da-140">Deadlines, timeouts, and cancellation</span></span>

<span data-ttu-id="1b9da-141">gRPC umožňuje klientům zadat maximální dobu, po kterou se RPC bude dokončit.</span><span class="sxs-lookup"><span data-stu-id="1b9da-141">gRPC allows clients to specify a maximum time for an RPC to finish.</span></span> <span data-ttu-id="1b9da-142">Pokud je překročen zadaný konečný termín, server může operaci zrušit nezávisle na klientovi.</span><span class="sxs-lookup"><span data-stu-id="1b9da-142">If the specified deadline is exceeded, the server can cancel the operation independently of the client.</span></span> <span data-ttu-id="1b9da-143">Termíny a zrušení se dají rozšířit prostřednictvím dalších volání gRPC, která vám pomůžou vyhodnotit omezení využití prostředků.</span><span class="sxs-lookup"><span data-stu-id="1b9da-143">Deadlines and cancellations can be propagated through subsequent gRPC calls to help enforce resource usage limits.</span></span> <span data-ttu-id="1b9da-144">Klienti mohou zastavit operace, pokud je překročen konečný termín nebo v případě potřeby předchozí.</span><span class="sxs-lookup"><span data-stu-id="1b9da-144">Clients can stop operations when a deadline is exceeded, or earlier if necessary.</span></span> <span data-ttu-id="1b9da-145">Například klienti mohou zastavit operace z důvodu interakce s uživatelem.</span><span class="sxs-lookup"><span data-stu-id="1b9da-145">For example, clients can stop operations because of a user interaction.</span></span>

### <a name="security"></a><span data-ttu-id="1b9da-146">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="1b9da-146">Security</span></span>

<span data-ttu-id="1b9da-147">gRPC může používat protokoly TLS a HTTP/2 k zajištění uceleného šifrovaného připojení mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="1b9da-147">gRPC can use TLS and HTTP/2 to provide an end-to-end encrypted connection between the client and the server.</span></span> <span data-ttu-id="1b9da-148">Podpora ověřování klientských certifikátů dále zvyšuje zabezpečení a důvěryhodnost mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="1b9da-148">Support for client certificate authentication further increases security and trust between client and server.</span></span>

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a><span data-ttu-id="1b9da-149">gRPC jako cestu migrace pro WCF do .NET Core a .NET 5</span><span class="sxs-lookup"><span data-stu-id="1b9da-149">gRPC as a migration path for WCF to .NET Core and .NET 5</span></span>

<span data-ttu-id="1b9da-150">Rozhraní .NET Core a .NET 5 označí Shift způsob, jakým společnost Microsoft dodává řešení pro vzdálenou komunikaci vývojářům, kteří chtějí poskytovat služby v rámci celé řady platforem.</span><span class="sxs-lookup"><span data-stu-id="1b9da-150">.NET Core and .NET 5 marks a shift in the way that Microsoft delivers remote communication solutions to developers who want to deliver services across a range of platforms.</span></span> <span data-ttu-id="1b9da-151">.NET Core a .NET 5 podporují [volání služeb WCF](/dotnet/core/additional-tools/wcf-web-service-reference-guide), ale nenabízejí podporu na straně serveru pro hostování WCF.</span><span class="sxs-lookup"><span data-stu-id="1b9da-151">.NET Core and .NET 5 support [calling WCF services](/dotnet/core/additional-tools/wcf-web-service-reference-guide), but won't offer server-side support for hosting WCF.</span></span>

<span data-ttu-id="1b9da-152">Existují dva doporučené cesty pro modernizaci aplikace WCF:</span><span class="sxs-lookup"><span data-stu-id="1b9da-152">There are two recommended paths for modernizing WCF apps:</span></span>

* <span data-ttu-id="1b9da-153">gRPC je postaven na moderních technologiích a je známý jako nejoblíbenější volba napříč komunitou vývojářů pro aplikace RPC.</span><span class="sxs-lookup"><span data-stu-id="1b9da-153">gRPC is built on modern technologies and has emerged as the most popular choice across the developer community for RPC apps.</span></span> <span data-ttu-id="1b9da-154">Počínaje .NET Core 3,0 mají moderní platformy .NET vynikající podporu pro gRPC.</span><span class="sxs-lookup"><span data-stu-id="1b9da-154">Starting with .NET Core 3.0, modern .NET platforms have excellent support for gRPC.</span></span> <span data-ttu-id="1b9da-155">Migrace služeb WCF na použití gRPC pomáhá poskytovat funkce RPC, výkon a interoperabilitu, která je potřeba v moderních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="1b9da-155">Migrating WCF services to use gRPC helps provide the RPC features, performance, an interoperability needed in modern apps.</span></span>

* <span data-ttu-id="1b9da-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) je snaha komunity o poskytování podpory pro hostování služeb WCF do .NET Core a .NET 5.</span><span class="sxs-lookup"><span data-stu-id="1b9da-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) is a community effort to bring support for hosting WCF services to .NET Core and .NET 5.</span></span> <span data-ttu-id="1b9da-157">K dispozici je verze Preview a projekt pracuje směrem k připravené výrobě.</span><span class="sxs-lookup"><span data-stu-id="1b9da-157">A preview release is available and the project is working towards being production ready.</span></span> <span data-ttu-id="1b9da-158">CoreWCF podporuje pouze podmnožinu funkcí WCF a .NET Framework aplikace, které migrují na použití, budou potřebovat změny kódu a testování, aby byly úspěšné.</span><span class="sxs-lookup"><span data-stu-id="1b9da-158">CoreWCF only supports a subset of WCF's features, and .NET Framework apps that migrate to use it will need code changes and testing to be successful.</span></span> <span data-ttu-id="1b9da-159">CoreWCF je dobrou volbou v případě, že aplikace musí zachovat kompatibilitu se stávajícími klienty, kteří volají služby WCF.</span><span class="sxs-lookup"><span data-stu-id="1b9da-159">CoreWCF is a good choice if an app has to maintain compatibility with existing clients that call WCF services.</span></span>

## <a name="get-started"></a><span data-ttu-id="1b9da-160">Začínáme</span><span class="sxs-lookup"><span data-stu-id="1b9da-160">Get started</span></span>

<span data-ttu-id="1b9da-161">Podrobné pokyny k vytváření gRPC Services v ASP.NET Core pro vývojáře WCF najdete v tématu [ASP.NET Core gRPC pro vývojáře WCF](/dotnet/architecture/grpc-for-wcf-developers) .</span><span class="sxs-lookup"><span data-stu-id="1b9da-161">For detailed guidance on building gRPC services in ASP.NET Core for WCF developers, see [ASP.NET Core gRPC for WCF Developers](/dotnet/architecture/grpc-for-wcf-developers)</span></span>
