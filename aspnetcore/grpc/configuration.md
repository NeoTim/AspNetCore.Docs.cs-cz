---
title: Konfigurace gRPC pro .NET
author: jamesnk
description: Přečtěte si, jak nakonfigurovat gRPC pro aplikace .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 02/26/2020
uid: grpc/configuration
ms.openlocfilehash: cabe2d86f535bf3063dd7ede9e8a3bc5de70e244
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666899"
---
# <a name="grpc-for-net-configuration"></a>Konfigurace gRPC pro .NET

## <a name="configure-services-options"></a>Konfigurace možností služeb

Služba gRPC Services je nakonfigurovaná s `AddGrpc` v *Startup.cs*. Následující tabulka popisuje možnosti konfigurace služeb gRPC:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | Maximální velikost zprávy v bajtech, která se dá odeslat ze serveru. Při pokusu o odeslání zprávy, která přesahuje nakonfigurovanou maximální velikost zprávy, dojde k výjimce. |
| MaxReceiveMessageSize | 4 MB | Maximální velikost zprávy v bajtech, kterou může server přijmout. Pokud server obdrží zprávu, která tento limit překračuje, vyvolá výjimku. Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti. |
| EnableDetailedErrors | `false` | Pokud `true`, jsou klientovi vraceny podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě služby. Výchozí formát je `false`. Nastavení `EnableDetailedErrors` `true` může způsobit nevracení citlivých informací. |
| CompressionProviders | gzip | Kolekce zprostředkovatelů komprese používaných ke komprimaci a dekompresi zpráv. Vlastní zprostředkovatelé komprese lze vytvořit a přidat do kolekce. Výchozí konfigurovaná zprostředkovatelé podporují kompresi **gzip** . |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | Kompresní algoritmus používaný ke kompresi zpráv odeslaných ze serveru. Algoritmus se musí shodovat se zprostředkovatelem komprese v `CompressionProviders`. Aby algoritmus mohl zkomprimovat odpověď, musí klient označit, že podporuje algoritmus, a to tak, že ho pošle v hlavičce **grpc-Accept-Encoding** . |
| ResponseCompressionLevel | `null` | Úroveň komprimace používaná ke kompresi zpráv odeslaných ze serveru. |
| Zachycovače | Žádná | Kolekce zachycení, které jsou spouštěny s každým voláním gRPC. Zachycení se spouští v pořadí, v jakém jsou zaregistrovaná. Globálně nakonfigurované zachycení se spouští před zachyceními nakonfigurovanými pro jednu službu. Další informace o zachycených gRPC naleznete v tématu [GRPC zachycení vs. middleware](xref:grpc/migration#grpc-interceptors-vs-middleware). |

Možnosti lze nakonfigurovat pro všechny služby tím, že poskytnete delegáty možností `AddGrpc` volání `Startup.ConfigureServices`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Možnosti pro jednu službu přepíší globální možnosti poskytované v `AddGrpc` a dají se nakonfigurovat pomocí `AddServiceOptions<TService>`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Konfigurace možností klienta

Konfigurace klienta gRPC je nastavená na `GrpcChannelOptions`. Následující tabulka popisuje možnosti konfigurace gRPCch kanálů:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| HttpClient | Nová instance | `HttpClient` slouží k volání gRPC. Klienta lze nastavit tak, aby nakonfiguroval vlastní `HttpClientHandler`, nebo přidat další obslužné rutiny do kanálu HTTP pro volání gRPC. Pokud není zadaný žádný `HttpClient`, vytvoří se pro kanál nová instance `HttpClient`. Bude automaticky vyřazen. |
| DisposeHttpClient | `false` | Pokud je zadána `true`a je zadán `HttpClient`, bude instance `HttpClient` po vyřazení `GrpcChannel` odstraněna. |
| LoggerFactory | `null` | `LoggerFactory`, kterou klient používá k protokolování informací o voláních gRPC. Instanci `LoggerFactory` lze přeložit z injektáže závislosti nebo vytvořit pomocí `LoggerFactory.Create`. Příklady konfigurace protokolování najdete v tématu <xref:grpc/diagnostics#grpc-client-logging>. |
| MaxSendMessageSize | `null` | Maximální velikost zprávy v bajtech, kterou je možné odeslat z klienta. Při pokusu o odeslání zprávy, která přesahuje nakonfigurovanou maximální velikost zprávy, dojde k výjimce. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | Maximální velikost zprávy v bajtech, kterou může klient přijmout. Pokud klient obdrží zprávu, která tento limit překračuje, vyvolá výjimku. Zvýšením této hodnoty umožníte klientovi přijímat větší zprávy, ale může negativně ovlivnit spotřebu paměti. |
| Přihlašovací údaje | `null` | Instance `ChannelCredentials`. Přihlašovací údaje se používají k přidání metadat ověřování pro volání gRPC. |
| CompressionProviders | gzip | Kolekce zprostředkovatelů komprese používaných ke komprimaci a dekompresi zpráv. Vlastní zprostředkovatelé komprese lze vytvořit a přidat do kolekce. Výchozí konfigurovaná zprostředkovatelé podporují kompresi **gzip** . |

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
