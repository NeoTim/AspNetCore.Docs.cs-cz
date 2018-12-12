---
uid: signalr/overview/advanced/dependency-injection
title: Injektáž závislostí v knihovně SignalR | Dokumentace Microsoftu
author: MikeWasson
description: Verze softwaru použitým v tomto tématu Visual Studio 2013 .NET 4.5 SignalR 2 předchozí verze tohoto tématu informace o předchozích verzích...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: a14121ae-02cf-4024-8af0-9dd0dc810690
msc.legacyurl: /signalr/overview/advanced/dependency-injection
msc.type: authoredcontent
ms.openlocfilehash: f5959902b030621a19d912d7db2ea0faf95b7a7c
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287786"
---
<a name="dependency-injection-in-signalr"></a>Injektáž závislostí v centrech SignalR
====================
podle [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> ## <a name="software-versions-used-in-this-topic"></a>Verze softwaru použitým v tomto tématu
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - Funkce SignalR verze 2
>
>
>
> ## <a name="previous-versions-of-this-topic"></a>Předchozích verzích tohoto tématu
>
> Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).
>
> ## <a name="questions-and-comments"></a>Otázky a komentáře
>
> Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky. Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).


Injektáž závislostí je způsob, jak odebrat pevně zakódované závislosti mezi objekty usnadnit k nahrazení objektu závislosti, buď pro testování (pomocí mock objektů), nebo chcete změnit chování za běhu. Tento kurz ukazuje, jak provádět injektáž závislostí v centrech SignalR. Také ukazuje, jak používat technologie IoC kontejnery s knihovnou SignalR. Kontejner IoC je obecné rozhraní pro vkládání závislostí.

## <a name="what-is-dependency-injection"></a>Co je injektáž závislostí?

Tuto část přeskočte, pokud jste už obeznámení s vkládání závislostí.

*Injektáž závislostí* (DI) je vzor, ve kterém jsou objekty není zodpovědný za vytváření vlastní závislosti. Tady je jednoduchý příklad ke DI. Předpokládejme, že budete mít objekt, který je potřeba protokolování zpráv. Můžete třeba definovat rozhraní protokolování:

[!code-csharp[Main](dependency-injection/samples/sample1.cs)]

V objektu, můžete vytvořit `ILogger` protokolování zpráv:

[!code-csharp[Main](dependency-injection/samples/sample2.cs)]

Tento postup funguje, ale není optimální. Pokud budete chtít nahradit `FileLogger` sebou `ILogger` implementace, budete muset upravit `SomeComponent`. Domnívat, že mnoho dalších objektů použijte `FileLogger`, budete muset změnit všechny z nich. Nebo pokud se rozhodnete provést `FileLogger` typu singleton, budete také muset provést změny v celé aplikaci.

Lepším řešením je "Vložit" `ILogger` do objektu, například pomocí argumentu konstruktoru:

[!code-csharp[Main](dependency-injection/samples/sample3.cs)]

Nyní na objekt neodpovídá pro výběr, který `ILogger` používat. Je možné swich `ILogger` implementace beze změny, které jsou na ní závislé objekty.

[!code-csharp[Main](dependency-injection/samples/sample4.cs)]

Tento model se nazývá [konstruktor vkládání](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection). Jiné vzor je vkládání setter, kde nastavujete závislost prostřednictvím metody setter nebo vlastnosti.

## <a name="simple-dependency-injection-in-signalr"></a>Injektáž závislostí jednoduché v knihovně SignalR

Vezměte v úvahu chatovací aplikaci z kurzu [Začínáme s knihovnou SignalR](../getting-started/tutorial-getting-started-with-signalr.md). Tady je třída rozbočovače z této aplikace:

[!code-csharp[Main](dependency-injection/samples/sample5.cs)]

Předpokládejme, že chcete ukládat zprávy chatu na serveru před jejich odesláním. Může definovat rozhraní, který získává tuto funkci a použít DI vkládat rozhraní portálu `ChatHub` třídy.

[!code-csharp[Main](dependency-injection/samples/sample6.cs)]

Jediným problémem je, že aplikace SignalR přímo nevytváří hubs; SignalR je pro vás vytvoří. Ve výchozím nastavení SignalR očekává, že třída rozbočovače mít konstruktor bez parametrů. Však můžete snadno zaregistrovat funkci pro vytvoření instancí rozbočovače a tuto funkci použít k provedení DI. Registraci funkci voláním **GlobalHost.DependencyResolver.Register**.

[!code-csharp[Main](dependency-injection/samples/sample7.cs)]

