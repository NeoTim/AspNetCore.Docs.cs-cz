---
uid: signalr/overview/older-versions/tutorial-high-frequency-realtime-with-signalr
title: Vysokofrekvenční Reálný čas s knihovnou SignalR 1.x | Dokumentace Microsoftu
author: pfletcher
description: Tento kurz ukazuje, jak vytvořit webovou aplikaci, která používá knihovnu ASP.NET SignalR k zajištění funkce zasílání zpráv vysokou frekvencí. Vysoká frekvence zasílání zpráv v...
ms.author: riande
ms.date: 04/16/2013
ms.assetid: ad2a5da5-2e79-40ea-bc84-028d327f5982
msc.legacyurl: /signalr/overview/older-versions/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: a679a7c66a94fa440a1ead64225eb86f7de90c9e
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287953"
---
<a name="high-frequency-realtime-with-signalr-1x"></a>Vysokofrekvenční reálný čas s knihovnou SignalR 1.x
====================
podle [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Tento kurz ukazuje, jak vytvořit webovou aplikaci, která používá knihovnu ASP.NET SignalR k zajištění funkce zasílání zpráv vysokou frekvencí. Vysoká frekvence zasílání zpráv v tomto případě znamená, že aktualizace, které se odesílají s pevnou sazbou; v případě této aplikace, až 10 zpráv za sekundu.
> 
> Aplikace, kterou vytvoříte v tomto kurzu zobrazí obrazec, který můžete přetáhnout uživatelů. Umístění obrazce ve všech propojených prohlížečů pak aktualizuje tak, aby odpovídala pozici Přetahované tvaru použitím vypršel časový limit aktualizace.
> 
> Koncepty představenými v tomto kurzu jste aplikací v reálném čase hry a další aplikace simulace.
> 
> Si mohou komentáře u tohoto kurzu. Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com).


## <a name="overview"></a>Přehled

Tento kurz ukazuje, jak vytvořit aplikaci, která sdílí stav objektu se ostatní prohlížeče v reálném čase. Aplikace, kterou vytvoříme, se nazývá MoveShape. Na stránce MoveShape se zobrazí element HTML Div, který uživatel můžete přetáhnout; Když uživatel přetáhne divu, pošle se na server, který vám potom sdělí, všechny ostatní připojené klienty se aktualizovat umístění obrazce tak, aby odpovídaly jeho nové pozice.

