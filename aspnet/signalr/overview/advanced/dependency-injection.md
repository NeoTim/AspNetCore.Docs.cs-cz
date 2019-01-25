---
uid: signalr/overview/advanced/dependency-injection
title: Injektáž závislostí v knihovně SignalR | Dokumentace Microsoftu
author: bradygaster
description: Verze softwaru použitým v tomto tématu Visual Studio 2013 .NET 4.5 SignalR 2 předchozí verze tohoto tématu informace o předchozích verzích...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: a14121ae-02cf-4024-8af0-9dd0dc810690
msc.legacyurl: /signalr/overview/advanced/dependency-injection
msc.type: authoredcontent
ms.openlocfilehash: 54e263e277852d2d478ce5bccd4164254498831a
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836607"
---
<a name="dependency-injection-in-signalr"></a><span data-ttu-id="9d282-103">Injektáž závislostí v centrech SignalR</span><span class="sxs-lookup"><span data-stu-id="9d282-103">Dependency Injection in SignalR</span></span>
====================
<span data-ttu-id="9d282-104">podle [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="9d282-104">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="9d282-105">Verze softwaru použitým v tomto tématu</span><span class="sxs-lookup"><span data-stu-id="9d282-105">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="9d282-106">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="9d282-106">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="9d282-107">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="9d282-107">.NET 4.5</span></span>
> - <span data-ttu-id="9d282-108">Funkce SignalR verze 2</span><span class="sxs-lookup"><span data-stu-id="9d282-108">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="9d282-109">Předchozích verzích tohoto tématu</span><span class="sxs-lookup"><span data-stu-id="9d282-109">Previous versions of this topic</span></span>
>
> <span data-ttu-id="9d282-110">Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="9d282-110">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="9d282-111">Otázky a komentáře</span><span class="sxs-lookup"><span data-stu-id="9d282-111">Questions and comments</span></span>
>
> <span data-ttu-id="9d282-112">Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="9d282-112">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="9d282-113">Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="9d282-113">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="9d282-114">Injektáž závislostí je způsob, jak odebrat pevně zakódované závislosti mezi objekty usnadnit k nahrazení objektu závislosti, buď pro testování (pomocí mock objektů), nebo chcete změnit chování za běhu.</span><span class="sxs-lookup"><span data-stu-id="9d282-114">Dependency injection is a way to remove hard-coded dependencies between objects, making it easier to replace an object's dependencies, either for testing (using mock objects) or to change run-time behavior.</span></span> <span data-ttu-id="9d282-115">Tento kurz ukazuje, jak provádět injektáž závislostí v centrech SignalR.</span><span class="sxs-lookup"><span data-stu-id="9d282-115">This tutorial shows how to perform dependency injection on SignalR hubs.</span></span> <span data-ttu-id="9d282-116">Také ukazuje, jak používat technologie IoC kontejnery s knihovnou SignalR.</span><span class="sxs-lookup"><span data-stu-id="9d282-116">It also shows how to use IoC containers with SignalR.</span></span> <span data-ttu-id="9d282-117">Kontejner IoC je obecné rozhraní pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="9d282-117">An IoC container is a general framework for dependency injection.</span></span>

## <a name="what-is-dependency-injection"></a><span data-ttu-id="9d282-118">Co je injektáž závislostí?</span><span class="sxs-lookup"><span data-stu-id="9d282-118">What is Dependency Injection?</span></span>

<span data-ttu-id="9d282-119">Tuto část přeskočte, pokud jste už obeznámení s vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="9d282-119">Skip this section if you are already familiar with dependency injection.</span></span>

<span data-ttu-id="9d282-120">*Injektáž závislostí* (DI) je vzor, ve kterém jsou objekty není zodpovědný za vytváření vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="9d282-120">*Dependency injection* (DI) is a pattern where objects are not responsible for creating their own dependencies.</span></span> <span data-ttu-id="9d282-121">Tady je jednoduchý příklad ke DI.</span><span class="sxs-lookup"><span data-stu-id="9d282-121">Here is a simple example to motivate DI.</span></span> <span data-ttu-id="9d282-122">Předpokládejme, že budete mít objekt, který je potřeba protokolování zpráv.</span><span class="sxs-lookup"><span data-stu-id="9d282-122">Suppose you have an object that needs to log messages.</span></span> <span data-ttu-id="9d282-123">Můžete třeba definovat rozhraní protokolování:</span><span class="sxs-lookup"><span data-stu-id="9d282-123">You might define a logging interface:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample1.cs)]

