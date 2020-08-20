---
title: Požadavky na zabezpečení v gRPC pro ASP.NET Core
author: jamesnk
description: Přečtěte si o požadavcích na zabezpečení pro gRPC pro ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
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
uid: grpc/security
ms.openlocfilehash: ca03a129281377af9271d56e2400d1a238358554
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632652"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="5dfe6-103">Požadavky na zabezpečení v gRPC pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5dfe6-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="5dfe6-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5dfe6-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="5dfe6-105">Tento článek poskytuje informace o zabezpečení gRPC pomocí .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5dfe6-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="5dfe6-106">Zabezpečení přenosu</span><span class="sxs-lookup"><span data-stu-id="5dfe6-106">Transport security</span></span>

<span data-ttu-id="5dfe6-107">zprávy gRPC jsou odesílány a přijímány pomocí protokolu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="5dfe6-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="5dfe6-108">Doporučujeme:</span><span class="sxs-lookup"><span data-stu-id="5dfe6-108">We recommend:</span></span>

* <span data-ttu-id="5dfe6-109">[Protokol TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) se používá k zabezpečení zpráv v produkčních aplikacích gRPC.</span><span class="sxs-lookup"><span data-stu-id="5dfe6-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="5dfe6-110">služby gRPC by měly naslouchat a reagovat jenom na zabezpečených portech.</span><span class="sxs-lookup"><span data-stu-id="5dfe6-110">gRPC services should only listen and respond over secured ports.</span></span>

<span data-ttu-id="5dfe6-111">Protokol TLS je nakonfigurovaný v Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5dfe6-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="5dfe6-112">Další informace o konfiguraci koncových bodů Kestrel najdete v tématu [Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="5dfe6-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="exceptions"></a><span data-ttu-id="5dfe6-113">Výjimky</span><span class="sxs-lookup"><span data-stu-id="5dfe6-113">Exceptions</span></span>

<span data-ttu-id="5dfe6-114">Zprávy výjimek se obecně považují za citlivá data, která by neměla být odhalena klientovi.</span><span class="sxs-lookup"><span data-stu-id="5dfe6-114">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="5dfe6-115">Ve výchozím nastavení gRPC neposílá podrobnosti o výjimce vyvolané službou gRPC klientovi.</span><span class="sxs-lookup"><span data-stu-id="5dfe6-115">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="5dfe6-116">Místo toho klient obdrží obecnou zprávu oznamující, že došlo k chybě.</span><span class="sxs-lookup"><span data-stu-id="5dfe6-116">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="5dfe6-117">Doručení zprávy o výjimce klientovi lze přepsat (například při vývoji nebo testování) pomocí [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="5dfe6-117">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="5dfe6-118">Zprávy výjimek by neměly být vystaveny klientovi v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="5dfe6-118">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="5dfe6-119">Omezení velikosti zpráv</span><span class="sxs-lookup"><span data-stu-id="5dfe6-119">Message size limits</span></span>

<span data-ttu-id="5dfe6-120">Příchozí zprávy pro klienty a služby gRPC jsou načteny do paměti.</span><span class="sxs-lookup"><span data-stu-id="5dfe6-120">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="5dfe6-121">Omezení velikosti zpráv jsou mechanismem, který může zabránit gRPC v využívání nadměrného množství prostředků.</span><span class="sxs-lookup"><span data-stu-id="5dfe6-121">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="5dfe6-122">gRPC používá pro správu příchozích a odchozích zpráv omezení velikosti jednotlivých zpráv.</span><span class="sxs-lookup"><span data-stu-id="5dfe6-122">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="5dfe6-123">Ve výchozím nastavení gRPC omezí příchozí zprávy na 4 MB.</span><span class="sxs-lookup"><span data-stu-id="5dfe6-123">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="5dfe6-124">Odchozí zprávy nejsou nijak omezené.</span><span class="sxs-lookup"><span data-stu-id="5dfe6-124">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="5dfe6-125">Na serveru je možné nakonfigurovat omezení pro zprávy gRPC pro všechny služby v aplikaci `AddGrpc` :</span><span class="sxs-lookup"><span data-stu-id="5dfe6-125">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

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

<span data-ttu-id="5dfe6-126">Omezení lze také nakonfigurovat pro jednotlivé služby pomocí `AddServiceOptions<TService>` .</span><span class="sxs-lookup"><span data-stu-id="5dfe6-126">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="5dfe6-127">Další informace o konfiguraci omezení velikosti zpráv najdete v tématu [Konfigurace gRPC](xref:grpc/configuration).</span><span class="sxs-lookup"><span data-stu-id="5dfe6-127">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="5dfe6-128">Ověření certifikátu klienta</span><span class="sxs-lookup"><span data-stu-id="5dfe6-128">Client certificate validation</span></span>

<span data-ttu-id="5dfe6-129">Po navázání spojení jsou [certifikáty klienta](https://tools.ietf.org/html/rfc5246#section-7.4.4) zpočátku ověřovány.</span><span class="sxs-lookup"><span data-stu-id="5dfe6-129">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="5dfe6-130">Ve výchozím nastavení Kestrel neprovede dodatečné ověření klientského certifikátu připojení.</span><span class="sxs-lookup"><span data-stu-id="5dfe6-130">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="5dfe6-131">Doporučujeme, aby služby gRPC zabezpečené klientskými certifikáty používaly balíček [Microsoft. AspNetCore. Authentication. Certificate](xref:security/authentication/certauth) .</span><span class="sxs-lookup"><span data-stu-id="5dfe6-131">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="5dfe6-132">ASP.NET Core ověřování certifikace provede dodatečné ověřování u klientského certifikátu, včetně:</span><span class="sxs-lookup"><span data-stu-id="5dfe6-132">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="5dfe6-133">Certifikát má platné použití rozšířeného klíče (EKU).</span><span class="sxs-lookup"><span data-stu-id="5dfe6-133">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="5dfe6-134">Spadá do období platnosti</span><span class="sxs-lookup"><span data-stu-id="5dfe6-134">Is within its validity period</span></span>
* <span data-ttu-id="5dfe6-135">Kontrolovat odvolání certifikátu</span><span class="sxs-lookup"><span data-stu-id="5dfe6-135">Check certificate revocation</span></span>
