---
title: SignalRPožadavky na návrh rozhraní API
author: anurse
description: Naučte se navrhovat SignalR rozhraní API pro kompatibilitu mezi verzemi vaší aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/api-design
ms.openlocfilehash: 9ad8d30da552d3d3084534b8c7ca57386ad111ac
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407795"
---
# <a name="signalr-api-design-considerations"></a>SignalRPožadavky na návrh rozhraní API

Autor [: Andrew Stanton – zdravotní sestry](https://twitter.com/anurse)

Tento článek poskytuje pokyny pro vytváření SignalR rozhraní API založených na sestaveních.

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>Použití vlastních parametrů objektů k zajištění zpětné kompatibility

Přidání parametrů do SignalR metody hub (na klientovi nebo na serveru) je zásadní *Změna*. To znamená, že starší klienti nebo servery budou mít chyby, když se pokusí vyvolat metodu bez příslušného počtu parametrů. Přidání vlastností do vlastního parametru objektu však **nepředstavuje zásadní** změnu. Dá se použít k návrhu kompatibilních rozhraní API, která jsou odolná proti změnám v klientovi nebo na serveru.

Představte si třeba rozhraní API na straně serveru, například následující:

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

Klient jazyka JavaScript volá tuto metodu pomocí `invoke` následujícího postupu:

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

Pokud později přidáte druhý parametr do metody serveru, starší klienti tuto hodnotu parametru neposkytují. Například:

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

Když se starý klient pokusí tuto metodu vyvolat, zobrazí se chyba takto:

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

Na serveru se zobrazí zpráva s protokolem, například:

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

Starý Klient poslal pouze jeden parametr, ale novější serverové rozhraní API vyžadovalo dva parametry. Použití vlastních objektů jako parametrů poskytuje větší flexibilitu. Pojďme znovu navrhnout původní rozhraní API, aby se používal vlastní objekt:

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

Nyní klient používá objekt pro volání metody:

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

Místo přidání parametru přidejte do `TotalLengthRequest` objektu vlastnost:

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

Když původní klient pošle jeden parametr, zůstane vlastnost extra `Param2` prázdná `null` . Můžete zjistit zprávu odeslanou starším klientem zaškrtnutím `Param2` u `null` a použijte výchozí hodnotu. Nový klient může odeslat oba parametry.

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

Stejný postup funguje pro metody definované v klientovi. Můžete odeslat vlastní objekt ze strany serveru:

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

Na straně klienta získáte přístup k `Message` vlastnosti místo použití parametru:

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

Pokud se později rozhodnete přidat odesílatele zprávy do datové části, přidejte do objektu vlastnost:

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

Starší klienti neočekávají `Sender` hodnotu, takže ji budou ignorovat. Nový klient ho může přijmout tak, že aktualizuje a přečte novou vlastnost:

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

V tomto případě je nový klient také odolný proti starému serveru, který tuto hodnotu neposkytuje `Sender` . Vzhledem k tomu, že původní server tuto hodnotu neposkytne `Sender` , klient zkontroluje, jestli existuje, než k němu přistoupí.