<span data-ttu-id="9d282-124">V objektu, můžete vytvořit `ILogger` protokolování zpráv:</span><span class="sxs-lookup"><span data-stu-id="9d282-124">In your object, you can create an `ILogger` to log messages:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample2.cs)]

<span data-ttu-id="9d282-125">Tento postup funguje, ale není optimální.</span><span class="sxs-lookup"><span data-stu-id="9d282-125">This works, but it's not the best design.</span></span> <span data-ttu-id="9d282-126">Pokud budete chtít nahradit `FileLogger` sebou `ILogger` implementace, budete muset upravit `SomeComponent`.</span><span class="sxs-lookup"><span data-stu-id="9d282-126">If you want to replace `FileLogger` with another `ILogger` implementation, you will have to modify `SomeComponent`.</span></span> <span data-ttu-id="9d282-127">Domnívat, že mnoho dalších objektů použijte `FileLogger`, budete muset změnit všechny z nich.</span><span class="sxs-lookup"><span data-stu-id="9d282-127">Supposing that a lot of other objects use `FileLogger`, you will need to change all of them.</span></span> <span data-ttu-id="9d282-128">Nebo pokud se rozhodnete provést `FileLogger` typu singleton, budete také muset provést změny v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9d282-128">Or if you decide to make `FileLogger` a singleton, you'll also need to make changes throughout the application.</span></span>

<span data-ttu-id="9d282-129">Lepším řešením je "Vložit" `ILogger` do objektu, například pomocí argumentu konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="9d282-129">A better approach is to "inject" an `ILogger` into the object—for example, by using a constructor argument:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample3.cs)]

<span data-ttu-id="9d282-130">Nyní na objekt neodpovídá pro výběr, který `ILogger` používat.</span><span class="sxs-lookup"><span data-stu-id="9d282-130">Now the object is not responsible for selecting which `ILogger` to use.</span></span> <span data-ttu-id="9d282-131">Je možné swich `ILogger` implementace beze změny, které jsou na ní závislé objekty.</span><span class="sxs-lookup"><span data-stu-id="9d282-131">You can swich `ILogger` implementations without changing the objects that depend on it.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample4.cs)]

<span data-ttu-id="9d282-132">Tento model se nazývá [konstruktor vkládání](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection).</span><span class="sxs-lookup"><span data-stu-id="9d282-132">This pattern is called [constructor injection](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection).</span></span> <span data-ttu-id="9d282-133">Jiné vzor je vkládání setter, kde nastavujete závislost prostřednictvím metody setter nebo vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="9d282-133">Another pattern is setter injection, where you set the dependency through a setter method or property.</span></span>

## <a name="simple-dependency-injection-in-signalr"></a><span data-ttu-id="9d282-134">Injektáž závislostí jednoduché v knihovně SignalR</span><span class="sxs-lookup"><span data-stu-id="9d282-134">Simple Dependency Injection in SignalR</span></span>

<span data-ttu-id="9d282-135">Vezměte v úvahu chatovací aplikaci z kurzu [Začínáme s knihovnou SignalR](../getting-started/tutorial-getting-started-with-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="9d282-135">Consider the Chat application from the tutorial [Getting Started with SignalR](../getting-started/tutorial-getting-started-with-signalr.md).</span></span> <span data-ttu-id="9d282-136">Tady je třída rozbočovače z této aplikace:</span><span class="sxs-lookup"><span data-stu-id="9d282-136">Here is the hub class from that application:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample5.cs)]

