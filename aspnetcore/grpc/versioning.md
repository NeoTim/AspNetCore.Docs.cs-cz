---
title: Správa verzí gRPC Services
author: jamesnk
description: Naučte se, jak verze služeb gRPC Services.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
uid: grpc/versioning
ms.openlocfilehash: 61dd5dfca4064af3ae58ac288a1ee636450ff56b
ms.sourcegitcommit: 79850db9e79b1705b89f466c6f2c961ff15485de
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75694206"
---
# <a name="versioning-grpc-services"></a>Správa verzí gRPC Services

Od [James Newton – král](https://twitter.com/jamesnk)

Nové funkce přidané do aplikace můžou vyžadovat, aby se služby gRPC mohly změnit, někdy v neočekávaných a rozlomených způsobech. Když se změní služba gRPC Services:

* Je třeba věnovat pozornost vlivu změn na klienty.
* Měla by se implementovat strategie správy verzí, která podporuje změny.

## <a name="backwards-compatibility"></a>Zpětná kompatibilita

Protokol gRPC je navržený tak, aby podporoval služby, které se v průběhu času mění. Obecně se doplňují služby a metody gRPC, které nejsou v chodu. Nerozdělování znamená, že stávající klienti budou fungovat i nadále. Změna nebo odstranění služeb gRPC Services se mění. Zásadní změny znamenají selhání stávajících klientů.

Provádění nerozhodujících změn služby má několik výhod:

- Stávající klienti pokračují v běhu.
- Zabrání práci, která je součástí upozorňování klientů na zásadní změny, a jejich aktualizaci.
- Musí být dokumentována a udržována pouze jedna verze služby.

Tento obsah se zaměřuje na to, jestli se změny ruší **v protokolu gRPC a v binární úrovni kompatibility .NET**. Při provádění změn zvažte, jestli můžou starší klienti logicky pokračovat v práci. Například přidání nového pole do zprávy žádosti:

* Nejedná se o změnu v důsledku přerušení protokolu.
* Vrátí stav chyby na serveru, pokud není nové pole nastaveno pro původní klienty jako zásadní změnu.

### <a name="non-breaking-changes"></a>Neprůlomové změny

Tyto změny jsou nerozdělitelné na úrovni protokolu gRPC a na binární úrovni .NET.

- **Přidává se nová služba.**
- **Přidání nové metody do služby**
- **Přidání pole do zprávy žádosti** – pole přidaná do zprávy požadavku jsou deserializována s [výchozí hodnotou](https://developers.google.com/protocol-buffers/docs/proto3#default) na serveru, pokud není nastavena. Pokud nechcete, aby služba byla neúspěšná, bude nutné, aby byla úspěšná, když ji starší klienti nenastaví.
- **Přidání pole do zprávy odpovědi** – pole přidaná do zprávy odpovědi jsou deserializována do kolekce [neznámá pole](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) zprávy na klientovi.
- **Přidání hodnoty do** výčtového výčtového typu je serializováno jako číselná hodnota. Nové hodnoty výčtu jsou v klientovi deserializovatelné na hodnotu výčtu bez názvu výčtu. Pokud chcete, aby starší klienti nemuseli fungovat, musí se správně spustit, když dostanou a nevýjimkou hodnoty.

### <a name="binary-breaking-changes"></a>Binární přerušující změny

Následující změny nejsou rozstupné na úrovni protokolu gRPC, ale pokud se upgradují na nejnovější verzi, je potřeba, aby byl klient aktualizovaný *. proto* se jedná o kontrakt nebo klientské sestavení .NET. Binární kompatibilita je důležitá, pokud plánujete publikovat knihovnu gRPC do NuGet.

- **Odebrání hodnot pole** z odebraného pole je deserializovatelné na [neznámá pole](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)zprávy. Nejedná se o změnu gRPC protokolu, ale pokud se upgraduje na nejnovější verzi, je potřeba aktualizovat klienta. Je důležité, aby odebrané číslo pole v budoucnu nechtěně znovu nepoužívalo. Jedním ze způsobů, jak zajistit, aby k tomu nedocházelo, je zadání čísel a názvů odstraněných polí ve zprávě pomocí klíčového slova [`reserved`](https://developers.google.com/protocol-buffers/docs/proto3#reserved) Protobuf.
- **Přejmenování** polí – názvy polí se používají pouze ve vygenerovaném kódu. Číslo pole se používá k identifikaci polí v síti. Klient bude muset být aktualizován, pokud se upgraduje na nejnovější kontrakt.
- **Přejmenování** zpráv – názvy zpráv nejsou odesílány v síti, takže se nejedná o gRPC změnu protokolu, ale klient bude muset být aktualizován, pokud upgraduje na nejnovější kontrakt.
- **Změna csharp_namespace** -změna `csharp_namespace` změní obor názvů vygenerovaných typů .NET. Nejedná se o změnu gRPC protokolu, ale pokud se upgraduje na nejnovější verzi, je potřeba aktualizovat klienta.

### <a name="breaking-changes"></a>Změny způsobující chyby

Jedná se o protokol a binární změny.

- **Změna datového typu pole** – Změna datového typu pole na [nekompatibilní typ](https://developers.google.com/protocol-buffers/docs/proto3#updating) způsobí chyby při deserializaci zprávy. I v případě, že je nový datový typ kompatibilní, je pravděpodobně potřeba aktualizovat klienta, aby podporoval nový typ, pokud se upgraduje na nejnovější kontrakt.
- **Změna čísla pole** – číslo pole se používá k identifikaci polí v síti.
- **Přejmenování balíčku, služby nebo metody** – gRPC používá k sestavení adresy URL název balíčku, název služby a název metody. Klient získá *Neimplementovaný* stav ze serveru.
- **Odebrání služby nebo metody** – klient získá *Neimplementovaný* stav ze serveru při volání odebrané metody.

## <a name="version-number-services"></a>Číslo verze služeb

Služby by se měly snažit, aby zůstaly zpětně kompatibilní se starými klienty. Nakonec změny vaší aplikace mohou vyžadovat zásadní změny. Poškození starých klientů a jejich vynucování spolu s vaší službou není dobrým uživatelským prostředím. Způsob, jak udržet zpětnou kompatibilitu při provádění zásadních změn, je publikování více verzí služby.

gRPC podporuje volitelný specifikátor [`package`](https://developers.google.com/protocol-buffers/docs/proto3#packages) , který funguje podobně jako obor názvů .NET. V takovém případě se `package` použije jako obor názvů .NET pro vygenerované typy .NET, pokud `option csharp_namespace` není nastaveno v souboru *..* . Balíček lze použít k zadání čísla verze vaší služby a jejích zpráv:

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

Název balíčku je v kombinaci s názvem služby k identifikaci adresy služby. Adresa služby umožňuje souběžně hostovat více verzí služby:

* `greet.v1.Greeter`
* `greet.v2.Greeter`

Implementace služby se správou verzí jsou zaregistrované v *Startup.cs*:

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

Zahrnutím čísla verze do názvu balíčku získáte možnost publikovat verzi *v2* vaší služby s nezměněnými změnami, a to i nadále podporovat starší klienty, kteří volají verzi *v1* . Jakmile se klienti aktualizují, aby používali službu *v2* , můžete si odebrat starou verzi. Při plánování publikování více verzí služby:

- Nepoužívejte závažné změny, pokud je to rozumné.
- Neaktualizujte číslo verze, pokud neprovádíte zásadní změny.
- Aktualizujte číslo verze, když provedete průlomové změny.

Publikování více verzí služby je duplicitní. Pokud chcete omezit duplicity, zvažte přesunutí obchodní logiky ze implementací služby do centralizovaného umístění, které je možné použít starými a novými implementacemi:

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

Služby a zprávy vygenerované s různými názvy balíčků jsou **různé typy rozhraní .NET**. Přesun obchodní logiky do centralizovaného umístění vyžaduje mapování zpráv na společné typy.
