---
title: Správa verzí služeb gRPC
author: jamesnk
description: Přečtěte si, jak verzi gRPC služby.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
uid: grpc/versioning
ms.openlocfilehash: 9bd76009ba28a1abef25a98686afea6753d4a8f4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664113"
---
# <a name="versioning-grpc-services"></a>Správa verzí služeb gRPC

Podle [James Newton-King](https://twitter.com/jamesnk)

Nové funkce přidané do aplikace mohou vyžadovat, aby se služby gRPC poskytované klientům měnilo, někdy neočekávanými a neočekávanými způsoby. Když se změní služby gRPC:

* Je třeba zvážit, jaký dopad mají změny na klienty.
* Měla by být implementována strategie správy verzí pro podporu změn.

## <a name="backwards-compatibility"></a>Zpětná kompatibilita

Protokol gRPC je určen pro podporu služeb, které se v průběhu času mění. Obecně platí, že dodatky ke službám a metodám gRPC jsou nenarušující. Nenarušující změny umožňují stávajícím klientům pokračovat v práci beze změn. Změna nebo odstranění služeb gRPC narušují změny. Pokud mají služby gRPC narušující změny, klienti používající tuto službu musí být aktualizováni a znovu nasazeni.

Provádění nenarušujících změn ve službě má řadu výhod:

* Stávající klienti nadále běží.
* Vyhne se práci spojené s upozorňováním klientů na narušující změny a jejich aktualizací.
* Je třeba dokumentovat a udržovat pouze jednu verzi služby.

### <a name="non-breaking-changes"></a>Nenarušující změny

Tyto změny jsou nenarušující na úrovni protokolu gRPC a na binární úrovni .NET.

* **Přidání nové služby**
* **Přidání nové metody do služby**
* **Přidání pole do zprávy požadavku** - Pole přidaná ke zprávě požadavku jsou v případě, že nejsou nastavena, rekonstruována s výchozí [hodnotou](https://developers.google.com/protocol-buffers/docs/proto3#default) na serveru. Chcete-li být nerozdělitelné změny, musí služba uspět, pokud nové pole není nastaveno staršími klienty.
* **Přidání pole do zprávy odpovědi** - Pole přidaná ke zprávě odpovědi jsou dekonstruována do kolekce [neznámých polí](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) zprávy v klientovi.
* **Přidání hodnoty do výčtu** - výčty jsou serializovány jako číselná hodnota. Nové hodnoty výčtu jsou deserializovány na straně klienta na hodnotu výčtu bez názvu výčtu. Chcete-li být nenarušující změny, starší klienti musí pracovat správně při příjmu nové hodnoty výčtu.

### <a name="binary-breaking-changes"></a>Binární změny rozdělení

Následující změny jsou nerozdělitelné na úrovni protokolu gRPC, ale klient musí být aktualizován, pokud upgraduje na nejnovější *.proto* smlouvy nebo sestavení klienta .NET. Binární kompatibilita je důležité, pokud máte v plánu publikovat knihovnu gRPC nuget.

* **Odebrání pole** - Hodnoty z odebrané pole jsou rekonstruovány do [neznámých polí](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)zprávy . Toto není změna porušení protokolu gRPC, ale klient musí být aktualizován, pokud upgraduje na nejnovější smlouvu. Je důležité, aby odstraněné číslo pole nebylo v budoucnu omylem znovu použito. Chcete-li zajistit, aby k tomu nedošlo, zadejte odstraněná čísla polí a názvy ve zprávě pomocí [vyhrazeného](https://developers.google.com/protocol-buffers/docs/proto3#reserved) klíčového slova Protobuf.
* **Přejmenování zprávy** – názvy zpráv se obvykle neposílají v síti, takže se nejedná o změnu protokolu gRPC. Klient bude muset být aktualizován, pokud upgraduje na nejnovější smlouvu. Jedna situace, kdy **jsou** v síti odesílány názvy zpráv, je s [libovolnými](https://developers.google.com/protocol-buffers/docs/proto3#any) poli, pokud je název zprávy použit k identifikaci typu zprávy.
* **Změna csharp_namespace** `csharp_namespace` – Změna změní obor názvů generovaných typů .NET. Toto není změna porušení protokolu gRPC, ale klient musí být aktualizován, pokud upgraduje na nejnovější smlouvu.

### <a name="protocol-breaking-changes"></a>Změny porušující protokol

Následující položky jsou změny protokolu a binární horečné změny:

* **Přejmenování pole** - S obsahem Protobuf se názvy polí používají pouze v generovaném kódu. Číslo pole slouží k identifikaci polí v síti. Přejmenování pole není pro Protobufa změnou porušení protokolu. Pokud však server používá obsah JSON, je přejmenování pole narušující změnou.
* **Změna datového typu pole** - Změna datového typu pole na [nekompatibilní typ](https://developers.google.com/protocol-buffers/docs/proto3#updating) způsobí chyby při rekonstrukci zprávy. I v případě, že nový datový typ je kompatibilní, je pravděpodobné, že klient musí být aktualizovány na podporu nového typu, pokud upgraduje na nejnovější smlouvy.
* **Změna čísla pole** – U datových částí Protobuf se číslo pole používá k identifikaci polí v síti.
* **Přejmenování balíčku, služby nebo metody** - gRPC používá název balíčku, název služby a název metody k vytvoření adresy URL. Klient získá *stav UNIMPLEMENTED* ze serveru.
* **Odebrání služby nebo metody** - Klient získá *stav UNIMPLEMENTED* ze serveru při volání odebrané metody.

### <a name="behavior-breaking-changes"></a>Změny narušující chování

Při provádění nenarušujících změn je také nutné zvážit, zda starší klienti mohou pokračovat v práci s novým chováním služby. Například přidání nového pole do zprávy požadavku:

* Není změna protokolu, která porušuje protokol.
* Vrácení chybového stavu na serveru, pokud není nastaveno nové pole, způsobí, že je pro staré klienty narušující změnu.

Kompatibilita chování je určena kódem specifickým pro aplikaci.

## <a name="version-number-services"></a>Služby s číslem verze

Služby by měly usilovat o to, aby zůstaly zpětně kompatibilní se starými klienty. Změny aplikace mohou nakonec vyžadovat změny. Rozbití starých klientů a jejich vynucení k aktualizaci spolu s vaší službou není dobrým uživatelským prostředím. Způsob, jak zachovat zpětnou kompatibilitu při provádění narušujících změn, je publikovat více verzí služby.

gRPC podporuje volitelný specifikátor [balíčku,](https://developers.google.com/protocol-buffers/docs/proto3#packages) který funguje podobně jako obor názvů .NET. Ve skutečnosti `package` bude použit jako obor názvů .NET pro generované `option csharp_namespace` typy .NET, pokud není nastaven v souboru *.proto.* Balíček lze zadat číslo verze pro vaši službu a její zprávy:

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

Název balíčku je kombinován s názvem služby k identifikaci adresy služby. Adresa služby umožňuje hostovat více verzí služby vedle sebe:

* `greet.v1.Greeter`
* `greet.v2.Greeter`

Implementace služby s verzí jsou registrovány v *Startup.cs*:

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

Zahrnutí čísla verze do názvu balíčku vám dává možnost publikovat verzi *služby v2* s nejnovějšími změnami a zároveň nadále podporovat starší klienty, kteří volají verzi *v1.* Jakmile klienti aktualizovali, aby používali službu *v2,* můžete odebrat starou verzi. Při plánování publikování více verzí služby:

* Vyhněte se porušení změn, pokud je to rozumné.
* Neaktualizujte číslo verze, pokud neprovedete změny.
* Aktualizujte číslo verze při provádění změn.

Publikování více verzí služby ji duplikuje. Chcete-li snížit duplicitu, zvažte přesunutí obchodní logiky z implementace služby do centralizovaného umístění, které může být znovu použito starými a novými implementacemi:

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

Služby a zprávy generované různými názvy balíčků se **liší typy rozhraní .NET**. Přesunutí obchodní logiky do centralizovaného umístění vyžaduje mapování zpráv na běžné typy.