<span data-ttu-id="9d282-137">Předpokládejme, že chcete ukládat zprávy chatu na serveru před jejich odesláním.</span><span class="sxs-lookup"><span data-stu-id="9d282-137">Suppose that you want to store chat messages on the server before sending them.</span></span> <span data-ttu-id="9d282-138">Může definovat rozhraní, který získává tuto funkci a použít DI vkládat rozhraní portálu `ChatHub` třídy.</span><span class="sxs-lookup"><span data-stu-id="9d282-138">You might define an interface that abstracts this functionality, and use DI to inject the interface into the `ChatHub` class.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample6.cs)]

<span data-ttu-id="9d282-139">Jediným problémem je, že aplikace SignalR přímo nevytváří hubs; SignalR je pro vás vytvoří.</span><span class="sxs-lookup"><span data-stu-id="9d282-139">The only problem is that a SignalR application does not directly create hubs; SignalR creates them for you.</span></span> <span data-ttu-id="9d282-140">Ve výchozím nastavení SignalR očekává, že třída rozbočovače mít konstruktor bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="9d282-140">By default, SignalR expects a hub class to have a parameterless constructor.</span></span> <span data-ttu-id="9d282-141">Však můžete snadno zaregistrovat funkci pro vytvoření instancí rozbočovače a tuto funkci použít k provedení DI.</span><span class="sxs-lookup"><span data-stu-id="9d282-141">However, you can easily register a function to create hub instances, and use this function to perform DI.</span></span> <span data-ttu-id="9d282-142">Registraci funkci voláním **GlobalHost.DependencyResolver.Register**.</span><span class="sxs-lookup"><span data-stu-id="9d282-142">Register the function by calling **GlobalHost.DependencyResolver.Register**.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample7.cs)]

<span data-ttu-id="9d282-143">Nyní SignalR vyvolá tuto anonymní funkci pokaždé, když je potřeba vytvořit `ChatHub` instance.</span><span class="sxs-lookup"><span data-stu-id="9d282-143">Now SignalR will invoke this anonymous function whenever it needs to create a `ChatHub` instance.</span></span>

## <a name="ioc-containers"></a><span data-ttu-id="9d282-144">Technologie IoC kontejnery</span><span class="sxs-lookup"><span data-stu-id="9d282-144">IoC Containers</span></span>

<span data-ttu-id="9d282-145">Předchozí kód je v pořádku pro jednoduché případy.</span><span class="sxs-lookup"><span data-stu-id="9d282-145">The previous code is fine for simple cases.</span></span> <span data-ttu-id="9d282-146">Ale máte stále zapisovat:</span><span class="sxs-lookup"><span data-stu-id="9d282-146">But you still had to write this:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample8.cs)]

<span data-ttu-id="9d282-147">V komplexní aplikace s velký počet závislostí potřebujete napsat velké množství tento kód "propojení".</span><span class="sxs-lookup"><span data-stu-id="9d282-147">In a complex application with many dependencies, you might need to write a lot of this "wiring" code.</span></span> <span data-ttu-id="9d282-148">Tento kód může být obtížné spravovat, zejména v případě, že jsou vnořené závislosti.</span><span class="sxs-lookup"><span data-stu-id="9d282-148">This code can be hard to maintain, especially if dependencies are nested.</span></span> <span data-ttu-id="9d282-149">Je taky obtížné testování částí.</span><span class="sxs-lookup"><span data-stu-id="9d282-149">It is also hard to unit test.</span></span>

<span data-ttu-id="9d282-150">Jedním řešením je použití kontejner IoC.</span><span class="sxs-lookup"><span data-stu-id="9d282-150">One solution is to use an IoC container.</span></span> <span data-ttu-id="9d282-151">Kontejner IoC je softwarová součást, která zodpovídá za správu závislostí. Registrace typů s kontejnerem a následné použití k vytvoření objektů kontejneru.</span><span class="sxs-lookup"><span data-stu-id="9d282-151">An IoC container is a software component that is responsible for managing dependencies.You register types with the container, and then use the container to create objects.</span></span> <span data-ttu-id="9d282-152">Kontejner automaticky zjistí vztahy závislostí.</span><span class="sxs-lookup"><span data-stu-id="9d282-152">The container automatically figures out the dependency relations.</span></span> <span data-ttu-id="9d282-153">Spousta kontejnerů IoC také umožňují řídit věci, jako je životní cyklus objektů a obor.</span><span class="sxs-lookup"><span data-stu-id="9d282-153">Many IoC containers also allow you to control things like object lifetime and scope.</span></span>