![Okno aplikace](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

Aplikace vytvořené v tomto kurzu je založena na ukázku Damianem Edwardsem. Můžou zobrazit videa obsahující tuto ukázku [tady](https://channel9.msdn.com/Series/Building-Web-Apps-with-ASP-NET-Jump-Start/Building-Web-Apps-with-ASPNET-Jump-Start-08-Real-time-Communication-with-SignalR).

Tento kurz se spustí prokazováním zasílání zpráv SignalR z každé události, který se aktivuje přetáhnout na obrazec. Každý připojený klient aktualizuje umístění v místní verzi tvar pak pokaždé, když je přijata zpráva.

I aplikace bude fungovat s touto metodou, není doporučenou programovací model, protože by existovat žádné horní omezení počtu zpráv, získávání odeslat, aby klienti a server může získat zahlcen zprávy a by dojít ke snížení výkonu . Zobrazené animací na straně klienta by také odděleném jako tvar by okamžitě přesunout jednotlivé metody spíše než přesouvání plynule do každého nového umístění. V dalších částech tohoto kurzu vám ukáže, jak vytvořit funkci časovače, který omezí maximální rychlost přenosu, ve kterém jsou zprávy odesílány klienta nebo serveru a postup přesunutí tvar plynule mezi umístěními. Konečná verze aplikace vytvořené v tomto kurzu si můžete stáhnout z [Galerie kódu na](https://code.msdn.microsoft.com/SignalR-MoveShape-demo-3366dac6).

Tento kurz obsahuje následující části:

- [Požadavky](#prerequisites)
- [Vytvoření projektu](#createtheproject)
- [Přidání balíčků funkce SignalR technologie ASP.NET a JQuery.UI NuGet](#nugetpackages)
- [Vytvoření základní aplikace](#baseapp)
- [Přidat cyklus klienta](#clientloop)
- [Přidat cyklus serveru](#serverloop)
- [Přidat plynulou animaci na straně klienta](#animation)
- [Další kroky](#furthersteps)

<a id="prerequisites"></a>

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje Visual Studio 2012 nebo Visual Studio 2010. Pokud se používá Visual Studio 2010, že projekt bude používat rozhraní .NET Framework 4 namísto rozhraní .NET Framework 4.5.

Pokud používáte sadu Visual Studio 2012, doporučuje se, že nainstalujete [ASP.NET and Web Tools 2012.2 aktualizace](https://go.microsoft.com/fwlink/?LinkId=282650). Tato aktualizace obsahuje nové funkce, jako je vylepšení publikování nové funkce a nové šablony.

Pokud máte Visual Studio 2010, ujistěte se, že [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) je nainstalována.

<a id="createtheproject"></a>

## <a name="create-the-project"></a>Vytvoření projektu

V této části vytvoříme projektu v sadě Visual Studio.

1. Z **souboru** klikněte na nabídku **nový projekt**.
2. V **nový projekt** dialogového okna rozbalte **jazyka C#** pod **šablony** a vyberte **webové**.
3. Vyberte **prázdná webová aplikace ASP.NET** šablony, pojmenujte projekt *MoveShapeDemo*a klikněte na tlačítko **OK**.

    ![Vytvoření nového projektu](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)

<a id="nugetpackages"></a>

## <a name="add-the-signalr-and-jqueryui-nuget-packages"></a>Přidat funkci SignalR a balíčky JQuery.UI NuGet

Funkce SignalR můžete přidat do projektu po instalaci balíčku NuGet. V tomto kurzu se také použít balíček JQuery.UI umožňující tvar, který má být kvůli usnadnění použití vypsány a animovat.

1. Klikněte na tlačítko **nástroje | Správce balíčků NuGet | Konzola správce balíčků**.
2. Správce balíčků zadejte následující příkaz.

    [!code-powershell[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.ps1)]

    Funkce SignalR balíček nainstaluje celou řadou dalších balíčcích NuGet jako závislosti. Po dokončení instalace budete mít vše serveru a klientské komponenty potřebné k použití v aplikaci ASP.NET SignalR.
3. Zadáním následujícího příkazu do konzoly Správce balíčků knihovny JQuery a JQuery.UI balíčky nainstalovat.

    [!code-powershell[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.ps1)]

<a id="baseapp"></a>

## <a name="create-the-base-application"></a>Vytvoření základní aplikace

V této části vytvoříme aplikaci prohlížeče, která odešle umístění obrazce na server během každou událost pohybu myší. Po přijetí, server pak vyšle tyto informace do všech ostatních připojených klientů. Jsme budete rozbalit na tuto aplikaci v předchozích částech.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat**, **třídy...** . Název třídy **MoveShapeHub** a klikněte na tlačítko **přidat**.
2. Nahraďte kód v novém **MoveShapeHub** třídy následujícím kódem.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.cs)]

    `MoveShapeHub` Třídy výše je implementace rozbočovače SignalR. Stejně jako [Začínáme s knihovnou SignalR](index.md) kurzu centra má metodu, která klientům se volat přímo. V tomto případě klient odešle objekt, který obsahuje novou souřadnice X a Y obrazce na server, který pak získá vysílali pro všechny ostatní připojených klientů. Funkce SignalR bude automaticky serializovat tento objekt pomocí formátu JSON.

    Objekt, který se pošle na klientovi (`ShapeModel`) obsahuje členy k uložení umístění obrazce. Verze objektu na serveru také obsahuje členy, aby mohli sledovat data klientů, které ukládají, tak, aby daného klienta se neodešlou svá vlastní data. Používá tento člen `JsonIgnore` atribut zabránit serializována a může odeslat klientovi.
3. V dalším kroku nastavíme centra při spuštění aplikace. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **přidat | Globální třída aplikace**. Přijměte výchozí název *globální* a klikněte na tlačítko **OK**.

    ![Přidání globální třída aplikace](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)
4. Přidejte následující `using` příkazem za zadaných **pomocí** příkazy ve třídě Global.asax.cs.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.cs)]
5. Přidejte následující řádek kódu `Application_Start` metoda globální třídy k registraci výchozí trasu pro funkci SignalR.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

    Váš soubor global.asax by měl vypadat nějak takto:

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.cs)]
6. V dalším kroku přidáme klienta. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **přidat | Nová položka**. V **přidat novou položku** dialogového okna, vyberte **stránku Html**. Na stránce zadejte vhodný název (jako je **Default.html**) a klikněte na tlačítko **přidat**.
7. V **Průzkumníka řešení**, klikněte pravým tlačítkem na stránku, který jste právě vytvořili a klikněte na tlačítko **nastavit jako úvodní stránku**.
8. Nahraďte kód výchozí stránku HTML následující fragment kódu.

    > [!NOTE]
    > Ověřte, že skript odkazuje pod shoda balíčky se přidaly do projektu ve složce Scripts. V sadě Visual Studio 2010 nemusí odpovídat verzi knihovny JQuery a SignalR přidány do projektu následující čísla verze.

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample7.html)]

    Výše uvedený kód HTML a JavaScript vytvoří red Div volá tvar, umožňuje přetahování chování tvaru pomocí knihovny jQuery a používá obrazce `drag` události a umístění obrazce odeslat na server.
9. Stisknutím klávesy F5 spusťte aplikaci. Zkopírujte adresu URL na stránku a vložte ho do druhé okno prohlížeče. Přetáhněte obrazec v jednom z okna prohlížeče; tvar v druhém okně prohlížeče by se měla přesunout.

    ![Okno aplikace](tutorial-high-frequency-realtime-with-signalr/_static/image4.png)

<a id="clientloop"></a>

## <a name="add-the-client-loop"></a>Přidat cyklus klienta

Protože odesílání umístění tvar na každou událost pohybu myší vytvořit nepotřebných objemu síťových přenosů, zpráv od klienta je potřeba omezit. Použijeme javascript `setInterval` funkce nastavit smyčku, která odešle nové informace o umístění na server s pevnou sazbou. Smyčka je velmi základní reprezentace "herní cyklus" opakovaně volaná funkce, která řídí všechny funkce hru nebo jiných simulace.

1. Aktualizace kódu klienta na stránce HTML tak, aby odpovídala následující fragment kódu.

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample8.html)]

    Výše uvedené aktualizace přidá `updateServerModel` funkce, který je volán na pevnou frekvencí. Tato funkce odesílá umístění dat na server pokaždé, když `moved` příznak určuje, že je nová umístění dat k odeslání.
2. Stisknutím klávesy F5 spusťte aplikaci. Zkopírujte adresu URL na stránku a vložte ho do druhé okno prohlížeče. Přetáhněte obrazec v jednom z okna prohlížeče; tvar v druhém okně prohlížeče by se měla přesunout. Protože se omezí počet zpráv, které odesílání na server, se nezobrazí animace pohodlné stejně jako v předchozí části.

    ![Okno aplikace](tutorial-high-frequency-realtime-with-signalr/_static/image5.png)

<a id="serverloop"></a>

## <a name="add-the-server-loop"></a>Přidat cyklus serveru

V aktuální aplikaci zprávy odeslané ze serveru klientovi chodit přímo tak často, jak jejich přijetí. To představuje podobný problém, jak je vidět na straně klienta; častěji, než jsou potřeba, a připojení může být zahlcenou díky tomu lze odesílat zprávy. Tato část popisuje postup aktualizace serveru pro implementaci časovač, který omezuje počet odchozích zpráv.

1. Nahraďte obsah `MoveShapeHub.cs` následujícím fragmentem kódu.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample9.cs)]

    Výše uvedený kód rozšiřuje klienta k přidání `Broadcaster` třídu, která omezuje odchozí zprávy pomocí `Timer` třídy z rozhraní .NET framework.

    Protože je Centrum samotné přechodné (ho vytvoří pokaždé, když je to potřeba), `Broadcaster` se vytvoří jako typ singleton. Opožděná inicializace (představíme v rozhraní .NET 4) slouží k jeho vytvoření odložit, dokud není potřeba, zajištění, že první instanci rozbočovače zcela vytvořil před spuštěním časovač.

    Volání klientů `UpdateShape` funkce je poté přesunut mimo centra `UpdateModel` metodu tak, že je volána již okamžitě pokaždé, když se přijme příchozí zprávy. Místo toho se pošle zprávy, které mají klienti ve výši 25 volání za sekundu, spravuje `_broadcastLoop` časovač v rámci `Broadcaster` třídy.

    A konečně, namísto volání metody klienta z centra přímo, `Broadcaster` třídy je potřeba získat odkaz na aktuálně operačního centra (`_hubContext`) pomocí `GlobalHost`.
