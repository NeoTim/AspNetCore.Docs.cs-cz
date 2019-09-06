---
title: gRPC pro konfiguraci ASP.NET Core
author: jamesnk
description: Naučte se nakonfigurovat gRPC pro aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/21/2019
uid: grpc/configuration
ms.openlocfilehash: 34eb598211c87fbb2c68ae5e041da50d02f543f7
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310317"
---
# <a name="grpc-for-aspnet-core-configuration"></a>gRPC pro konfiguraci ASP.NET Core

## <a name="configure-services-options"></a>Konfigurace možností služeb

Následující tabulka popisuje možnosti konfigurace služeb gRPC:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | Maximální velikost zprávy v bajtech, která se dá odeslat ze serveru. Při pokusu o odeslání zprávy, která přesahuje nakonfigurovanou maximální velikost zprávy, dojde k výjimce. |
| `MaxReceiveMessageSize` | 4 MB | Maximální velikost zprávy v bajtech, kterou může server přijmout. Pokud server obdrží zprávu, která tento limit překračuje, vyvolá výjimku. Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti. |
| `EnableDetailedErrors` | `false` | Pokud `true`se do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě služby. Výchozí hodnota je `false`. Nastavení `EnableDetailedErrors` na`true` může nevracení citlivých informací. |
| `CompressionProviders` | GZIP, uprostřed zúžené | Kolekce zprostředkovatelů komprese používaných ke komprimaci a dekompresi zpráv. Vlastní zprostředkovatelé komprese lze vytvořit a přidat do kolekce. Výchozí nakonfigurované zprostředkovatelé podporují kompresi **gzip** a **Deflate** . |
| `ResponseCompressionAlgorithm` | `null` | Kompresní algoritmus používaný ke kompresi zpráv odeslaných ze serveru. Algoritmus se musí shodovat se zprostředkovatelem komprese `CompressionProviders`v. Aby algoritmus mohl zkomprimovat odpověď, musí klient označit, že podporuje algoritmus, a to tak, že ho pošle v hlavičce **grpc-Accept-Encoding** . |
| `ResponseCompressionLevel` | `null` | Úroveň komprimace používaná ke kompresi zpráv odeslaných ze serveru. |

Možnosti lze nakonfigurovat pro všechny služby tím, že poskytnete možnosti delegáta `AddGrpc` pro volání `Startup.ConfigureServices`v:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Možnosti pro jednu službu přepíší globální možnosti poskytované v `AddGrpc` a můžou být nakonfigurované pomocí: `AddServiceOptions<TService>`

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Konfigurace možností klienta

Následující tabulka popisuje možnosti konfigurace gRPCch kanálů:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| `HttpClient` | Nová instance | `HttpClient` Slouží k provádění volání gRPC. Klienta lze nastavit tak, aby nakonfiguroval vlastní `HttpClientHandler`nastavení, nebo přidat další obslužné rutiny do kanálu HTTP pro volání gRPC. Ve výchozím nastavení je `HttpClient` vytvořena nová instance. |
| `MaxSendMessageSize` | `null` | Maximální velikost zprávy v bajtech, kterou je možné odeslat z klienta. Při pokusu o odeslání zprávy, která přesahuje nakonfigurovanou maximální velikost zprávy, dojde k výjimce. |
| `MaxReceiveMessageSize` | 4 MB | Maximální velikost zprávy v bajtech, kterou může klient přijmout. Pokud server obdrží zprávu, která tento limit překračuje, vyvolá výjimku. Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti. |
| `TransportOptions` | `null` | Možnosti přenosu konfigurují způsob, jakým kanál volá službu gRPC. V současné době jedinou implementací `HttpClientTransport` můžete určit, `HttpClient` které používá gRPC. |
| `Credentials` | `null` | `ChannelCredentials` Instance. Přihlašovací údaje se používají k přidání metadat ověřování pro volání gRPC. |
| `CompressionProviders` | GZIP, uprostřed zúžené | Kolekce zprostředkovatelů komprese používaných ke komprimaci a dekompresi zpráv. Vlastní zprostředkovatelé komprese lze vytvořit a přidat do kolekce. Výchozí nakonfigurované zprostředkovatelé podporují kompresi **gzip** a **Deflate** . |

Následující kód:

* Nastaví maximální velikost zprávy pro odesílání a přijímání na kanálu.
* Vytvoří klienta.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:tutorials/grpc/grpc-start>
