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
ms.sourcegitcommit: 408921a932448f66cb46fd53c307a864f5323fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571593"
---
# <a name="signalr-api-design-considerations"></a><span data-ttu-id="f1862-103">Aspekty návrhu rozhraní API SignalR</span><span class="sxs-lookup"><span data-stu-id="f1862-103">SignalR API design considerations</span></span>

<span data-ttu-id="f1862-104">Podle [Andrew Stanton sestry](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="f1862-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="f1862-105">Tento článek obsahuje pokyny pro vytváření rozhraní API založená na systému SignalR.</span><span class="sxs-lookup"><span data-stu-id="f1862-105">This article provides guidance for building SignalR-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="f1862-106">Použít vlastní objekt parametry k zajištění zpětné kompatibility</span><span class="sxs-lookup"><span data-stu-id="f1862-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="f1862-107">Přidání parametrů do metody rozbočovače SignalR (u klienta nebo serveru) je *narušující změna*.</span><span class="sxs-lookup"><span data-stu-id="f1862-107">Adding parameters to a SignalR hub method (on either the client or the server) is a *breaking change*.</span></span> <span data-ttu-id="f1862-108">To znamená, že starší klienti a servery, bude docházet k chybám při pokusu o volání metody bez odpovídající počet parametrů.</span><span class="sxs-lookup"><span data-stu-id="f1862-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="f1862-109">Přidání vlastnosti do vlastní objekt parametru ale **není** zásadní změnu.</span><span class="sxs-lookup"><span data-stu-id="f1862-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="f1862-110">To umožňuje návrh kompatibilních rozhraní API, které jsou odolné vůči změnám v klientovi nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="f1862-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="f1862-111">Představte si třeba API na straně serveru nějak takto:</span><span class="sxs-lookup"><span data-stu-id="f1862-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="f1862-112">JavaScript klienta volá tuto metodu pomocí `invoke` následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="f1862-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="f1862-113">Pokud chcete později přidat druhý parametr metody serveru, starší klienti neposkytne hodnotu tohoto parametru.</span><span class="sxs-lookup"><span data-stu-id="f1862-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="f1862-114">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f1862-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="f1862-115">Když se původní klient se pokusí vyvolat tuto metodu, dojde k chybě takto:</span><span class="sxs-lookup"><span data-stu-id="f1862-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="f1862-116">Na serveru zobrazí se vám podobná zpráva protokolu:</span><span class="sxs-lookup"><span data-stu-id="f1862-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="f1862-117">Starého klienta odešlou, jenom jeden parametr, ale novější rozhraní API serveru vyžaduje dva parametry.</span><span class="sxs-lookup"><span data-stu-id="f1862-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="f1862-118">Pomocí vlastních objektů jako parametry vám zajistí větší flexibilitu.</span><span class="sxs-lookup"><span data-stu-id="f1862-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="f1862-119">Umožňuje změnit návrh původní rozhraní API pro použití vlastního objektu:</span><span class="sxs-lookup"><span data-stu-id="f1862-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="f1862-120">Nyní klient použije objekt k volání metody:</span><span class="sxs-lookup"><span data-stu-id="f1862-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="f1862-121">Nepřidávat parametr přidat vlastnost, která má `TotalLengthRequest` objektu:</span><span class="sxs-lookup"><span data-stu-id="f1862-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="f1862-122">Pokud původní klient odešle jediný parametr, nadbytečné `Param2` zůstane vlastnost `null`.</span><span class="sxs-lookup"><span data-stu-id="f1862-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="f1862-123">Zpráva odeslaná staršího klienta tak, že zkontrolujete, můžete zjistit `Param2` pro `null` a použijte výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f1862-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="f1862-124">Nový klient může odesílat oba parametry.</span><span class="sxs-lookup"><span data-stu-id="f1862-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="f1862-125">Stejný postup funguje pro metody definované na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f1862-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="f1862-126">Vlastní objekt můžete odeslat na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="f1862-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="f1862-127">Na straně klienta přistupujete `Message` vlastnost spíše než pomocí parametru:</span><span class="sxs-lookup"><span data-stu-id="f1862-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="f1862-128">Pokud se později rozhodnete přidat odesílatele zprávy do datové části, přidání vlastnosti do objektu:</span><span class="sxs-lookup"><span data-stu-id="f1862-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="f1862-129">Starší klienti nebudou očekává `Sender` hodnotu, takže se bude ignorovat.</span><span class="sxs-lookup"><span data-stu-id="f1862-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="f1862-130">Nový klient může přijmout tak, že aktualizace zobrazíte nové vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="f1862-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="f1862-131">V tomto případě je nový klient také odolný vůči chybám starého serveru, který neposkytuje `Sender` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f1862-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="f1862-132">Protože starý server nebude poskytovat `Sender` hodnotu, klient zkontroluje, jestli existuje před jeho použitím.</span><span class="sxs-lookup"><span data-stu-id="f1862-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