Nyní SignalR vyvolá tuto anonymní funkci pokaždé, když je potřeba vytvořit `ChatHub` instance.

## <a name="ioc-containers"></a>Technologie IoC kontejnery

Předchozí kód je v pořádku pro jednoduché případy. Ale máte stále zapisovat:

[!code-csharp[Main](dependency-injection/samples/sample8.cs)]

V komplexní aplikace s velký počet závislostí potřebujete napsat velké množství tento kód "propojení". Tento kód může být obtížné spravovat, zejména v případě, že jsou vnořené závislosti. Je taky obtížné testování částí.

Jedním řešením je použití kontejner IoC. Kontejner IoC je softwarová součást, která zodpovídá za správu závislostí. Registrace typů s kontejnerem a následné použití k vytvoření objektů kontejneru. Kontejner automaticky zjistí vztahy závislostí. Spousta kontejnerů IoC také umožňují řídit věci, jako je životní cyklus objektů a obor.

> [!NOTE]
> "IoC" znamená "inverzi ovládacího prvku", což je obecný vzor, pokud rámec volání do kódu aplikace. Kontejner IoC vytvoří objekty, které "Invertuje" obvyklý tok řízení.


## <a name="using-ioc-containers-in-signalr"></a>Použití technologie IoC kontejnerů v knihovně SignalR