> [!NOTE]
> <span data-ttu-id="9d282-154">"IoC" znamená "inverzi ovládacího prvku", což je obecný vzor, pokud rámec volání do kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="9d282-154">"IoC" stands for "inversion of control", which is a general pattern where a framework calls into application code.</span></span> <span data-ttu-id="9d282-155">Kontejner IoC vytvoří objekty, které "Invertuje" obvyklý tok řízení.</span><span class="sxs-lookup"><span data-stu-id="9d282-155">An IoC container constructs your objects for you, which "inverts" the usual flow of control.</span></span>


## <a name="using-ioc-containers-in-signalr"></a><span data-ttu-id="9d282-156">Použití technologie IoC kontejnerů v knihovně SignalR</span><span class="sxs-lookup"><span data-stu-id="9d282-156">Using IoC Containers in SignalR</span></span>

<span data-ttu-id="9d282-157">Chatovací aplikaci je pravděpodobně příliš jednoduché, abyste využili výhod kontejner IoC.</span><span class="sxs-lookup"><span data-stu-id="9d282-157">The Chat application is probably too simple to benefit from an IoC container.</span></span> <span data-ttu-id="9d282-158">Místo toho, Podívejme se [StockTicker](http://nuget.org/packages/microsoft.aspnet.signalr.sample) vzorku.</span><span class="sxs-lookup"><span data-stu-id="9d282-158">Instead, let's look at the [StockTicker](http://nuget.org/packages/microsoft.aspnet.signalr.sample) sample.</span></span>

<span data-ttu-id="9d282-159">Ukázka StockTicker definuje dva hlavní třídy:</span><span class="sxs-lookup"><span data-stu-id="9d282-159">The StockTicker sample defines two main classes:</span></span>

- <span data-ttu-id="9d282-160">`StockTickerHub`: Třída rozbočovače, která spravuje připojení klientů.</span><span class="sxs-lookup"><span data-stu-id="9d282-160">`StockTickerHub`: The hub class, which manages client connections.</span></span>
- <span data-ttu-id="9d282-161">`StockTicker`: Jednotlivý prvek, který obsahuje ceny akcií a je pravidelně aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="9d282-161">`StockTicker`: A singleton that holds stock prices and periodically updates them.</span></span>

<span data-ttu-id="9d282-162">`StockTickerHub` obsahuje odkaz na `StockTicker` singleton, zatímco `StockTicker` obsahuje odkaz na **IHubConnectionContext** pro `StockTickerHub`.</span><span class="sxs-lookup"><span data-stu-id="9d282-162">`StockTickerHub` holds a reference to the `StockTicker` singleton, while `StockTicker` holds a reference to the **IHubConnectionContext** for the `StockTickerHub`.</span></span> <span data-ttu-id="9d282-163">Toto rozhraní se používá ke komunikaci s `StockTickerHub` instancí.</span><span class="sxs-lookup"><span data-stu-id="9d282-163">It uses this interface to communicate with `StockTickerHub` instances.</span></span> <span data-ttu-id="9d282-164">(Další informace najdete v tématu [serverové vysílání s knihovnou ASP.NET SignalR](../getting-started/tutorial-server-broadcast-with-signalr.md).)</span><span class="sxs-lookup"><span data-stu-id="9d282-164">(For more information, see [Server Broadcast with ASP.NET SignalR](../getting-started/tutorial-server-broadcast-with-signalr.md).)</span></span>

<span data-ttu-id="9d282-165">K trochu rozplétání těchto závislostí můžeme použít kontejner IoC.</span><span class="sxs-lookup"><span data-stu-id="9d282-165">We can use an IoC container to untangle these dependencies a bit.</span></span> <span data-ttu-id="9d282-166">Nejprve můžeme zjednodušit `StockTickerHub` a `StockTicker` třídy.</span><span class="sxs-lookup"><span data-stu-id="9d282-166">First, let's simplify the `StockTickerHub` and `StockTicker` classes.</span></span> <span data-ttu-id="9d282-167">V následujícím kódu můžu jste zakomentované části, že nebudeme potřebovat.</span><span class="sxs-lookup"><span data-stu-id="9d282-167">In the following code, I've commented out the parts that we don't need.</span></span>

<span data-ttu-id="9d282-168">Odebrat konstruktor bez parametrů z `StockTickerHub`.</span><span class="sxs-lookup"><span data-stu-id="9d282-168">Remove the parameterless constructor from `StockTickerHub`.</span></span> <span data-ttu-id="9d282-169">Místo toho vždy použije DI centrum vytvořit.</span><span class="sxs-lookup"><span data-stu-id="9d282-169">Instead, we will always use DI to create the hub.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample9.cs)]

