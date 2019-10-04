---
title: Konfigurace gRPC pro .NET
author: jamesnk
description: Přečtěte si, jak nakonfigurovat gRPC pro aplikace .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/configuration
ms.openlocfilehash: 3ef92f10d914ef9fa3e13a7bdd5c863bab297f57
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925209"
---
# <a name="grpc-for-net-configuration"></a>Konfigurace gRPC pro .NET

## <a name="configure-services-options"></a>Konfigurace možností služeb

služby gRPC Services se konfigurují `AddGrpc` v *Startup.cs*. Následující tabulka popisuje možnosti konfigurace služeb gRPC:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | Maximální velikost zprávy v bajtech, která se dá odeslat ze serveru. Při pokusu o odeslání zprávy, která přesahuje nakonfigurovanou maximální velikost zprávy, dojde k výjimce. |
| `MaxReceiveMessageSize` | 4 MB | Maximální velikost zprávy v bajtech, kterou může server přijmout. Pokud server obdrží zprávu, která tento limit překračuje, vyvolá výjimku. Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti. |
| `EnableDetailedErrors` | `false` | Pokud `true`se do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě služby. Výchozí hodnota je `false`. Nastavení `EnableDetailedErrors` na`true` může nevracení citlivých informací. |
| `CompressionProviders` | gzip | Kolekce zprostředkovatelů komprese používaných ke komprimaci a dekompresi zpráv. Vlastní zprostředkovatelé komprese lze vytvořit a přidat do kolekce. Výchozí konfigurovaná zprostředkovatelé podporují kompresi **gzip** . |
| `ResponseCompressionAlgorithm` | `null` | Kompresní algoritmus používaný ke kompresi zpráv odeslaných ze serveru. Algoritmus se musí shodovat se zprostředkovatelem komprese `CompressionProviders`v. Aby algoritmus mohl zkomprimovat odpověď, musí klient označit, že podporuje algoritmus, a to tak, že ho pošle v hlavičce **grpc-Accept-Encoding** . |
| `ResponseCompressionLevel` | `null` | Úroveň komprimace používaná ke kompresi zpráv odeslaných ze serveru. |

Možnosti lze nakonfigurovat pro všechny služby tím, že poskytnete možnosti delegáta `AddGrpc` pro volání `Startup.ConfigureServices`v:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Možnosti pro jednu službu přepíší globální možnosti poskytované v `AddGrpc` a můžou být nakonfigurované pomocí: `AddServiceOptions<TService>`

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Konfigurace možností klienta

Konfigurace klienta gRPC je nastavená `GrpcChannelOptions`na. Následující tabulka popisuje možnosti konfigurace gRPCch kanálů:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `HttpClient` | Nová instance | `HttpClient` Slouží k provádění volání gRPC. Klienta lze nastavit tak, aby nakonfiguroval vlastní `HttpClientHandler`nastavení, nebo přidat další obslužné rutiny do kanálu HTTP pro volání gRPC. Pokud není zadaný, vytvoří se pro kanál `HttpClient` nová instance. `HttpClient` Bude automaticky vyřazen. |
| `DisposeHttpClient` | `false` | Pokud `true` a`HttpClient` `GrpcChannel` je zadán, instance bude uvolněna při uvolnění. `HttpClient` |
| `LoggerFactory` | `null` | Klient `LoggerFactory` používaný klientem k protokolování informací o voláních gRPC. Instanci lze přeložit z injektáže závislosti nebo vytvořit pomocí `LoggerFactory.Create`. `LoggerFactory` Příklady konfigurace protokolování najdete v tématu <xref:grpc/diagnostics#grpc-client-logging>. |
| `MaxSendMessageSize` | `null` | Maximální velikost zprávy v bajtech, kterou je možné odeslat z klienta. Při pokusu o odeslání zprávy, která přesahuje nakonfigurovanou maximální velikost zprávy, dojde k výjimce. |
| `MaxReceiveMessageSize` | 4 MB | Maximální velikost zprávy v bajtech, kterou může klient přijmout. Pokud klient obdrží zprávu, která tento limit překračuje, vyvolá výjimku. Zvýšením této hodnoty umožníte klientovi přijímat větší zprávy, ale může negativně ovlivnit spotřebu paměti. |
| `Credentials` | `null` | `ChannelCredentials` Instance. Přihlašovací údaje se používají k přidání metadat ověřování pro volání gRPC. |
| `CompressionProviders` | gzip | Kolekce zprostředkovatelů komprese používaných ke komprimaci a dekompresi zpráv. Vlastní zprostředkovatelé komprese lze vytvořit a přidat do kolekce. Výchozí konfigurovaná zprostředkovatelé podporují kompresi **gzip** . |

Následující kód:

* Nastaví maximální velikost zprávy pro odesílání a přijímání na kanálu.
* Vytvoří klienta.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
