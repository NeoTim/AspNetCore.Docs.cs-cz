---
title: SignalR Požadavky na návrh rozhraní API
author: anurse
description: Naučte se navrhovat SignalR rozhraní API pro kompatibilitu mezi verzemi vaší aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/api-design
ms.openlocfilehash: 4a838c3a051476bd3d281e133d08b643656ae3b7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632899"
---
# <a name="no-locsignalr-api-design-considerations"></a><span data-ttu-id="6d431-103">SignalR Požadavky na návrh rozhraní API</span><span class="sxs-lookup"><span data-stu-id="6d431-103">SignalR API design considerations</span></span>

<span data-ttu-id="6d431-104">Autor [: Andrew Stanton – zdravotní sestry](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="6d431-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="6d431-105">Tento článek poskytuje pokyny pro vytváření SignalR rozhraní API založených na sestaveních.</span><span class="sxs-lookup"><span data-stu-id="6d431-105">This article provides guidance for building SignalR-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="6d431-106">Použití vlastních parametrů objektů k zajištění zpětné kompatibility</span><span class="sxs-lookup"><span data-stu-id="6d431-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="6d431-107">Přidání parametrů do SignalR metody hub (na klientovi nebo na serveru) je zásadní *Změna*.</span><span class="sxs-lookup"><span data-stu-id="6d431-107">Adding parameters to a SignalR hub method (on either the client or the server) is a *breaking change*.</span></span> <span data-ttu-id="6d431-108">To znamená, že starší klienti nebo servery budou mít chyby, když se pokusí vyvolat metodu bez příslušného počtu parametrů.</span><span class="sxs-lookup"><span data-stu-id="6d431-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="6d431-109">Přidání vlastností do vlastního parametru objektu však **nepředstavuje zásadní** změnu.</span><span class="sxs-lookup"><span data-stu-id="6d431-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="6d431-110">Dá se použít k návrhu kompatibilních rozhraní API, která jsou odolná proti změnám v klientovi nebo na serveru.</span><span class="sxs-lookup"><span data-stu-id="6d431-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="6d431-111">Představte si třeba rozhraní API na straně serveru, například následující:</span><span class="sxs-lookup"><span data-stu-id="6d431-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="6d431-112">Klient jazyka JavaScript volá tuto metodu pomocí `invoke` následujícího postupu:</span><span class="sxs-lookup"><span data-stu-id="6d431-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="6d431-113">Pokud později přidáte druhý parametr do metody serveru, starší klienti tuto hodnotu parametru neposkytují.</span><span class="sxs-lookup"><span data-stu-id="6d431-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="6d431-114">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6d431-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="6d431-115">Když se starý klient pokusí tuto metodu vyvolat, zobrazí se chyba takto:</span><span class="sxs-lookup"><span data-stu-id="6d431-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="6d431-116">Na serveru se zobrazí zpráva s protokolem, například:</span><span class="sxs-lookup"><span data-stu-id="6d431-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="6d431-117">Starý Klient poslal pouze jeden parametr, ale novější serverové rozhraní API vyžadovalo dva parametry.</span><span class="sxs-lookup"><span data-stu-id="6d431-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="6d431-118">Použití vlastních objektů jako parametrů poskytuje větší flexibilitu.</span><span class="sxs-lookup"><span data-stu-id="6d431-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="6d431-119">Pojďme znovu navrhnout původní rozhraní API, aby se používal vlastní objekt:</span><span class="sxs-lookup"><span data-stu-id="6d431-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="6d431-120">Nyní klient používá objekt pro volání metody:</span><span class="sxs-lookup"><span data-stu-id="6d431-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="6d431-121">Místo přidání parametru přidejte do `TotalLengthRequest` objektu vlastnost:</span><span class="sxs-lookup"><span data-stu-id="6d431-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="6d431-122">Když původní klient pošle jeden parametr, zůstane vlastnost extra `Param2` prázdná `null` .</span><span class="sxs-lookup"><span data-stu-id="6d431-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="6d431-123">Můžete zjistit zprávu odeslanou starším klientem zaškrtnutím `Param2` u `null` a použijte výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6d431-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="6d431-124">Nový klient může odeslat oba parametry.</span><span class="sxs-lookup"><span data-stu-id="6d431-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="6d431-125">Stejný postup funguje pro metody definované v klientovi.</span><span class="sxs-lookup"><span data-stu-id="6d431-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="6d431-126">Můžete odeslat vlastní objekt ze strany serveru:</span><span class="sxs-lookup"><span data-stu-id="6d431-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="6d431-127">Na straně klienta získáte přístup k `Message` vlastnosti místo použití parametru:</span><span class="sxs-lookup"><span data-stu-id="6d431-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="6d431-128">Pokud se později rozhodnete přidat odesílatele zprávy do datové části, přidejte do objektu vlastnost:</span><span class="sxs-lookup"><span data-stu-id="6d431-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="6d431-129">Starší klienti neočekávají `Sender` hodnotu, takže ji budou ignorovat.</span><span class="sxs-lookup"><span data-stu-id="6d431-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="6d431-130">Nový klient ho může přijmout tak, že aktualizuje a přečte novou vlastnost:</span><span class="sxs-lookup"><span data-stu-id="6d431-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="6d431-131">V tomto případě je nový klient také odolný proti starému serveru, který tuto hodnotu neposkytuje `Sender` .</span><span class="sxs-lookup"><span data-stu-id="6d431-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="6d431-132">Vzhledem k tomu, že původní server tuto hodnotu neposkytne `Sender` , klient zkontroluje, jestli existuje, než k němu přistoupí.</span><span class="sxs-lookup"><span data-stu-id="6d431-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