2. Stisknutím klávesy F5 spusťte aplikaci. Zkopírujte adresu URL na stránku a vložte ho do druhé okno prohlížeče. Přetáhněte obrazec v jednom z okna prohlížeče; tvar v druhém okně prohlížeče by se měla přesunout. Nesmí být vidět rozdíl v prohlížeči z předchozí části, ale se omezí počet zpráv, které odesílání do klienta.

    ![Okno aplikace](tutorial-high-frequency-realtime-with-signalr/_static/image6.png)

<a id="animation"></a>

## <a name="add-smooth-animation-on-the-client"></a>Přidat plynulou animaci na straně klienta

Aplikace je skoro hotová, ale jsme dokonce vytvářet jeden další vylepšení v pohybu tvar na straně klienta jako je přesunut v reakci na zprávu serveru. Namísto nastavení pozice tvar do nového umístění uvedena v každém serveru, použijeme knihovna uživatelského rozhraní JQuery `animate` funkce přesunout tvar plynule mezi jeho aktuální a nové pozice.

1. Také aktualizovat klienta sady `updateShape` metoda vypadat jako následující zvýrazněný kód:

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample10.html?highlight=35-42)]

    Výše uvedený kód přesune tvaru z původního umístění do nové Dal serveru v průběhu intervalu animace (v tomto případě 100 milisekund). Všechny předchozí animace spuštěna s tvarem není zaškrtnuto, před zahájením nové animace.
