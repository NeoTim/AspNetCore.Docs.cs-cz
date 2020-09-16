---
title: Spolehlivé služby gRPC s konečnými termíny a zrušením
author: jamesnk
description: Naučte se vytvářet spolehlivé služby gRPC pomocí konečných termínů a zrušení v .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: 59b737a032ea37a554ad5ddd0f4d44e4e1602d88
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594425"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a>Spolehlivé služby gRPC s konečnými termíny a zrušením

Od [James Newton – král](https://twitter.com/jamesnk)

Termíny a zrušení jsou funkce používané klienty gRPC k přerušení probíhajících volání. Tento článek popisuje, proč jsou termíny a zrušení důležité a jak je používat v aplikacích .NET gRPC.

## <a name="deadlines"></a>Dodržování

Konečný termín umožňuje klientovi gRPC určit, jak dlouho bude čekat na dokončení volání. Pokud je překročen konečný termín, volání je zrušeno. Nastavení konečného termínu je důležité, protože poskytuje horní limit toho, jak dlouho může volání běžet. Zastaví se tak nesprávnému chování služeb, které neběží trvale a vyčerpání prostředků serveru. Termíny jsou užitečným nástrojem pro vytváření spolehlivých aplikací a měly by být nakonfigurované.

Konfigurace konečného termínu:

* Konečný termín je nakonfigurován pomocí `CallOptions.Deadline` , když je provedeno volání.
* Neexistuje žádná výchozí hodnota termínu. volání gRPC nejsou časově omezená, pokud není zadaný konečný termín.
* Konečný termín je čas UTC, kdy je překročen konečný termín. Například `DateTime.UtcNow.AddSeconds(5)` je konečný termín 5 sekund od této chvíle.
* Pokud se použije minulý nebo aktuální čas, volání okamžitě přesáhne konečný termín.
* Konečný termín je odeslán s voláním gRPC do služby a je nezávisle sledován klientem i službou. Je možné, že volání gRPC se dokončí na jednom počítači, ale až do doby, kdy byla odpověď vrácena klientovi, byl překročen konečný termín.

Pokud je překročen konečný termín, má klient a služba jiné chování:

* Klient okamžitě přeruší příslušný požadavek HTTP a vyvolá `DeadlineExceeded` chybu. Klientská aplikace se může rozhodnout zachytit chybu a zobrazit uživateli zprávu s časovým limitem.
* Na serveru je spuštěn požadavek HTTP přerušen a je vyvolána [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) . I když je požadavek HTTP přerušený, volání gRPC i nadále běží na serveru, dokud se metoda nedokončí. Je důležité, aby byl token zrušení předán asynchronním metodám, aby byly zrušeny spolu s voláním. Například předání tokenu zrušení do asynchronních databázových dotazů a požadavků HTTP. Předání tokenu zrušení umožňuje, aby zrušené volání bylo rychle dokončeno na serveru a uvolnilo prostředky pro jiné hovory.

Nakonfigurujte `CallOptions.Deadline` , aby se nastavil konečný termín pro gRPC volání:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

Použití `ServerCallContext.CancellationToken` ve službě gRPC:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a>Rozšiřování konečných termínů

Při volání gRPC z vykonávající služby gRPC by se měl konečný termín rozšířit. Příklad:

1. Klientská aplikace `FrontendService.GetUser` je volána s konečným termínem.
2. `FrontendService` volání `UserService.GetUser` . Konečný termín určený klientem by měl být zadaný pomocí nového volání gRPC.
3. `UserService.GetUser` přijme konečný termín. Je-li překročen konečný termín klientské aplikace, správně vyprší časový limit.

Kontext volání poskytuje konečný termín s `ServerCallContext.Deadline` :

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

Ruční rozšiřování konečných termínů může mít nenáročný. Konečný termín musí být předán každé volání a je snadno neúmyslně nechtěně neúspěšný. Pro objekt pro vytváření klientů gRPC je k dispozici automatické řešení. Zadání `EnableCallContextPropagation` :

* Automaticky rozšíří konečný termín a token zrušení na podřízená volání.
* Je vynikající způsob, jak zajistit, aby komplexní a vnořené gRPC scénáře vždy rozšířily konečný termín a zrušení.

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

Další informace naleznete v tématu <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.

## <a name="cancellation"></a>Zrušení

Zrušení umožňuje klientovi gRPC zrušit dlouho běžící volání, která už nepotřebujete. Například volání gRPC, které streamuje aktualizace v reálném čase, se spustí, když uživatel navštíví stránku na webu. Datový proud by měl být zrušen, pokud uživatel přejde ze stránky jinam.

Volání gRPC lze v klientovi zrušit předáním tokenu zrušení pomocí [CallOptions. CancellationToken](xref:System.Threading.CancellationToken) nebo voláním `Dispose` volání.

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

gRPC služby, které je možné zrušit, by měly:
* Předejte `ServerCallContext.CancellationToken` do asynchronních metod. Zrušení asynchronních metod umožňuje rychlé dokončení volání na serveru.
* Rozšiřte token zrušení na podřízená volání. Rozšiřování tokenu zrušení zajišťuje, aby se podřízená volání zrušila s jejich nadřazeným prvkem. [gRPC klientskou továrnu](xref:grpc/clientfactory) a `EnableCallContextPropagation()` automaticky šíří token zrušení.

## <a name="additional-resources"></a>Další materiály

* <xref:grpc/client>
* <xref:grpc/clientfactory>