<span data-ttu-id="9d282-170">StockTicker odeberte instanci typu singleton.</span><span class="sxs-lookup"><span data-stu-id="9d282-170">For StockTicker, remove the singleton instance.</span></span> <span data-ttu-id="9d282-171">Později řízení životnosti StockTicker použijeme kontejner IoC.</span><span class="sxs-lookup"><span data-stu-id="9d282-171">Later, we'll use the IoC container to control the StockTicker lifetime.</span></span> <span data-ttu-id="9d282-172">Navíc musí být konstruktor public.</span><span class="sxs-lookup"><span data-stu-id="9d282-172">Also, make the constructor public.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample10.cs?highlight=7)]

<span data-ttu-id="9d282-173">Dále jsme Refaktorovat kód tak, že vytvoříte rozhraní pro `StockTicker`.</span><span class="sxs-lookup"><span data-stu-id="9d282-173">Next, we can refactor the code by creating an interface for `StockTicker`.</span></span> <span data-ttu-id="9d282-174">Použijeme oddělit toto rozhraní `StockTickerHub` z `StockTicker` třídy.</span><span class="sxs-lookup"><span data-stu-id="9d282-174">We'll use this interface to decouple the `StockTickerHub` from the `StockTicker` class.</span></span>

<span data-ttu-id="9d282-175">Vytvoří malou Visual Studio tento druh refaktoringu snadné.</span><span class="sxs-lookup"><span data-stu-id="9d282-175">Visual Studio makes this kind of refactoring easy.</span></span> <span data-ttu-id="9d282-176">Otevřete soubor StockTicker.cs, klikněte pravým tlačítkem na `StockTicker` deklarace třídy a vyberte **Refaktorovat** ... **Extrahování rozhraní**.</span><span class="sxs-lookup"><span data-stu-id="9d282-176">Open the file StockTicker.cs, right-click on the `StockTicker` class declaration, and select **Refactor** ... **Extract Interface**.</span></span>

![](dependency-injection/_static/image1.png)

<span data-ttu-id="9d282-177">V **extrahování rozhraní** dialogového okna, klikněte na tlačítko **Vybrat vše**.</span><span class="sxs-lookup"><span data-stu-id="9d282-177">In the **Extract Interface** dialog, click **Select All**.</span></span> <span data-ttu-id="9d282-178">Nechte ostatní výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="9d282-178">Leave the other defaults.</span></span> <span data-ttu-id="9d282-179">Klikněte na **OK**.</span><span class="sxs-lookup"><span data-stu-id="9d282-179">Click **OK**.</span></span>

![](dependency-injection/_static/image2.png)

<span data-ttu-id="9d282-180">Visual Studio vytvoří nové rozhraní s názvem `IStockTicker`a taky změní `StockTicker` odvodit z `IStockTicker`.</span><span class="sxs-lookup"><span data-stu-id="9d282-180">Visual Studio creates a new interface named `IStockTicker`, and also changes `StockTicker` to derive from `IStockTicker`.</span></span>

<span data-ttu-id="9d282-181">Otevřete soubor IStockTicker.cs a změňte rozhraní **veřejné**.</span><span class="sxs-lookup"><span data-stu-id="9d282-181">Open the file IStockTicker.cs and change the interface to **public**.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample11.cs?highlight=1)]

