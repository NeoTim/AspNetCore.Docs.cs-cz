---
title: Důležité informace o zabezpečení v gRPC pro ASP.NET Core
author: jamesnk
description: Přečtěte si o aspektech zabezpečení pro gRPC pro ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: f84bec0ef485b701b2be36384a2e49b9b28e473d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667319"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="02f73-103">Důležité informace o zabezpečení v gRPC pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="02f73-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="02f73-104">Podle [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="02f73-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="02f73-105">Tento článek obsahuje informace o zabezpečení gRPC s .NET Core.</span><span class="sxs-lookup"><span data-stu-id="02f73-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="02f73-106">Bezpečnost dopravy</span><span class="sxs-lookup"><span data-stu-id="02f73-106">Transport security</span></span>

<span data-ttu-id="02f73-107">zprávy gRPC jsou odesílány a přijímány pomocí PROTOKOLU HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="02f73-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="02f73-108">Doporučujeme:</span><span class="sxs-lookup"><span data-stu-id="02f73-108">We recommend:</span></span>

* <span data-ttu-id="02f73-109">[Zabezpečení transportní vrstvy (TLS)](https://tools.ietf.org/html/rfc5246) se používá k zabezpečení zpráv v produkčních aplikacích gRPC.</span><span class="sxs-lookup"><span data-stu-id="02f73-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="02f73-110">gRPC služby by měly naslouchat a reagovat pouze přes zabezpečené porty.</span><span class="sxs-lookup"><span data-stu-id="02f73-110">gRPC services should only listen and respond over secured ports.</span></span>

<span data-ttu-id="02f73-111">TLS je konfigurován v Kestrel.</span><span class="sxs-lookup"><span data-stu-id="02f73-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="02f73-112">Další informace o konfiguraci koncových bodů Kestrel naleznete v [tématu Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="02f73-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="exceptions"></a><span data-ttu-id="02f73-113">Výjimky</span><span class="sxs-lookup"><span data-stu-id="02f73-113">Exceptions</span></span>

<span data-ttu-id="02f73-114">Zprávy o výjimkách jsou obecně považovány za citlivá data, která by neměla být odhalena klientovi.</span><span class="sxs-lookup"><span data-stu-id="02f73-114">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="02f73-115">Ve výchozím nastavení gRPC neodesílá klientovi podrobnosti o výjimce vyvolané službou gRPC.</span><span class="sxs-lookup"><span data-stu-id="02f73-115">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="02f73-116">Místo toho klient obdrží obecnou zprávu označující došlo k chybě.</span><span class="sxs-lookup"><span data-stu-id="02f73-116">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="02f73-117">Doručení zprávy o výjimce klientovi může být přepsáno (například ve vývoji nebo testu) pomocí [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="02f73-117">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="02f73-118">Zprávy o výjimce by neměly být klientům v produkčních aplikacích vystaveny.</span><span class="sxs-lookup"><span data-stu-id="02f73-118">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="02f73-119">Omezení velikosti zprávy</span><span class="sxs-lookup"><span data-stu-id="02f73-119">Message size limits</span></span>

<span data-ttu-id="02f73-120">Příchozí zprávy klientům a službám gRPC jsou načteny do paměti.</span><span class="sxs-lookup"><span data-stu-id="02f73-120">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="02f73-121">Omezení velikosti zprávy jsou mechanismy, které pomáhají zabránit gRPC z využívání nadměrných prostředků.</span><span class="sxs-lookup"><span data-stu-id="02f73-121">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="02f73-122">gRPC používá omezení velikosti pro příchozí a odchozí zprávy.</span><span class="sxs-lookup"><span data-stu-id="02f73-122">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="02f73-123">Ve výchozím nastavení gRPC omezuje příchozí zprávy na 4 MB.</span><span class="sxs-lookup"><span data-stu-id="02f73-123">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="02f73-124">Odchozí zprávy nejsou nijak omezeny.</span><span class="sxs-lookup"><span data-stu-id="02f73-124">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="02f73-125">Na serveru lze gRPC limity zpráv nakonfigurovat pro `AddGrpc`všechny služby v aplikaci s :</span><span class="sxs-lookup"><span data-stu-id="02f73-125">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

<span data-ttu-id="02f73-126">Limity lze také nakonfigurovat pro `AddServiceOptions<TService>`jednotlivé služby pomocí .</span><span class="sxs-lookup"><span data-stu-id="02f73-126">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="02f73-127">Další informace o konfiguraci omezení velikosti zpráv naleznete v [tématu gRPC configuration](xref:grpc/configuration).</span><span class="sxs-lookup"><span data-stu-id="02f73-127">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="02f73-128">Ověření klientského certifikátu</span><span class="sxs-lookup"><span data-stu-id="02f73-128">Client certificate validation</span></span>

<span data-ttu-id="02f73-129">[Klientské certifikáty](https://tools.ietf.org/html/rfc5246#section-7.4.4) jsou nejprve ověřeny při navázání připojení.</span><span class="sxs-lookup"><span data-stu-id="02f73-129">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="02f73-130">Ve výchozím nastavení kestrel neprovádí další ověření klientského certifikátu připojení.</span><span class="sxs-lookup"><span data-stu-id="02f73-130">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="02f73-131">Doporučujeme, aby služby gRPC zabezpečené klientskými certifikáty používaly balíček [Microsoft.AspNetCore.Authentication.Certificate.](xref:security/authentication/certauth)</span><span class="sxs-lookup"><span data-stu-id="02f73-131">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="02f73-132">ASP.NET ověřování základní certifikace provede další ověření klientského certifikátu, včetně:</span><span class="sxs-lookup"><span data-stu-id="02f73-132">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="02f73-133">Certifikát má platné rozšířené použití klíče (EKU)</span><span class="sxs-lookup"><span data-stu-id="02f73-133">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="02f73-134">Je v době platnosti</span><span class="sxs-lookup"><span data-stu-id="02f73-134">Is within its validity period</span></span>
* <span data-ttu-id="02f73-135">Zkontrolovat odvolání certifikátu</span><span class="sxs-lookup"><span data-stu-id="02f73-135">Check certificate revocation</span></span>
