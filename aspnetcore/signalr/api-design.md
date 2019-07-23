---
title: Aspekty návrhu rozhraní API SignalR
author: anurse
description: Informace o návrhu rozhraní API funkce SignalR pro kompatibilitu mezi verzemi aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/api-design
ms.openlocfilehash: 3f17bf055b793e8fc91fbcc15f668928ca261f77
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64903180"
---
# <a name="signalr-api-design-considerations"></a>Aspekty návrhu rozhraní API SignalR

Podle [Andrew Stanton sestry](https://twitter.com/anurse)

Tento článek obsahuje pokyny pro vytváření rozhraní API založená na systému SignalR.

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>Použít vlastní objekt parametry k zajištění zpětné kompatibility

Přidání parametrů do metody rozbočovače SignalR (u klienta nebo serveru) je *narušující změna*. To znamená, že starší klienti a servery, bude docházet k chybám při pokusu o volání metody bez odpovídající počet parametrů. Přidání vlastnosti do vlastní objekt parametru ale **není** zásadní změnu. To umožňuje návrh kompatibilních rozhraní API, které jsou odolné vůči změnám v klientovi nebo serveru.

Představte si třeba API na straně serveru nějak takto:

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

JavaScript klienta volá tuto metodu pomocí `invoke` následujícím způsobem:

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

Pokud chcete později přidat druhý parametr metody serveru, starší klienti neposkytne hodnotu tohoto parametru. Příklad:

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

Když se původní klient se pokusí vyvolat tuto metodu, dojde k chybě takto:

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

Na serveru zobrazí se vám podobná zpráva protokolu:

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

Starého klienta odešlou, jenom jeden parametr, ale novější rozhraní API serveru vyžaduje dva parametry. Pomocí vlastních objektů jako parametry vám zajistí větší flexibilitu. Umožňuje změnit návrh původní rozhraní API pro použití vlastního objektu:

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

Nyní klient použije objekt k volání metody:

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

Nepřidávat parametr přidat vlastnost, která má `TotalLengthRequest` objektu:

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

Pokud původní klient odešle jediný parametr, nadbytečné `Param2` zůstane vlastnost `null`. Zpráva odeslaná staršího klienta tak, že zkontrolujete, můžete zjistit `Param2` pro `null` a použijte výchozí hodnotu. Nový klient může odesílat oba parametry.

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

Stejný postup funguje pro metody definované na straně klienta. Vlastní objekt můžete odeslat na straně serveru:

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

Na straně klienta přistupujete `Message` vlastnost spíše než pomocí parametru:

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

Pokud se později rozhodnete přidat odesílatele zprávy do datové části, přidání vlastnosti do objektu:

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

Starší klienti nebudou očekává `Sender` hodnotu, takže se bude ignorovat. Nový klient může přijmout tak, že aktualizace zobrazíte nové vlastnosti:

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

V tomto případě je nový klient také odolný vůči chybám starého serveru, který neposkytuje `Sender` hodnotu. Protože starý server nebude poskytovat `Sender` hodnotu, klient zkontroluje, jestli existuje před jeho použitím.
