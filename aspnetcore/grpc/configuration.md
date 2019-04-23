---
title: gRPC konfigurace ASP.NET Core
author: jamesnk
description: Zjistěte, jak nakonfigurovat gRPC pro aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 04/09/2019
uid: grpc/configuration
ms.openlocfilehash: 66dfb9ec136616f10c1b7aaad766e18813b87de4
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59984523"
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

## <a name="configure-kestrel-options"></a>Nakonfigurujte Kestrel možnosti

Kestrel serveru obsahuje možnosti konfigurace, které ovlivňují chování gRPC pro technologii ASP.NET.

### <a name="request-body-data-rate-limit"></a>Omezení četnosti data těla požadavku

Ve výchozím nastavení, Kestrel server ukládá [minimální požadavek tělo přenosová rychlost](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>). Pro klienty streamování a streaming volání duplexní režim nemusí být splněny tato sazba a připojení může být vypršení časového limitu. Minimální požadavku limitu přenosové rychlosti dat musí být zakázáno, pokud obsahuje služba gRPC klienta streamování a streaming volání duplexní režim:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
         Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
        webBuilder.ConfigureKestrel((context, options) =>
        {
            options.Limits.MinRequestBodyDataRate = null;
        });
    });
}
```

## <a name="additional-resources"></a>Další zdroje

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
