---
title: Úvod do gRPC v ASP.NET Core
author: juntaoluo
description: Další informace o službách gRPC Kestrel serveru a ASP.NET Core zásobník.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 02/26/2019
uid: grpc/index
ms.openlocfilehash: dd1c42744bfda965df91ea1fcc0b71814317b969
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085543"
---
# <a name="introduction-to-grpc-on-aspnet-core"></a>Úvod do gRPC v ASP.NET Core

Podle [Luo Jan](https://github.com/juntaoluo)

[gRPC](https://grpc.io/docs/guides/) je jazyk nezávislá, vysoce výkonné rozhraní vzdáleného volání procedur (RPC). Další informace o gRPC základy, najdete v článku [stránky dokumentace gRPC](https://grpc.io/docs/).

Hlavní výhody gRPC jsou:
* Moderní zjednodušené RPC architektura s vysokým výkonem.
* Vývoj rozhraní API kontraktem, ve výchozím nastavení, umožňující bez implementace jazyka pomocí Protocol Buffers.
* Nástroje dostupné pro řadu jazyků pro generování silného typu serverů a klientů.
* Podporuje klient, server a obousměrné streamování volání.
* Snížená síťová využití pomocí Protobuf binární serializace.

Mezi tyto výhody proveďte gRPC ideální pro:
* Jednoduché mikroslužby, kde efektivita je velmi důležité.
* Polyglot systémy, kde se vyžadují pro vývoj pro více jazyků.
* Point-to-Point v reálném čase služby, které je třeba pro zpracování datových proudů žádostí a odpovědí.

Zatímco C# implementace je teď dostupná pro official je přínosné pro [gRPC stránky](https://grpc.io/docs/quickstart/csharp.html), aktuální implementace závisí na nativní knihovny napsané v jazyce C (gRPC [C – jádro](https://grpc.io/blog/grpc-stacks)). Práce probíhá aktuálně k dispozici nové implementace založené na server Kestrel HTTP a ASP.NET Core zásobníku, která je plně spravovaná. Následující dokumenty poskytují úvod do vytváření gRPC služeb s touto novou implementací.

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/basics>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>