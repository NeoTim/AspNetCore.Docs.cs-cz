---
title: Konfigurace gRPC pro .NET
author: jamesnk
description: Přečtěte si, jak nakonfigurovat gRPC pro aplikace .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 05/26/2020
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
uid: grpc/configuration
ms.openlocfilehash: 8a4f518e30432a79151ec34a7092123c390f4d5d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631144"
---
# <a name="grpc-for-net-configuration"></a>Konfigurace gRPC pro .NET

## <a name="configure-services-options"></a>Konfigurace možností služeb

služby gRPC Services se konfigurují `AddGrpc` v *Startup.cs*. Následující tabulka popisuje možnosti konfigurace služeb gRPC:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | Maximální velikost zprávy v bajtech, která se dá odeslat ze serveru. Při pokusu o odeslání zprávy, která přesahuje nakonfigurovanou maximální velikost zprávy, dojde k výjimce. Při nastavení na je `null` velikost zprávy neomezená. |
| MaxReceiveMessageSize | 4 MB | Maximální velikost zprávy v bajtech, kterou může server přijmout. Pokud server obdrží zprávu, která tento limit překračuje, vyvolá výjimku. Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti. Při nastavení na je `null` velikost zprávy neomezená. |
| EnableDetailedErrors | `false` | Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě služby. Výchozí formát je `false`. Nastavení `EnableDetailedErrors` na `true` může nevracení citlivých informací. |
| CompressionProviders | GZIP | Kolekce zprostředkovatelů komprese používaných ke komprimaci a dekompresi zpráv. Vlastní zprostředkovatelé komprese lze vytvořit a přidat do kolekce. Výchozí konfigurovaná zprostředkovatelé podporují kompresi **gzip** . |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | Kompresní algoritmus používaný ke kompresi zpráv odeslaných ze serveru. Algoritmus se musí shodovat se zprostředkovatelem komprese v `CompressionProviders` . Aby algoritmus mohl zkomprimovat odpověď, musí klient označit, že podporuje algoritmus, a to tak, že ho pošle v hlavičce **grpc-Accept-Encoding** . |
| ResponseCompressionLevel | `null` | Úroveň komprimace používaná ke kompresi zpráv odeslaných ze serveru. |
| Zachycovače | Žádné | Kolekce zachycení, které jsou spouštěny s každým voláním gRPC. Zachycení se spouští v pořadí, v jakém jsou zaregistrovaná. Globálně nakonfigurované zachycení se spouští před zachyceními nakonfigurovanými pro jednu službu. Další informace o zachycených gRPC naleznete v tématu [GRPC zachycení vs. middleware](xref:grpc/migration#grpc-interceptors-vs-middleware). |
| IgnoreUnknownServices | `false` | Pokud `true` volání neznámých služeb a metod nevrátí **Neimplementovaný** stav a požadavek projde do dalšího registrovaného middlewaru v ASP.NET Core. |

Možnosti lze nakonfigurovat pro všechny služby tím, že poskytnete možnosti delegáta pro `AddGrpc` volání v `Startup.ConfigureServices` :

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Možnosti pro jednu službu přepíší globální možnosti poskytované v `AddGrpc` a můžou být nakonfigurované pomocí `AddServiceOptions<TService>` :

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Konfigurace možností klienta

Konfigurace klienta gRPC je nastavená na `GrpcChannelOptions` . Následující tabulka popisuje možnosti konfigurace gRPCch kanálů:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| HttpHandler | Nová instance | `HttpMessageHandler`Slouží k provádění volání gRPC. Klienta lze nastavit tak, aby nakonfiguroval vlastní `HttpClientHandler` nebo přidal další obslužné rutiny do kanálu HTTP pro volání gRPC. Pokud `HttpMessageHandler` není zadaný, vytvoří se `HttpClientHandler` pro kanál nová instance s automatickým vyřazením. |
| HttpClient | `null` | `HttpClient`Slouží k provádění volání gRPC. Toto nastavení je alternativou k `HttpHandler` . |
| DisposeHttpClient | `false` | Je-li nastavena na `true` a `HttpMessageHandler` nebo `HttpClient` je zadána možnost nebo, pak buď `HttpHandler` nebo `HttpClient` , v uvedeném pořadí, je uvolněna při `GrpcChannel` vyřazení. |
| LoggerFactory | `null` | `LoggerFactory`Klient používaný klientem k protokolování informací o voláních gRPC. `LoggerFactory`Instanci lze přeložit z injektáže závislosti nebo vytvořit pomocí `LoggerFactory.Create` . Příklady konfigurace protokolování najdete v tématu <xref:grpc/diagnostics#grpc-client-logging> . |
| MaxSendMessageSize | `null` | Maximální velikost zprávy v bajtech, kterou je možné odeslat z klienta. Při pokusu o odeslání zprávy, která přesahuje nakonfigurovanou maximální velikost zprávy, dojde k výjimce. Při nastavení na je `null` velikost zprávy neomezená. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | Maximální velikost zprávy v bajtech, kterou může klient přijmout. Pokud klient obdrží zprávu, která tento limit překračuje, vyvolá výjimku. Zvýšením této hodnoty umožníte klientovi přijímat větší zprávy, ale může negativně ovlivnit spotřebu paměti. Při nastavení na je `null` velikost zprávy neomezená. |
| Přihlašovací údaje | `null` | `ChannelCredentials`Instance. Přihlašovací údaje se používají k přidání metadat ověřování pro volání gRPC. |
| CompressionProviders | GZIP | Kolekce zprostředkovatelů komprese používaných ke komprimaci a dekompresi zpráv. Vlastní zprostředkovatelé komprese lze vytvořit a přidat do kolekce. Výchozí konfigurovaná zprostředkovatelé podporují kompresi **gzip** . |

Následující kód:

* Nastaví maximální velikost zprávy pro odesílání a přijímání na kanálu.
* Vytvoří klienta.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Další materiály

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
