---
title: gRPC pro konfiguraci rozhraní .NET
author: jamesnk
description: Přečtěte si, jak nakonfigurovat gRPC pro aplikace .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 02/26/2020
uid: grpc/configuration
ms.openlocfilehash: 4c13c45ce745643c3cb089a1c984d2ef599db48b
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440828"
---
# <a name="grpc-for-net-configuration"></a>gRPC pro konfiguraci rozhraní .NET

## <a name="configure-services-options"></a>Konfigurace možností služeb

služby gRPC `AddGrpc` jsou konfigurovány v *Startup.cs*. Následující tabulka popisuje možnosti konfigurace služeb gRPC:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| MaxSendMessageVelikost | `null` | Maximální velikost zprávy v bajtů, které mohou být odeslány ze serveru. Pokus o odeslání zprávy, která překračuje nakonfigurovanou maximální velikost zprávy, má za následek výjimku. Pokud je `null`nastavena na , velikost zprávy je neomezená. |
| Velikost zprávy MaxReceiveMessage | 4 MB | Maximální velikost zprávy v bajtů, které mohou být přijaty serverem. Pokud server obdrží zprávu, která překračuje tento limit, vyvolá výjimku. Zvýšení této hodnoty umožňuje serveru přijímat větší zprávy, ale může negativně ovlivnit spotřebu paměti. Pokud je `null`nastavena na , velikost zprávy je neomezená. |
| EnableDetailedErrors | `false` | Pokud `true`, podrobné zprávy o výjimce jsou vráceny klientům při vyvolání výjimky v metodě služby. Výchozí formát je `false`. Nastavení `EnableDetailedErrors` `true` může unikat citlivé informace. |
| Poskytovatelé komprese | Gzip | Kolekce zprostředkovatelů komprese slouží ke kompresi a dekompresi zpráv. Vlastní zprostředkovatelé komprese lze vytvořit a přidat do kolekce. Výchozí nakonfigurované zprostředkovatele podporují kompresi **gzip.** |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | Kompresní algoritmus používaný ke kompresi zpráv odeslaných ze serveru. Algoritmus musí odpovídat `CompressionProviders`zprostředkovateli komprese v . Aby algoritmus komprimoval odpověď, musí klient uvést, že podporuje algoritmus odesláním v záhlaví **grpc-accept-encoding.** |
| Úroveň komprese odezvy | `null` | Úroveň komprese používaná ke kompresi zpráv odeslaných ze serveru. |
| Zachycovače | Žádná | Kolekce zachycovače, které jsou spuštěny s každým voláním gRPC. Stíhače jsou spuštěny v pořadí, v jakém jsou registrovány. Globálně konfigurované zachycovače jsou spuštěny před interceptory nakonfigurovanými pro jednu službu. Další informace o zachycovačích gRPC naleznete [v tématu gRPC Interceptors vs. Middleware](xref:grpc/migration#grpc-interceptors-vs-middleware). |

Možnosti lze nakonfigurovat pro všechny služby `AddGrpc` poskytnutím možnosti delegáta volání v aplikaci `Startup.ConfigureServices`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Možnosti pro jednu službu přepíší `AddGrpc` globální možnosti `AddServiceOptions<TService>`poskytované v aplikaci a lze je konfigurovat pomocí :

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Konfigurace možností klienta

gRPC konfigurace klienta `GrpcChannelOptions`je nastavena na . Následující tabulka popisuje možnosti konfigurace kanálů gRPC:

| Možnost | Výchozí hodnota | Popis |
| ------ | ------------- | ----------- |
| HttpClient | Nová instance | Slouží `HttpClient` k volání gRPC. Klienta lze nastavit tak, `HttpClientHandler`aby nakonfiguroval vlastní nebo přidal další obslužné rutiny do kanálu HTTP pro volání gRPC. Pokud `HttpClient` není zadána žádná, je pro kanál vytvořena nová `HttpClient` instance. Bude automaticky zlikvidován. |
| DisposehttpClient | `false` | Pokud `true`, `HttpClient` a je zadán, pak `HttpClient` instance bude `GrpcChannel` uvolněna při vyřazení. |
| Továrna na dřevorubce | `null` | Používá `LoggerFactory` klient protokolu informací o volání gRPC. Instance `LoggerFactory` může být vyřešena z vkládání `LoggerFactory.Create`závislostí nebo vytvořena pomocí . Příklady konfigurace protokolování naleznete <xref:grpc/diagnostics#grpc-client-logging>v tématu . |
| MaxSendMessageVelikost | `null` | Maximální velikost zprávy v bajtů, které mohou být odeslány z klienta. Pokus o odeslání zprávy, která překračuje nakonfigurovanou maximální velikost zprávy, má za následek výjimku. Pokud je `null`nastavena na , velikost zprávy je neomezená. |
| <span style="word-break:normal;word-wrap:normal">Velikost zprávy MaxReceiveMessage</span> | 4 MB | Maximální velikost zprávy v bajtů, které mohou být přijaty klientem. Pokud klient obdrží zprávu, která překračuje tento limit, vyvolá výjimku. Zvýšení této hodnoty umožňuje klientovi přijímat větší zprávy, ale může negativně ovlivnit spotřebu paměti. Pokud je `null`nastavena na , velikost zprávy je neomezená. |
| Přihlašovací údaje | `null` | Instance. `ChannelCredentials` Přihlašovací údaje se používají k přidání metadat ověřování do volání gRPC. |
| Poskytovatelé komprese | Gzip | Kolekce zprostředkovatelů komprese slouží ke kompresi a dekompresi zpráv. Vlastní zprostředkovatelé komprese lze vytvořit a přidat do kolekce. Výchozí nakonfigurované zprostředkovatele podporují kompresi **gzip.** |

Následující kód:

* Nastaví maximální velikost odesílaných a přijímaných zpráv v kanálu.
* Vytvoří klienta.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