<span data-ttu-id="9d282-182">V `StockTickerHub` třídy, dvě instance změnit `StockTicker` k `IStockTicker`:</span><span class="sxs-lookup"><span data-stu-id="9d282-182">In the `StockTickerHub` class, change the two instances of `StockTicker` to `IStockTicker`:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample12.cs?highlight=4,6)]

<span data-ttu-id="9d282-183">Vytvoření `IStockTicker` rozhraní není nezbytně nutné, ale já bych chtěl ukazují, jak DI vám může pomoct snížit párování mezi komponentami vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="9d282-183">Creating an `IStockTicker` interface isn't strictly necessary, but I wanted to show how DI can help to reduce coupling between components in your application.</span></span>

## <a name="add-the-ninject-library"></a><span data-ttu-id="9d282-184">Přidání knihovny Ninject</span><span class="sxs-lookup"><span data-stu-id="9d282-184">Add the Ninject Library</span></span>

<span data-ttu-id="9d282-185">Existuje spousta open source technologie IoC kontejnerů pro .NET.</span><span class="sxs-lookup"><span data-stu-id="9d282-185">There are many open-source IoC containers for .NET.</span></span> <span data-ttu-id="9d282-186">Pro účely tohoto kurzu použiju [Ninject](http://www.ninject.org/).</span><span class="sxs-lookup"><span data-stu-id="9d282-186">For this tutorial, I'll use [Ninject](http://www.ninject.org/).</span></span> <span data-ttu-id="9d282-187">(Zahrnout další oblíbené knihovny [Castle Windsor](http://www.castleproject.org/), [Spring.Net](http://www.springframework.net/), [Autofac](https://code.google.com/p/autofac/), [Unity](https://github.com/unitycontainer/unity), a [StructureMap ](http://docs.structuremap.net).)</span><span class="sxs-lookup"><span data-stu-id="9d282-187">(Other popular libraries include [Castle Windsor](http://www.castleproject.org/), [Spring.Net](http://www.springframework.net/), [Autofac](https://code.google.com/p/autofac/), [Unity](https://github.com/unitycontainer/unity), and [StructureMap](http://docs.structuremap.net).)</span></span>

<span data-ttu-id="9d282-188">Použití Správce balíčků NuGet k instalaci [Ninject knihovny](https://nuget.org/packages/Ninject/3.0.1.10).</span><span class="sxs-lookup"><span data-stu-id="9d282-188">Use NuGet Package Manager to install the [Ninject library](https://nuget.org/packages/Ninject/3.0.1.10).</span></span> <span data-ttu-id="9d282-189">V sadě Visual Studio z **nástroje** nabídky vyberte možnost **Správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="9d282-189">In Visual Studio, from the **Tools** menu select **NuGet Package Manager** > **Package Manager Console**.</span></span> <span data-ttu-id="9d282-190">V okně konzoly Správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="9d282-190">In the Package Manager Console window, enter the following command:</span></span>

[!code-powershell[Main](dependency-injection/samples/sample13.ps1)]

## <a name="replace-the-signalr-dependency-resolver"></a><span data-ttu-id="9d282-191">Nahraďte překladač závislostí SignalR</span><span class="sxs-lookup"><span data-stu-id="9d282-191">Replace the SignalR Dependency Resolver</span></span>

<span data-ttu-id="9d282-192">Pokud chcete použít Ninject v rámci SignalR, vytvořte třídu, která je odvozena z **DefaultDependencyResolver**.</span><span class="sxs-lookup"><span data-stu-id="9d282-192">To use Ninject within SignalR, create a class that derives from **DefaultDependencyResolver**.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample14.cs)]

<span data-ttu-id="9d282-193">Tato třída přepíše **GetService** a **GetServices** metody **DefaultDependencyResolver**.</span><span class="sxs-lookup"><span data-stu-id="9d282-193">This class overrides the **GetService** and **GetServices** methods of **DefaultDependencyResolver**.</span></span> <span data-ttu-id="9d282-194">Funkce SignalR volá tyto metody k vytvoření různých objektů za běhu, včetně instancí rozbočovače, jakož i různé služby používaná interně knihovnou SignalR.</span><span class="sxs-lookup"><span data-stu-id="9d282-194">SignalR calls these methods to create various objects at runtime, including hub instances, as well as various services used internally by SignalR.</span></span>

