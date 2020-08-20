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
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>Požadavky na zabezpečení v gRPC pro ASP.NET Core

Od [James Newton – král](https://twitter.com/jamesnk)

Tento článek poskytuje informace o zabezpečení gRPC pomocí .NET Core.

## <a name="transport-security"></a>Zabezpečení přenosu

zprávy gRPC jsou odesílány a přijímány pomocí protokolu HTTP/2. Doporučujeme:

* [Protokol TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) se používá k zabezpečení zpráv v produkčních aplikacích gRPC.
* služby gRPC by měly naslouchat a reagovat jenom na zabezpečených portech.

Protokol TLS je nakonfigurovaný v Kestrel. Další informace o konfiguraci koncových bodů Kestrel najdete v tématu [Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

## <a name="exceptions"></a>Výjimky

Zprávy výjimek se obecně považují za citlivá data, která by neměla být odhalena klientovi. Ve výchozím nastavení gRPC neposílá podrobnosti o výjimce vyvolané službou gRPC klientovi. Místo toho klient obdrží obecnou zprávu oznamující, že došlo k chybě. Doručení zprávy o výjimce klientovi lze přepsat (například při vývoji nebo testování) pomocí [EnableDetailedErrors](xref:grpc/configuration#configure-services-options). Zprávy výjimek by neměly být vystaveny klientovi v produkčních aplikacích.

## <a name="message-size-limits"></a>Omezení velikosti zpráv

Příchozí zprávy pro klienty a služby gRPC jsou načteny do paměti. Omezení velikosti zpráv jsou mechanismem, který může zabránit gRPC v využívání nadměrného množství prostředků.

gRPC používá pro správu příchozích a odchozích zpráv omezení velikosti jednotlivých zpráv. Ve výchozím nastavení gRPC omezí příchozí zprávy na 4 MB. Odchozí zprávy nejsou nijak omezené.

Na serveru je možné nakonfigurovat omezení pro zprávy gRPC pro všechny služby v aplikaci `AddGrpc` :

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

Omezení lze také nakonfigurovat pro jednotlivé služby pomocí `AddServiceOptions<TService>` . Další informace o konfiguraci omezení velikosti zpráv najdete v tématu [Konfigurace gRPC](xref:grpc/configuration).

## <a name="client-certificate-validation"></a>Ověření certifikátu klienta

Po navázání spojení jsou [certifikáty klienta](https://tools.ietf.org/html/rfc5246#section-7.4.4) zpočátku ověřovány. Ve výchozím nastavení Kestrel neprovede dodatečné ověření klientského certifikátu připojení.

Doporučujeme, aby služby gRPC zabezpečené klientskými certifikáty používaly balíček [Microsoft. AspNetCore. Authentication. Certificate](xref:security/authentication/certauth) . ASP.NET Core ověřování certifikace provede dodatečné ověřování u klientského certifikátu, včetně:

* Certifikát má platné použití rozšířeného klíče (EKU).
* Spadá do období platnosti
* Kontrolovat odvolání certifikátu
