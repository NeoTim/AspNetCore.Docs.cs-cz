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
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>Důležité informace o zabezpečení v gRPC pro ASP.NET Core

Podle [James Newton-King](https://twitter.com/jamesnk)

Tento článek obsahuje informace o zabezpečení gRPC s .NET Core.

## <a name="transport-security"></a>Bezpečnost dopravy

zprávy gRPC jsou odesílány a přijímány pomocí PROTOKOLU HTTP/2. Doporučujeme:

* [Zabezpečení transportní vrstvy (TLS)](https://tools.ietf.org/html/rfc5246) se používá k zabezpečení zpráv v produkčních aplikacích gRPC.
* gRPC služby by měly naslouchat a reagovat pouze přes zabezpečené porty.

TLS je konfigurován v Kestrel. Další informace o konfiguraci koncových bodů Kestrel naleznete v [tématu Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).

## <a name="exceptions"></a>Výjimky

Zprávy o výjimkách jsou obecně považovány za citlivá data, která by neměla být odhalena klientovi. Ve výchozím nastavení gRPC neodesílá klientovi podrobnosti o výjimce vyvolané službou gRPC. Místo toho klient obdrží obecnou zprávu označující došlo k chybě. Doručení zprávy o výjimce klientovi může být přepsáno (například ve vývoji nebo testu) pomocí [EnableDetailedErrors](xref:grpc/configuration#configure-services-options). Zprávy o výjimce by neměly být klientům v produkčních aplikacích vystaveny.

## <a name="message-size-limits"></a>Omezení velikosti zprávy

Příchozí zprávy klientům a službám gRPC jsou načteny do paměti. Omezení velikosti zprávy jsou mechanismy, které pomáhají zabránit gRPC z využívání nadměrných prostředků.

gRPC používá omezení velikosti pro příchozí a odchozí zprávy. Ve výchozím nastavení gRPC omezuje příchozí zprávy na 4 MB. Odchozí zprávy nejsou nijak omezeny.

Na serveru lze gRPC limity zpráv nakonfigurovat pro `AddGrpc`všechny služby v aplikaci s :

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

Limity lze také nakonfigurovat pro `AddServiceOptions<TService>`jednotlivé služby pomocí . Další informace o konfiguraci omezení velikosti zpráv naleznete v [tématu gRPC configuration](xref:grpc/configuration).

## <a name="client-certificate-validation"></a>Ověření klientského certifikátu

[Klientské certifikáty](https://tools.ietf.org/html/rfc5246#section-7.4.4) jsou nejprve ověřeny při navázání připojení. Ve výchozím nastavení kestrel neprovádí další ověření klientského certifikátu připojení.

Doporučujeme, aby služby gRPC zabezpečené klientskými certifikáty používaly balíček [Microsoft.AspNetCore.Authentication.Certificate.](xref:security/authentication/certauth) ASP.NET ověřování základní certifikace provede další ověření klientského certifikátu, včetně:

* Certifikát má platné rozšířené použití klíče (EKU)
* Je v době platnosti
* Zkontrolovat odvolání certifikátu
