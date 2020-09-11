---
title: Proč migrovat WCF na ASP.NET Core gRPC
author: markrendle
description: Tento článek poskytuje přehled důvodů, proč je ASP.NET Core gRPC vhodný pro vývojáře Windows Communication Foundation (WCF), kteří chtějí migrovat na moderní architektury a platformy.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 811e6037b058b26fcf91063123d04d448a9a28a8
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90012756"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a>gRPC pro vývojáře pro Windows Communication Foundation (WCF)

Tento článek poskytuje přehled důvodů, proč je ASP.NET Core gRPC vhodný pro vývojáře Windows Communication Foundation (WCF), kteří chtějí migrovat na moderní architektury a platformy.

## <a name="comparison-to-wcf"></a>Porovnání s WCF

I když je implementace a přístup odlišná pro gRPC, možnosti vývoje a využívání služeb s gRPC by měly být intuitivní pro vývojáře WCF. WCF a gRPC jsou architektury RPC (Remote Procedure Call) se stejnými cíli:

* Umožňuje psaní kódu, jako by klient a server byly na stejné platformě.
* Poskytněte zjednodušené přenosné síťové rozhraní API.

Obě platformy sdílejí požadavky na deklaraci a implementaci rozhraní, i když proces pro deklaraci rozhraní je jiný. Mnoho typů volání RPC, které gRPC podporuje, je dobře mapované na vazby, které jsou dostupné pro služby WCF. Další informace a příklady najdete v tématu [migrace řešení WCF do gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).

## <a name="benefits-of-grpc"></a>Výhody gRPC

gRPC poskytuje lepší rámec než jiné přístupy z následujících důvodů.

### <a name="performance"></a>Výkon

gRPC používá protokol HTTP/2. Na rozdíl od HTTP/1.1, HTTP/2:

* Je menší a rychlejší binární protokol.
* Je efektivnější pro počítače k analýze.
* Podporuje multiplexování požadavků v rámci jednoho připojení. Multiplexování umožňuje odesílání více požadavků přes jedno připojení bez nutnosti vzájemného blokování požadavků. V HTTP/1.1 se blokování nazývá "blokování na konci řádku (HOL)".

gRPC používá ke serializaci zpráv Protobuf efektivní binární formát. Protobuf zprávy:
* Rychlá serializace a deserializace.
* Používejte méně šířky pásma než textové formáty. 

gRPC je vhodné řešení pro mobilní zařízení a sítě s omezeními šířky pásma.

### <a name="interoperability"></a>Interoperabilita

Existují nástroje a knihovny gRPC pro všechny hlavní programovací jazyky a platformy, včetně .NET, Java, Python, přejít, C++, Node.js, SWIFT, DART, Ruby a PHP. Díky Protobuf binárnímu formátu a efektivnímu generování kódu pro každou platformu můžou vývojáři vytvářet aplikace pro různé platformy a provádět aplikace.

### <a name="usability-and-productivity"></a>Použitelnost a produktivita

gRPC je komplexní řešení RPC. Funguje konzistentně napříč různými jazyky a platformami. Nabízí také skvělé nástroje, které jsou automaticky generovány velkým často používaným kódem. Podobně jako WCF, gRPC automaticky generuje zprávy a klienta silného typu. Čas vývojáře je zdarma zaměřen na obchodní logiku.

### <a name="streaming"></a>Streamování

gRPC má plný obousměrný Stream, který poskytuje podobné funkce jako plně duplexní služby WCF. gRPC streaming může pracovat přes běžná připojení k Internetu, nástroje pro vyrovnávání zatížení a sítě.

### <a name="deadlines-timeouts-and-cancellation"></a>Konečné termíny, časové limity a zrušení

gRPC umožňuje klientům zadat maximální dobu, po kterou se RPC bude dokončit. Pokud je překročen zadaný konečný termín, server může operaci zrušit nezávisle na klientovi. Termíny a zrušení se dají rozšířit prostřednictvím dalších volání gRPC, která vám pomůžou vyhodnotit omezení využití prostředků. Klienti mohou zastavit operace, pokud je překročen konečný termín nebo v případě potřeby předchozí. Například klienti mohou zastavit operace z důvodu interakce s uživatelem.

### <a name="security"></a>Zabezpečení

gRPC může používat protokoly TLS a HTTP/2 k zajištění uceleného šifrovaného připojení mezi klientem a serverem. Podpora ověřování klientských certifikátů dále zvyšuje zabezpečení a důvěryhodnost mezi klientem a serverem.

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a>gRPC jako cestu migrace pro WCF do .NET Core a .NET 5

Rozhraní .NET Core a .NET 5 označí Shift způsob, jakým společnost Microsoft dodává řešení pro vzdálenou komunikaci vývojářům, kteří chtějí poskytovat služby v rámci celé řady platforem. .NET Core a .NET 5 podporují [volání služeb WCF](/dotnet/core/additional-tools/wcf-web-service-reference-guide), ale nenabízejí podporu na straně serveru pro hostování WCF.

Existují dva doporučené cesty pro modernizaci aplikace WCF:

* gRPC je postaven na moderních technologiích a je známý jako nejoblíbenější volba napříč komunitou vývojářů pro aplikace RPC. Počínaje .NET Core 3,0 mají moderní platformy .NET vynikající podporu pro gRPC. Migrace služeb WCF na použití gRPC pomáhá poskytovat funkce RPC, výkon a interoperabilitu, která je potřeba v moderních aplikacích.

* [CoreWCF](https://github.com/CoreWCF/CoreWCF) je snaha komunity o poskytování podpory pro hostování služeb WCF do .NET Core a .NET 5. K dispozici je verze Preview a projekt pracuje směrem k připravené výrobě. CoreWCF podporuje pouze podmnožinu funkcí WCF a .NET Framework aplikace, které migrují na použití, budou potřebovat změny kódu a testování, aby byly úspěšné. CoreWCF je dobrou volbou v případě, že aplikace musí zachovat kompatibilitu se stávajícími klienty, kteří volají služby WCF.

## <a name="get-started"></a>Začínáme

Podrobné pokyny k vytváření gRPC Services v ASP.NET Core pro vývojáře WCF najdete v tématu [ASP.NET Core gRPC pro vývojáře WCF](/dotnet/architecture/grpc-for-wcf-developers) .
