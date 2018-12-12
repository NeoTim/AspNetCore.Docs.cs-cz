---
uid: signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
title: 'Kurz: Vysokofrekvenční Reálný čas s knihovnou SignalR 2 | Dokumentace Microsoftu'
author: pfletcher
description: Tento kurz ukazuje, jak vytvořit webovou aplikaci, která používá knihovnu ASP.NET SignalR k zajištění funkce zasílání zpráv vysokou frekvencí. Vysoká frekvence zasílání zpráv v...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: 9f969dda-78ea-4329-b1e3-e51c02210a2b
msc.legacyurl: /signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 04ce650509268ee63daafe24bc8dcc9725aea16b
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287712"
---
<a name="tutorial-high-frequency-realtime-with-signalr-2"></a>Kurz: Vysokofrekvenční Reálný čas s knihovnou SignalR 2
====================
podle [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[Stáhnout dokončený projekt](http://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a)

> Tento kurz ukazuje, jak vytvořit webovou aplikaci, která používá ASP.NET SignalR 2 zasílání zpráv nakonfigurovánu vysokou frekvencí. Vysoká frekvence zasílání zpráv v tomto případě znamená, že aktualizace, které se odesílají s pevnou sazbou; v případě této aplikace, až 10 zpráv za sekundu.
>
> Aplikace, kterou vytvoříte v tomto kurzu zobrazí obrazec, který můžete přetáhnout uživatelů. Umístění obrazce ve všech propojených prohlížečů pak aktualizuje tak, aby odpovídala pozici Přetahované tvaru použitím vypršel časový limit aktualizace.
>
> Koncepty představenými v tomto kurzu jste aplikací v reálném čase hry a další aplikace simulace.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - Funkce SignalR verze 2
>
>
>
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a>V tomto kurzu pomocí sady Visual Studio 2012
>
>
> Pokud chcete použít Visual Studio 2012 s tímto kurzem, postupujte takto:
>
> - Aktualizace vašeho [Správce balíčků](http://docs.nuget.org/docs/start-here/installing-nuget) na nejnovější verzi.
> - Nainstalujte [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).
> - Instalace webové platformy, vyhledejte a nainstalujte **technologie ASP.NET a webové nástroje 2013.1 pro Visual Studio 2012**. Tím se nainstaluje šablony sady Visual Studio pro funkci SignalR třídy jako **centra**.
> - Některé šablony (jako například **třídy pro spuštění OWIN**) nebudou k dispozici; pro ty, použijte místo toho soubor třídy.
>
>
> ## <a name="tutorial-versions"></a>Kurz verze
>
> Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).
>
> ## <a name="questions-and-comments"></a>Otázky a komentáře
>
> Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky. Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).


## <a name="overview"></a>Přehled

Tento kurz ukazuje, jak vytvořit aplikaci, která sdílí stav objektu se ostatní prohlížeče v reálném čase. Aplikace, kterou vytvoříme, se nazývá MoveShape. Na stránce MoveShape se zobrazí element HTML Div, který uživatel můžete přetáhnout; Když uživatel přetáhne divu, pošle se na server, který vám potom sdělí, všechny ostatní připojené klienty se aktualizovat umístění obrazce tak, aby odpovídaly jeho nové pozice.

![Okno aplikace](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

Aplikace vytvořené v tomto kurzu je založena na ukázku Damianem Edwardsem. Můžou zobrazit videa obsahující tuto ukázku [tady](https://channel9.msdn.com/Series/Building-Web-Apps-with-ASP-NET-Jump-Start/Building-Web-Apps-with-ASPNET-Jump-Start-08-Real-time-Communication-with-SignalR).

Tento kurz se spustí prokazováním zasílání zpráv SignalR z každé události, který se aktivuje přetáhnout na obrazec. Každý připojený klient aktualizuje umístění v místní verzi tvar pak pokaždé, když je přijata zpráva.

I aplikace bude fungovat s touto metodou, není doporučenou programovací model, protože by existovat žádné horní omezení počtu zpráv, získávání odeslat, aby klienti a server může získat zahlcen zprávy a by dojít ke snížení výkonu . Zobrazené animací na straně klienta by také odděleném jako tvar by okamžitě přesunout jednotlivé metody spíše než přesouvání plynule do každého nového umístění. V dalších částech tohoto kurzu vám ukáže, jak vytvořit funkci časovače, který omezí maximální rychlost přenosu, ve kterém jsou zprávy odesílány klienta nebo serveru a postup přesunutí tvar plynule mezi umístěními. Konečná verze aplikace vytvořené v tomto kurzu si můžete stáhnout z [Galerie kódu na](https://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a).

Tento kurz obsahuje následující části:

- [Požadavky](#prerequisites)
- [Vytvořte projekt a přidejte balíček SignalR a JQuery.UI NuGet](#createtheproject2013)
- [Vytvoření základní aplikace](#baseapp)
- [Od centra při spuštění aplikace](#startup2013)
- [Přidat cyklus klienta](#clientloop)
- [Přidat cyklus serveru](#serverloop)
- [Přidat plynulou animaci na straně klienta](#animation)
- [Další kroky](#furthersteps)

<a id="prerequisites"></a>

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje Visual Studio 2013.

<a id="createtheproject2013"></a>

## <a name="create-the-project-and-add-the-signalr-and-jqueryui-nuget-package"></a>Vytvořte projekt a přidejte balíček SignalR a JQuery.UI NuGet

V této části vytvoříme projektu v sadě Visual Studio 2013.

Následující postup slouží k vytvoření prázdná webová aplikace ASP.NET a přidejte knihovny SignalR a jQuery.UI Visual Studio 2013:

1. V sadě Visual Studio vytvořte webovou aplikaci ASP.NET.

    ![Vytvořte web](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)
2. V **nový projekt ASP.NET** okně, ponechejte tuto položku **prázdný** vybraný a klikněte na tlačítko **vytvořit projekt**.

    ![Vytvoření prázdného webu](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)
3. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt, vyberte **přidat | Třída rozbočovače SignalR (v2)**. Název třídy **MoveShapeHub.cs** a přidejte ho do projektu. Tento krok vytvoří **MoveShapeHub** třídy a přidá do projektu sadu souborů skriptů a odkazy na sestavení, podporující funkci SignalR.

    > [!NOTE]
    > Funkce SignalR můžete také přidat do projektu kliknutím **nástroje > Správce balíčků NuGet > Konzola správce balíčků** a spuštění příkazu:

    `install-package Microsoft.AspNet.SignalR`.

    Pokud používáte konzolu pro přidání SignalR, vytvořte třída rozbočovače SignalR jako samostatný krok po přidání SignalR.
4. Klikněte na tlačítko **nástroje > Správce balíčků NuGet > Konzola správce balíčků**. V okně Správce balíčku spusťte následující příkaz:

    `Install-Package jQuery.UI.Combined`

    Tím se nainstaluje knihovny uživatelského rozhraní jQuery, které budete používat pro animaci tvaru.
5. V **Průzkumníka řešení** rozbalte uzel skripty. Knihovny skriptů pro funkci SignalR, jQuery a jQueryUI jsou viditelné v projektu.

    ![Odkazy na knihovnu skriptu](tutorial-high-frequency-realtime-with-signalr/_static/image4.png)

<a id="baseapp"></a>

## <a name="create-the-base-application"></a>Vytvoření základní aplikace

V této části vytvoříme aplikaci prohlížeče, která odešle umístění obrazce na server během každou událost pohybu myší. Po přijetí, server pak vyšle tyto informace do všech ostatních připojených klientů. Jsme budete rozbalit na tuto aplikaci v předchozích částech.

1. Pokud jste ještě nevytvořili třídu MoveShapeHub.cs v **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat**, **třídy...** . Název třídy **MoveShapeHub** a klikněte na tlačítko **přidat**.
2. Nahraďte kód v novém **MoveShapeHub** třídy následujícím kódem.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.cs)]

    `MoveShapeHub` Třídy výše je implementace rozbočovače SignalR. Stejně jako [Začínáme s knihovnou SignalR](tutorial-getting-started-with-signalr.md) kurzu centra má metodu, která klientům se volat přímo. V tomto případě klient odešle objekt, který obsahuje novou souřadnice X a Y obrazce na server, který pak získá vysílali pro všechny ostatní připojených klientů. Funkce SignalR bude automaticky serializovat tento objekt pomocí formátu JSON.

    Objekt, který se pošle na klientovi (`ShapeModel`) obsahuje členy k uložení umístění obrazce. Verze objektu na serveru také obsahuje členy, aby mohli sledovat data klientů, které ukládají, tak, aby daného klienta se neodešlou svá vlastní data. Používá tento člen `JsonIgnore` atribut zabránit serializována a může odeslat klientovi.

<a id="startup2013"></a>
## <a name="starting-the-hub-when-the-application-starts"></a>Od centra při spuštění aplikace

1. V dalším kroku nastavíme mapování k centru při spuštění aplikace. V SignalR 2, je to tak, že přidáte třídy pro spuštění OWIN, který bude volat `MapSignalR` při spuštění třídy `Configuration` provedení metody při spuštění OWIN. Třída je přidán do vaší OWIN startup zpracovat pomocí `OwinStartup` atribut sestavení.

    V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **přidat | Třídy pro spuštění OWIN**. Název třídy *spuštění* a klikněte na tlačítko **OK**.
2. Změňte obsah souboru Startup.cs následujícím způsobem:

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.cs)]

<a id="client"></a>
## <a name="adding-the-client"></a>Přidání klienta

1. V dalším kroku přidáme klienta. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **přidat | Nová položka**. V **přidat novou položku** dialogového okna, vyberte **stránku Html**. Pojmenujte stránku **Default.html** a klikněte na tlačítko **přidat**.
2. V **Průzkumníka řešení**, klikněte pravým tlačítkem na stránku, který jste právě vytvořili a klikněte na tlačítko **nastavit jako úvodní stránku**.
3. Nahraďte kód výchozí stránku HTML následující fragment kódu.

    > [!NOTE]
    > Ověřte, že skript odkazuje pod shoda balíčky se přidaly do projektu ve složce Scripts.

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.html)]

    Výše uvedený kód HTML a JavaScript vytvoří red Div volá tvar, umožňuje přetahování chování tvaru pomocí knihovny jQuery a používá obrazce `drag` události a umístění obrazce odeslat na server.
4. Stisknutím klávesy F5 spusťte aplikaci. Zkopírujte adresu URL na stránku a vložte ho do druhé okno prohlížeče. Přetáhněte obrazec v jednom z okna prohlížeče; tvar v druhém okně prohlížeče by se měla přesunout.

    ![Okno aplikace](tutorial-high-frequency-realtime-with-signalr/_static/image5.png)

<a id="clientloop"></a>

## <a name="add-the-client-loop"></a>Přidat cyklus klienta

Protože odesílání umístění tvar na každou událost pohybu myší vytvořit nepotřebných objemu síťových přenosů, zpráv od klienta je potřeba omezit. Použijeme javascript `setInterval` funkce nastavit smyčku, která odešle nové informace o umístění na server s pevnou sazbou. Smyčka je velmi základní reprezentace "herní cyklus" opakovaně volaná funkce, která řídí všechny funkce hru nebo jiných simulace.

1. Aktualizace kódu klienta na stránce HTML tak, aby odpovídala následující fragment kódu.

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.html)]

    Výše uvedené aktualizace přidá `updateServerModel` funkce, který je volán na pevnou frekvencí. Tato funkce odesílá umístění dat na server pokaždé, když `moved` příznak určuje, že je nová umístění dat k odeslání.
2. Stisknutím klávesy F5 spusťte aplikaci. Zkopírujte adresu URL na stránku a vložte ho do druhé okno prohlížeče. Přetáhněte obrazec v jednom z okna prohlížeče; tvar v druhém okně prohlížeče by se měla přesunout. Protože se omezí počet zpráv, které odesílání na server, se nezobrazí animace pohodlné stejně jako v předchozí části.

    ![Okno aplikace](tutorial-high-frequency-realtime-with-signalr/_static/image6.png)

<a id="serverloop"></a>

## <a name="add-the-server-loop"></a>Přidat cyklus serveru

V aktuální aplikaci zprávy odeslané ze serveru klientovi chodit přímo tak často, jak jejich přijetí. To představuje podobný problém, jak je vidět na straně klienta; častěji, než jsou potřeba, a připojení může být zahlcenou díky tomu lze odesílat zprávy. Tato část popisuje postup aktualizace serveru pro implementaci časovač, který omezuje počet odchozích zpráv.

1. Nahraďte obsah `MoveShapeHub.cs` následujícím fragmentem kódu.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

    Výše uvedený kód rozšiřuje klienta k přidání `Broadcaster` třídu, která omezuje odchozí zprávy pomocí `Timer` třídy z rozhraní .NET framework.

    Protože je Centrum samotné přechodné (ho vytvoří pokaždé, když je to potřeba), `Broadcaster` se vytvoří jako typ singleton. Opožděná inicializace (představíme v rozhraní .NET 4) slouží k jeho vytvoření odložit, dokud není potřeba, zajištění, že první instanci rozbočovače zcela vytvořil před spuštěním časovač.

    Volání klientů `UpdateShape` funkce je poté přesunut mimo centra `UpdateModel` metodu tak, že je volána již okamžitě pokaždé, když se přijme příchozí zprávy. Místo toho se pošle zprávy, které mají klienti ve výši 25 volání za sekundu, spravuje `_broadcastLoop` časovač v rámci `Broadcaster` třídy.

    A konečně, namísto volání metody klienta z centra přímo, `Broadcaster` třídy je potřeba získat odkaz na aktuálně operačního centra (`_hubContext`) pomocí `GlobalHost`.
2. Stisknutím klávesy F5 spusťte aplikaci. Zkopírujte adresu URL na stránku a vložte ho do druhé okno prohlížeče. Přetáhněte obrazec v jednom z okna prohlížeče; tvar v druhém okně prohlížeče by se měla přesunout. Nesmí být vidět rozdíl v prohlížeči z předchozí části, ale se omezí počet zpráv, které odesílání do klienta.

    ![Okno aplikace](tutorial-high-frequency-realtime-with-signalr/_static/image7.png)

<a id="animation"></a>

## <a name="add-smooth-animation-on-the-client"></a>Přidat plynulou animaci na straně klienta

Aplikace je skoro hotová, ale jsme dokonce vytvářet jeden další vylepšení v pohybu tvar na straně klienta jako je přesunut v reakci na zprávu serveru. Namísto nastavení pozice tvar do nového umístění uvedena v každém serveru, použijeme knihovna uživatelského rozhraní JQuery `animate` funkce přesunout tvar plynule mezi jeho aktuální a nové pozice.

1. Také aktualizovat klienta sady `updateShape` metoda vypadat jako následující zvýrazněný kód:

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.html?highlight=33-40)]

    Výše uvedený kód přesune tvaru z původního umístění do nové Dal serveru v průběhu intervalu animace (v tomto případě 100 milisekund). Všechny předchozí animace spuštěna s tvarem není zaškrtnuto, před zahájením nové animace.
2. Stisknutím klávesy F5 spusťte aplikaci. Zkopírujte adresu URL na stránku a vložte ho do druhé okno prohlížeče. Přetáhněte obrazec v jednom z okna prohlížeče; tvar v druhém okně prohlížeče by se měla přesunout. Pohyb tvaru v druhém okně by se zobrazit méně přehrávat nepravidelně, jako je pohyb interpolovaných přes čas Místo nastavování jednou za příchozí zprávy.

    ![Okno aplikace](tutorial-high-frequency-realtime-with-signalr/_static/image8.png)

<a id="furthersteps"></a>

## <a name="further-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak programovat aplikace SignalR, která odesílá zprávy s vysokou frekvencí mezi klienty a servery. Toto paradigma komunikace je užitečné pro vývoj online hry a další simulace, jako například [ShootR hru vytvořili s knihovnou SignalR](https://shootr.azurewebsites.net/).

Hotové aplikace vytvořené v tomto kurzu si můžete stáhnout z [Galerie kódu na](https://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a).

Další informace o konceptech vývoj SignalR, najdete na následujících webech pro funkci SignalR zdrojový kód a prostředky:

- [Projekt SignalR](http://signalr.net)
- [Funkce SignalR Githubu a ukázky](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

Návod k nasazení aplikace SignalR do Azure najdete v tématu [pomocí SignalR s webovými aplikacemi ve službě Azure App Service](../deployment/using-signalr-with-azure-web-sites.md). Podrobné informace o tom, jak nasadit webový projekt sady Visual Studio na webu Windows Azure naleznete v tématu [vytvoření webové aplikace ASP.NET ve službě Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).