- <span data-ttu-id="9d282-195">**GetService** metoda vytvoří jednu instanci typu.</span><span class="sxs-lookup"><span data-stu-id="9d282-195">The **GetService** method creates a single instance of a type.</span></span> <span data-ttu-id="9d282-196">Potlačí tuto metodu volat Ninject jádra **TryGet** metody.</span><span class="sxs-lookup"><span data-stu-id="9d282-196">Override this method to call the Ninject kernel's **TryGet** method.</span></span> <span data-ttu-id="9d282-197">Pokud tato metoda vrátí hodnotu null, vrátit k výchozí překladač.</span><span class="sxs-lookup"><span data-stu-id="9d282-197">If that method returns null, fall back to the default resolver.</span></span>
- <span data-ttu-id="9d282-198">**GetServices** metoda vytvoří kolekci objektů zadaného typu.</span><span class="sxs-lookup"><span data-stu-id="9d282-198">The **GetServices** method creates a collection of objects of a specified type.</span></span> <span data-ttu-id="9d282-199">Potlačí tuto metodu ke zřetězení výsledky z Ninject s výsledky z výchozí překladač.</span><span class="sxs-lookup"><span data-stu-id="9d282-199">Override this method to concatenate the results from Ninject with the results from the default resolver.</span></span>

## <a name="configure-ninject-bindings"></a><span data-ttu-id="9d282-200">Nakonfigurujte Ninject vazby</span><span class="sxs-lookup"><span data-stu-id="9d282-200">Configure Ninject Bindings</span></span>

<span data-ttu-id="9d282-201">Nyní použijeme Ninject deklarovat typ vazby.</span><span class="sxs-lookup"><span data-stu-id="9d282-201">Now we'll use Ninject to declare type bindings.</span></span>

<span data-ttu-id="9d282-202">Otevřete třídu Startup.cs vaší aplikace (buď vytvořené ručně podle pokynů balíček `readme.txt`, nebo který byl vytvořen tak, že přidáte do projektu ověřování).</span><span class="sxs-lookup"><span data-stu-id="9d282-202">Open your application's Startup.cs class (that you either created manually as per the package instructions in `readme.txt`, or that was created by adding authentication to your project).</span></span> <span data-ttu-id="9d282-203">V `Startup.Configuration` metoda, vytvoření kontejneru Ninject, která volá Ninject *jádra*.</span><span class="sxs-lookup"><span data-stu-id="9d282-203">In the `Startup.Configuration` method, create the Ninject container, which Ninject calls the *kernel*.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample15.cs)]

<span data-ttu-id="9d282-204">Vytvoření instance překladače naše vlastní závislost:</span><span class="sxs-lookup"><span data-stu-id="9d282-204">Create an instance of our custom dependency resolver:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample16.cs)]

<span data-ttu-id="9d282-205">Vytvoření vazby pro `IStockTicker` následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="9d282-205">Create a binding for `IStockTicker` as follows:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample17.cs)]

<span data-ttu-id="9d282-206">Tento kód říká dvě věci.</span><span class="sxs-lookup"><span data-stu-id="9d282-206">This code is saying two things.</span></span> <span data-ttu-id="9d282-207">První, pokaždé, když aplikace potřebuje `IStockTicker`, jádra by měl vytvořit instanci `StockTicker`.</span><span class="sxs-lookup"><span data-stu-id="9d282-207">First, whenever the application needs an `IStockTicker`, the kernel should create an instance of `StockTicker`.</span></span> <span data-ttu-id="9d282-208">Druhý, `StockTicker` by měl být třída vytvořená jako objekt typu singleton.</span><span class="sxs-lookup"><span data-stu-id="9d282-208">Second, the `StockTicker` class should be a created as a singleton object.</span></span> <span data-ttu-id="9d282-209">Ninject vytvoří jednu instanci objektu a vrátí stejnou instanci pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="9d282-209">Ninject will create one instance of the object, and return the same instance for each request.</span></span>

