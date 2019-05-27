---
title: gRPC konfigurace ASP.NET Core
author: jamesnk
description: Zjistěte, jak nakonfigurovat gRPC pro aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 04/09/2019
uid: grpc/configuration
ms.openlocfilehash: 851c9ca1f7d62f6f368df66bb38eb4bbaf64bf32
ms.sourcegitcommit: 5d384db2fa9373a93b5d15e985fb34430e49ad7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66041885"
---
# <a name="grpc-for-aspnet-core-configuration"></a>gRPC konfigurace ASP.NET Core

## <a name="configure-services-options"></a>Nakonfigurujte možnosti služby

Následující tabulka popisuje možnosti pro konfiguraci služby gRPC:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | Maximální velikost zprávy v bajtech odeslaných ze serveru. Došlo k pokusu o odeslání zprávy, která překračuje velikost výsledky nakonfigurovanou maximální zprávy výjimek. |
| `ReceiveMaxMessageSize` | 4 MB | Maximální velikost zprávy v bajtech, které lze přijímat pomocí serveru. Pokud server přijme zprávu, která překračuje tento limit, dojde k výjimce. Zvýšení hodnoty tuto umožňuje serveru pro příjem větší zprávy, ale může mít negativní vliv na využití paměti. |
| `EnableDetailedErrors` | `false` | Pokud `true`, podrobné zprávy o výjimkách se vrátí ke klientům, když dojde k výjimce v metodě služby. Výchozí hodnota je `false`. Nastavení na `true` může způsobit únik citlivých informací. |
| `CompressionProviders` | gzip | Kolekce zprostředkovatelů komprese používá při komprimaci a dekomprimaci zprávy. Poskytovatelé vlastního komprese můžete vytvořen a přidán do kolekce. Výchozí nastavení nakonfigurován zprostředkovatel podporuje **gzip** komprese. |
| `ResponseCompressionAlgorithm` | `null` | Komprese algoritmus používaný ke kompresi zpráv odeslaných ze serveru. Algoritmus musí odpovídat komprese zprostředkovatele v `CompressionProviders`. Pro algorthm pro kompresi odpovědí musíte uvést podporuje algoritmus a odeslat ho klienta **grpc přijmout – kódování** záhlaví. |
| `ResponseCompressionLevel` | `null` | Úroveň komprese použitý ke kompresi zpráv odeslaných ze serveru. |

Je možné nakonfigurovat možnosti pro všechny služby poskytnutím delegáta možnosti k `AddGrpc` volání v `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.EnableDetailedErrors = true;
    });
}
```

Možnosti pro jednu službu přepsat globální možnosti uvedené v `AddGrpc` a dá se nakonfigurovat pomocí `AddServiceOptions<TService>`:

```csharp
services.AddGrpc().AddServiceOptions<MyService>(options =>
{
    options.ReceiveMaxMessageSize = 10 * 1024 * 1024; // 10 megabytes
});
```

## <a name="additional-resources"></a>Další zdroje

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