Chatovací aplikaci je pravděpodobně příliš jednoduché, abyste využili výhod kontejner IoC. Místo toho, Podívejme se [StockTicker](http://nuget.org/packages/microsoft.aspnet.signalr.sample) vzorku.

Ukázka StockTicker definuje dva hlavní třídy:

- `StockTickerHub`: Třída rozbočovače, která spravuje připojení klientů.
- `StockTicker`: Jednotlivý prvek, který obsahuje ceny akcií a je pravidelně aktualizuje.

`StockTickerHub` obsahuje odkaz na `StockTicker` singleton, zatímco `StockTicker` obsahuje odkaz na **IHubConnectionContext** pro `StockTickerHub`. Toto rozhraní se používá ke komunikaci s `StockTickerHub` instancí. (Další informace najdete v tématu [serverové vysílání s knihovnou ASP.NET SignalR](../getting-started/tutorial-server-broadcast-with-signalr.md).)

K trochu rozplétání těchto závislostí můžeme použít kontejner IoC. Nejprve můžeme zjednodušit `StockTickerHub` a `StockTicker` třídy. V následujícím kódu můžu jste zakomentované části, že nebudeme potřebovat.

Odebrat konstruktor bez parametrů z `StockTickerHub`. Místo toho vždy použije DI centrum vytvořit.

[!code-csharp[Main](dependency-injection/samples/sample9.cs)]

StockTicker odeberte instanci typu singleton. Později řízení životnosti StockTicker použijeme kontejner IoC. Navíc musí být konstruktor public.

[!code-csharp[Main](dependency-injection/samples/sample10.cs?highlight=7)]

Dále jsme Refaktorovat kód tak, že vytvoříte rozhraní pro `StockTicker`. Použijeme oddělit toto rozhraní `StockTickerHub` z `StockTicker` třídy.

Vytvoří malou Visual Studio tento druh refaktoringu snadné. Otevřete soubor StockTicker.cs, klikněte pravým tlačítkem na `StockTicker` deklarace třídy a vyberte **Refaktorovat** ... **Extrahování rozhraní**.

![](dependency-injection/_static/image1.png)

V **extrahování rozhraní** dialogového okna, klikněte na tlačítko **Vybrat vše**. Nechte ostatní výchozí hodnoty. Klikněte na **OK**.

![](dependency-injection/_static/image2.png)

Visual Studio vytvoří nové rozhraní s názvem `IStockTicker`a taky změní `StockTicker` odvodit z `IStockTicker`.

Otevřete soubor IStockTicker.cs a změňte rozhraní **veřejné**.

[!code-csharp[Main](dependency-injection/samples/sample11.cs?highlight=1)]

V `StockTickerHub` třídy, dvě instance změnit `StockTicker` k `IStockTicker`:

[!code-csharp[Main](dependency-injection/samples/sample12.cs?highlight=4,6)]

Vytvoření `IStockTicker` rozhraní není nezbytně nutné, ale já bych chtěl ukazují, jak DI vám může pomoct snížit párování mezi komponentami vaší aplikace.

## <a name="add-the-ninject-library"></a>Přidání knihovny Ninject

Existuje spousta open source technologie IoC kontejnerů pro .NET. Pro účely tohoto kurzu použiju [Ninject](http://www.ninject.org/). (Zahrnout další oblíbené knihovny [Castle Windsor](http://www.castleproject.org/), [Spring.Net](http://www.springframework.net/), [Autofac](https://code.google.com/p/autofac/), [Unity](https://github.com/unitycontainer/unity), a [StructureMap ](http://docs.structuremap.net).)

Použití Správce balíčků NuGet k instalaci [Ninject knihovny](https://nuget.org/packages/Ninject/3.0.1.10). V sadě Visual Studio z **nástroje** nabídky vyberte možnost **Správce balíčků NuGet** > **Konzola správce balíčků**. V okně konzoly Správce balíčků zadejte následující příkaz:

[!code-powershell[Main](dependency-injection/samples/sample13.ps1)]

## <a name="replace-the-signalr-dependency-resolver"></a>Nahraďte překladač závislostí SignalR

Pokud chcete použít Ninject v rámci SignalR, vytvořte třídu, která je odvozena z **DefaultDependencyResolver**.

[!code-csharp[Main](dependency-injection/samples/sample14.cs)]

Tato třída přepíše **GetService** a **GetServices** metody **DefaultDependencyResolver**. Funkce SignalR volá tyto metody k vytvoření různých objektů za běhu, včetně instancí rozbočovače, jakož i různé služby používaná interně knihovnou SignalR.

- **GetService** metoda vytvoří jednu instanci typu. Potlačí tuto metodu volat Ninject jádra **TryGet** metody. Pokud tato metoda vrátí hodnotu null, vrátit k výchozí překladač.
- **GetServices** metoda vytvoří kolekci objektů zadaného typu. Potlačí tuto metodu ke zřetězení výsledky z Ninject s výsledky z výchozí překladač.

## <a name="configure-ninject-bindings"></a>Nakonfigurujte Ninject vazby

Nyní použijeme Ninject deklarovat typ vazby.

Otevřete třídu Startup.cs vaší aplikace (buď vytvořené ručně podle pokynů balíček `readme.txt`, nebo který byl vytvořen tak, že přidáte do projektu ověřování). V `Startup.Configuration` metoda, vytvoření kontejneru Ninject, která volá Ninject *jádra*.

[!code-csharp[Main](dependency-injection/samples/sample15.cs)]

Vytvoření instance překladače naše vlastní závislost:

[!code-csharp[Main](dependency-injection/samples/sample16.cs)]

Vytvoření vazby pro `IStockTicker` následujícím způsobem:

[!code-csharp[Main](dependency-injection/samples/sample17.cs)]

Tento kód říká dvě věci. První, pokaždé, když aplikace potřebuje `IStockTicker`, jádra by měl vytvořit instanci `StockTicker`. Druhý, `StockTicker` by měl být třída vytvořená jako objekt typu singleton. Ninject vytvoří jednu instanci objektu a vrátí stejnou instanci pro každý požadavek.

Vytvoření vazby pro **IHubConnectionContext** následujícím způsobem:

[!code-csharp[Main](dependency-injection/samples/sample18.cs)]

Tento kód creatres anonymní funkce, která se vrátí **IHubConnection**. **WhenInjectedInto** metoda říká Ninject tuto funkci použít, pouze při vytváření `IStockTicker` instancí. Důvodem je, že vytvoří SignalR **IHubConnectionContext** instance interně, a nechceme přepsat, jak je vytvořila SignalR. Tato funkce platí jenom pro naše `StockTicker` třídy.

Předat do překladače závislostí **MapSignalR** metodu tak, že přidáte Centrum konfigurací:

[!code-csharp[Main](dependency-injection/samples/sample19.cs)]

Aktualizujte metodu Startup.ConfigureSignalR ve třídě po spuštění tohoto příkladu nový parametr:

[!code-csharp[Main](dependency-injection/samples/sample20.cs)]

Nyní SignalR použije překladač podle **MapSignalR**, namísto výchozí překladač.

Tady je úplný výpis pro kódu `Startup.Configuration`.

[!code-csharp[Main](dependency-injection/samples/sample21.cs)]

Ke spuštění aplikace StockTicker v sadě Visual Studio, stiskněte klávesu F5. V okně prohlížeče přejděte na `http://localhost:*port*/SignalR.Sample/StockTicker.html`.

![](dependency-injection/_static/image3.png)

Aplikace má přesně stejnou funkci jako před. (Popis najdete v tématu [serverové vysílání s knihovnou ASP.NET SignalR](../getting-started/tutorial-server-broadcast-with-signalr.md).) Jsme nedošlo ke změně chování; usnadňuje testování, údržbu a rozvíjet říkám kód.