<span data-ttu-id="9d282-210">Vytvoření vazby pro **IHubConnectionContext** následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="9d282-210">Create a binding for **IHubConnectionContext** as follows:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample18.cs)]

<span data-ttu-id="9d282-211">Tento kód creatres anonymní funkce, která se vrátí **IHubConnection**.</span><span class="sxs-lookup"><span data-stu-id="9d282-211">This code creatres an anonymous function that returns an **IHubConnection**.</span></span> <span data-ttu-id="9d282-212">**WhenInjectedInto** metoda říká Ninject tuto funkci použít, pouze při vytváření `IStockTicker` instancí.</span><span class="sxs-lookup"><span data-stu-id="9d282-212">The **WhenInjectedInto** method tells Ninject to use this function only when creating `IStockTicker` instances.</span></span> <span data-ttu-id="9d282-213">Důvodem je, že vytvoří SignalR **IHubConnectionContext** instance interně, a nechceme přepsat, jak je vytvořila SignalR.</span><span class="sxs-lookup"><span data-stu-id="9d282-213">The reason is that SignalR creates **IHubConnectionContext** instances internally, and we don't want to override how SignalR creates them.</span></span> <span data-ttu-id="9d282-214">Tato funkce platí jenom pro naše `StockTicker` třídy.</span><span class="sxs-lookup"><span data-stu-id="9d282-214">This function only applies to our `StockTicker` class.</span></span>

<span data-ttu-id="9d282-215">Předat do překladače závislostí **MapSignalR** metodu tak, že přidáte Centrum konfigurací:</span><span class="sxs-lookup"><span data-stu-id="9d282-215">Pass the dependency resolver into the **MapSignalR** method by adding a hub configuration:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample19.cs)]

<span data-ttu-id="9d282-216">Aktualizujte metodu Startup.ConfigureSignalR ve třídě po spuštění tohoto příkladu nový parametr:</span><span class="sxs-lookup"><span data-stu-id="9d282-216">Update the Startup.ConfigureSignalR method in the sample's Startup class with the new parameter:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample20.cs)]

<span data-ttu-id="9d282-217">Nyní SignalR použije překladač podle **MapSignalR**, namísto výchozí překladač.</span><span class="sxs-lookup"><span data-stu-id="9d282-217">Now SignalR will use the resolver specified in **MapSignalR**, instead of the default resolver.</span></span>

<span data-ttu-id="9d282-218">Tady je úplný výpis pro kódu `Startup.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="9d282-218">Here is the complete code listing for `Startup.Configuration`.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample21.cs)]

<span data-ttu-id="9d282-219">Ke spuštění aplikace StockTicker v sadě Visual Studio, stiskněte klávesu F5.</span><span class="sxs-lookup"><span data-stu-id="9d282-219">To run the StockTicker application in Visual Studio, press F5.</span></span> <span data-ttu-id="9d282-220">V okně prohlížeče přejděte na `http://localhost:*port*/SignalR.Sample/StockTicker.html`.</span><span class="sxs-lookup"><span data-stu-id="9d282-220">In the browser window, navigate to `http://localhost:*port*/SignalR.Sample/StockTicker.html`.</span></span>

![](dependency-injection/_static/image3.png)

<span data-ttu-id="9d282-221">Aplikace má přesně stejnou funkci jako před.</span><span class="sxs-lookup"><span data-stu-id="9d282-221">The application has exactly the same functionality as before.</span></span> <span data-ttu-id="9d282-222">(Popis najdete v tématu [serverové vysílání s knihovnou ASP.NET SignalR](../getting-started/tutorial-server-broadcast-with-signalr.md).) Jsme nedošlo ke změně chování; usnadňuje testování, údržbu a rozvíjet říkám kód.</span><span class="sxs-lookup"><span data-stu-id="9d282-222">(For a description, see [Server Broadcast with ASP.NET SignalR](../getting-started/tutorial-server-broadcast-with-signalr.md).) We haven't changed the behavior; just made the code easier to test, maintain, and evolve.</span></span>