2. Stisknutím klávesy F5 spusťte aplikaci. Zkopírujte adresu URL na stránku a vložte ho do druhé okno prohlížeče. Přetáhněte obrazec v jednom z okna prohlížeče; tvar v druhém okně prohlížeče by se měla přesunout. Pohyb tvaru v druhém okně by se zobrazit méně přehrávat nepravidelně, jako je pohyb interpolovaných přes čas Místo nastavování jednou za příchozí zprávy.

    ![Okno aplikace](tutorial-high-frequency-realtime-with-signalr/_static/image7.png)

<a id="furthersteps"></a>

## <a name="further-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak programovat aplikace SignalR, která odesílá zprávy s vysokou frekvencí mezi klienty a servery. Toto paradigma komunikace je užitečné pro vývoj online hry a další simulace, jako například [ShootR hru vytvořili s knihovnou SignalR](http://shootr.signalr.net).

Hotové aplikace vytvořené v tomto kurzu si můžete stáhnout z [Galerie kódu na](https://code.msdn.microsoft.com/SignalR-MoveShape-demo-3366dac6).

Další informace o konceptech vývoj SignalR, najdete na následujících webech pro funkci SignalR zdrojový kód a prostředky:

- [Projekt SignalR](http://signalr.net)
- [Funkce SignalR Githubu a ukázky](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)
