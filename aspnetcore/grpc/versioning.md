---
title: Správa verzí služeb gRPC
author: jamesnk
description: Naučte se, jak verze služeb gRPC Services.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/versioning
ms.openlocfilehash: 087ce449b0d8ab1eaf3434ad64feeff78c1fee22
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015956"
---
# <a name="versioning-grpc-services"></a>Správa verzí služeb gRPC

Od [James Newton – král](https://twitter.com/jamesnk)

Nové funkce přidané do aplikace můžou vyžadovat, aby se služby gRPC mohly změnit, někdy v neočekávaných a rozlomených způsobech. Když se změní služba gRPC Services:

* Je třeba věnovat pozornost vlivu změn na klienty.
* Měla by se implementovat strategie správy verzí, která podporuje změny.

## <a name="backwards-compatibility"></a>Zpětná kompatibilita

Protokol gRPC je navržený tak, aby podporoval služby, které se v průběhu času mění. Obecně platí, že přidání do služeb a metod gRPC je nenáročné. Neprůlomové změny umožňují stávajícím klientům pokračovat v práci bez změn. Změna nebo odstranění služeb gRPC Services se mění. Pokud gRPC služby přerušují změny, musí se klienti, kteří tuto službu používají, aktualizovat a znovu nasadit.

Provádění nerozhodujících změn služby má několik výhod:

* Stávající klienti pokračují v běhu.
* Zabrání práci, která je součástí upozorňování klientů na zásadní změny, a jejich aktualizaci.
* Musí být dokumentována a udržována pouze jedna verze služby.

### <a name="non-breaking-changes"></a>Neprůlomové změny

Tyto změny jsou nerozdělitelné na úrovni protokolu gRPC a binární úrovni .NET.

* **Přidává se nová služba.**
* **Přidání nové metody do služby**
* **Přidání pole do zprávy žádosti** – pole přidaná do zprávy požadavku jsou deserializována s [výchozí hodnotou](https://developers.google.com/protocol-buffers/docs/proto3#default) na serveru, pokud není nastavena. Aby byla nekoncová změna, služba musí být úspěšná, když je nové pole nenastavené staršími klienty.
* **Přidání pole do zprávy odpovědi** – pole přidaná do zprávy odpovědi jsou deserializována do kolekce [neznámá pole](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) zprávy na klientovi.
* **Přidání hodnoty do** výčtového výčtového typu je serializováno jako číselná hodnota. Nové hodnoty výčtu jsou v klientovi deserializovatelné na hodnotu výčtu bez názvu výčtu. Aby byla nekoncová změna, starší klienti musí při příjmu nové hodnoty výčtu běžet správně.

### <a name="binary-breaking-changes"></a>Binární přerušující změny

Následující změny nejsou rozstupné na úrovni protokolu gRPC, ale pokud se upgradují na nejnovější verzi, je potřeba, aby byl klient aktualizovaný *. proto* se jedná o kontrakt nebo klientské sestavení .NET. Binární kompatibilita je důležitá, pokud plánujete publikovat knihovnu gRPC do NuGet.

* **Odebrání hodnot pole** z odebraného pole je deserializovatelné na [neznámá pole](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)zprávy. Nejedná se o změnu gRPC protokolu, ale pokud se upgraduje na nejnovější verzi, je potřeba aktualizovat klienta. Je důležité, aby odebrané číslo pole v budoucnu nechtěně znovu nepoužívalo. Chcete-li zajistit, aby k tomu nedocházelo, zadejte ve zprávě pomocí [rezervovaného](https://developers.google.com/protocol-buffers/docs/proto3#reserved) klíčového slova Protobuf Odstraněná čísla a názvy polí.
* **Přejmenování** zpráv – názvy zpráv se obvykle neodesílají v síti, takže to není gRPCá Změna protokolu. Klient bude muset být aktualizován, pokud se upgraduje na nejnovější kontrakt. Jedna situace, kdy se **názvy zpráv** odesílají v síti, jsou v [jakémkoli](https://developers.google.com/protocol-buffers/docs/proto3#any) poli, když se k identifikaci typu zprávy použije název zprávy.
* **Změna csharp_namespace** změny změní `csharp_namespace` obor názvů vygenerovaných typů .NET. Nejedná se o změnu gRPC protokolu, ale pokud se upgraduje na nejnovější verzi, je potřeba aktualizovat klienta.

### <a name="protocol-breaking-changes"></a>Přerušující se změny protokolu

Následující položky jsou protokoly a binární změny:

* **Přejmenování pole** – pomocí Protobuf obsahu se názvy polí používají pouze ve vygenerovaném kódu. Číslo pole se používá k identifikaci polí v síti. Přejmenování pole není Protobuf Změna protokolu pro. Pokud ale server používá obsah JSON, přejmenovávání pole je zásadní změna.
* **Změna datového typu pole** – Změna datového typu pole na [nekompatibilní typ](https://developers.google.com/protocol-buffers/docs/proto3#updating) způsobí chyby při deserializaci zprávy. I v případě, že je nový datový typ kompatibilní, je pravděpodobně nutné aktualizovat klienta, aby podporoval nový typ, pokud se upgraduje na nejnovější kontrakt.
* **Změna čísla pole** – pomocí datových částí Protobuf se číslo pole používá k identifikaci polí v síti.
* **Přejmenování balíčku, služby nebo metody** – gRPC používá název balíčku, název služby a název metody k sestavení adresy URL. Klient získá *Neimplementovaný* stav ze serveru.
* **Odebrání služby nebo metody** – klient získá *Neimplementovaný* stav ze serveru při volání odebrané metody.

### <a name="behavior-breaking-changes"></a>Chování při ukončování změn

Při provádění nerozhodujících změn musíte taky zvážit, jestli můžou starší klienti pokračovat v práci s novým chováním služby. Například přidání nového pole do zprávy žádosti:

* Nejedná se o změnu v důsledku přerušení protokolu.
* Vrátí stav chyby na serveru, pokud není nové pole nastavené, což pro staré klienty způsobuje zásadní změnu.

Kompatibilita chování je určena kódem specifickým pro danou aplikaci.

## <a name="version-number-services"></a>Číslo verze služeb

Služby by se měly snažit, aby zůstaly zpětně kompatibilní se starými klienty. Nakonec změny vaší aplikace mohou vyžadovat zásadní změny. Poškození starých klientů a jejich vynucování spolu s vaší službou není dobrým uživatelským prostředím. Způsob, jak udržet zpětnou kompatibilitu při provádění zásadních změn, je publikování více verzí služby.

gRPC podporuje volitelný specifikátor [balíčku](https://developers.google.com/protocol-buffers/docs/proto3#packages) , který funguje podobně jako obor názvů .NET. Ve skutečnosti `package` bude použit jako obor názvů .NET pro vygenerované typy .NET, pokud `option csharp_namespace` není nastaven v souboru *..* . Balíček lze použít k zadání čísla verze vaší služby a jejích zpráv:

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

* Nepoužívejte závažné změny, pokud je to rozumné.
* Neaktualizujte číslo verze, pokud neprovádíte zásadní změny.
* Aktualizujte číslo verze, když provedete průlomové změny.

Publikování více verzí služby je duplicitní. Pokud chcete omezit duplicity, zvažte přesunutí obchodní logiky ze implementací služby do centralizovaného umístění, které je možné použít starými a novými implementacemi:

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

Služby a zprávy vygenerované s různými názvy balíčků jsou **různé typy rozhraní .NET**. Přesun obchodní logiky do centralizovaného umístění vyžaduje mapování zpráv na společné typy.
