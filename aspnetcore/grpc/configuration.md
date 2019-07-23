---
title: gRPC konfigurace ASP.NET Core
author: jamesnk
description: Zjistěte, jak nakonfigurovat gRPC pro aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 05/30/2019
uid: grpc/configuration
ms.openlocfilehash: e269d701f45c0b852a9006107f0162cc5af2c38a
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814927"
---
# <a name="grpc-for-aspnet-core-configuration"></a>gRPC konfigurace ASP.NET Core

## <a name="configure-services-options"></a>Nakonfigurujte možnosti služby

Následující tabulka popisuje možnosti pro konfiguraci služby gRPC:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | Maximální velikost zprávy v bajtech odeslaných ze serveru. Došlo k pokusu o odeslání zprávy, která překračuje velikost výsledky nakonfigurovanou maximální zprávy výjimek. |
| `ReceiveMaxMessageSize` | 4 MB | Maximální velikost zprávy v bajtech, které lze přijímat pomocí serveru. Pokud server přijme zprávu, která překračuje tento limit, dojde k výjimce. Zvýšení hodnoty tuto umožňuje serveru pro příjem větší zprávy, ale může mít negativní vliv na využití paměti. |
| `EnableDetailedErrors` | `false` | Pokud `true`, podrobné zprávy o výjimkách se vrátí ke klientům, když dojde k výjimce v metodě služby. Výchozí hodnota je `false`. Nastavení `EnableDetailedErrors` k `true` může způsobit únik citlivých informací. |
| `CompressionProviders` | gzip | Kolekce zprostředkovatelů komprese používá při komprimaci a dekomprimaci zprávy. Poskytovatelé vlastního komprese můžete vytvořen a přidán do kolekce. Výchozí nastavení nakonfigurován zprostředkovatel podporuje **gzip** komprese. |
| `ResponseCompressionAlgorithm` | `null` | Komprese algoritmus používaný ke kompresi zpráv odeslaných ze serveru. Algoritmus musí odpovídat komprese zprostředkovatele v `CompressionProviders`. Pro algoritmus pro kompresi odpovědí, musíte uvést podporuje algoritmus a odeslat ho klienta **grpc přijmout – kódování** záhlaví. |
| `ResponseCompressionLevel` | `null` | Úroveň komprese použitý ke kompresi zpráv odeslaných ze serveru. |

Je možné nakonfigurovat možnosti pro všechny služby poskytnutím delegáta možnosti k `AddGrpc` volání v `Startup.ConfigureServices`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Možnosti pro jednu službu přepsat globální možnosti uvedené v `AddGrpc` a dá se nakonfigurovat pomocí `AddServiceOptions<TService>`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Konfigurovat možnosti klienta

Následující kód nastaví maximální odeslat klienta a přijímat zprávy velikost:

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-6)]

## <a name="additional-resources"></a>Další zdroje

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
