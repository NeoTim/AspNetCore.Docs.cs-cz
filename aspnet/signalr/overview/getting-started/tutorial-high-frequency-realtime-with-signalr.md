---
uid: signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
title: 'Kurz: Vytvoření aplikace v reálném čase vysokou frekvencí s funkcí SignalR 2 | Dokumentace Microsoftu'
author: bradygaster
description: Tento kurz ukazuje, jak vytvořit webovou aplikaci, která používá knihovnu ASP.NET SignalR k zajištění funkce zasílání zpráv vysokou frekvencí.
ms.author: bradyg
ms.date: 01/22/2019
ms.assetid: 9f969dda-78ea-4329-b1e3-e51c02210a2b
msc.legacyurl: /signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 44aaa2b0c059de310e963f642fa56c2f00a7e443
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836724"
---
# <a name="tutorial-create-high-frequency-real-time-app-with-signalr-2"></a>Kurz: Vytvoření aplikace v reálném čase vysokou frekvencí s funkcí SignalR 2

Tento kurz ukazuje, jak vytvořit webovou aplikaci, která používá ASP.NET SignalR 2 zasílání zpráv nakonfigurovánu vysokou frekvencí. V tomto případě "vysokou frekvencí zasílání zpráv" znamená, že server odešle aktualizace s pevnou sazbou. Můžete odeslat až 10 zpráv za sekundu.

Vytvářené aplikace zobrazí obrazec, který můžete přetáhnout uživatelů. Na serveru aktualizuje umístění obrazce ve všech propojených prohlížečů tak, aby odpovídala pozici Přetahované tvaru použitím vypršel časový limit aktualizace.

Koncepty představenými v tomto kurzu jste aplikací v reálném čase hry a další aplikace simulace.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení projektu
> * Vytvoření základní aplikace
> * Mapovat k centru při spuštění aplikace
> * Přidat klienta
> * Spuštění aplikace
> * Přidat cyklus klienta
> * Přidat cyklus serveru
> * Přidat plynulou animaci

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a>Požadavky

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) s **vývoj pro ASP.NET a web** pracovního vytížení.

## <a name="set-up-the-project"></a>Nastavení projektu

V této části vytvoříte projekt v sadě Visual Studio 2017.

Tato část ukazuje, jak pomocí sady Visual Studio 2017 vytvoří prázdná webová aplikace ASP.NET a přidejte knihovny SignalR a jQuery.UI.

1. V sadě Visual Studio vytvořte webovou aplikaci ASP.NET.

    ![Vytvořte web](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

1. V **nová webová aplikace ASP.NET - MoveShapeDemo** okně, ponechejte tuto položku **prázdný** vybraný a vyberte **OK**.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**.

1. V **přidat novou položku - MoveShapeDemo**vyberte **nainstalováno** > **Visual C#**   >  **webové**  >  **SignalR** a pak vyberte **třída rozbočovače SignalR (v2)**.

1. Název třídy *MoveShapeHub* a přidejte ho do projektu.

    Tento krok vytvoří *MoveShapeHub.cs* souboru třídy. Současně přidá sadu souborů skriptů a odkazy na sestavení, podporující funkci SignalR k projektu.

1. Vyberte **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.

1. V **Konzola správce balíčků**, spusťte tento příkaz:

    ```console
    Install-Package jQuery.UI.Combined
    ```

    Tento příkaz nainstaluje knihovny uživatelského rozhraní jQuery. Použít pro animaci tvaru.

1. V **Průzkumníka řešení**, rozbalte uzel skripty.

    ![Odkazy na knihovnu skriptu](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)

    Knihovny skriptů pro funkci SignalR, jQuery a jQueryUI jsou viditelné v projektu.

## <a name="create-the-base-application"></a>Vytvoření základní aplikace

V této části vytvoříte aplikace prohlížeče. Aplikace odešle umístění obrazce na server během každou událost pohybu myší. Server vysílá tyto informace do dalších připojených klientů v reálném čase. Další informace o této aplikaci v předchozích částech.

1. Otevřít *MoveShapeHub.cs* souboru.

1. Nahraďte kód v *MoveShapeHub.cs* soubor s tímto kódem:

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.cs)]

1. Uložte soubor.

`MoveShapeHub` Třída je implementace rozbočovače SignalR. Stejně jako [Začínáme s knihovnou SignalR](tutorial-getting-started-with-signalr.md) kurzu centra má metodu, která klientům volat přímo. V tomto případě klient zasílá objektu s novou X a Y souřadnic tvar, který má na serveru. Všechny ostatní připojeným klientům získat vysílali Tyhle souřadnice. Funkce SignalR automaticky serializuje tento objekt pomocí formátu JSON.

Aplikace odešle `ShapeModel` objektu do klienta. Obsahuje členy k uložení umístění obrazce. Verze objektu na serveru má také členy, aby mohli sledovat kterého klienta data ukládají. Tento objekt brání serveru z odesílání dat klientovi sám na sebe. Používá tento člen `JsonIgnore` atribut, aby byla aplikace z serializace dat a jejich odesláním zpět do klienta.

## <a name="map-to-the-hub-when-app-starts"></a>Mapovat k centru při spuštění aplikace

V dalším kroku můžete nastavit mapování k centru při spuštění aplikace. Přidání třídy pro spuštění OWIN SignalR 2, vytvoří mapování.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**.

1. V **přidat novou položku - MoveShapeDemo** vyberte **nainstalováno** > **Visual C#**   >  **webové** a pak Vyberte **třídy pro spuštění OWIN**.

1. Název třídy *spuštění* a vyberte **OK**.

1. Nahraďte kód v *Startup.cs* soubor s tímto kódem:

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.cs)]

Volání OWIN při spuštění třídy `MapSignalR` kdy aplikace spouští `Configuration` metody. Aplikace přidá třídu pro spuštění OWIN pro zpracování pomocí `OwinStartup` atribut sestavení.

## <a name="add-the-client"></a>Přidat klienta

Přidáte stránku HTML pro klienta.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **stránku HTML**.

1. Pojmenujte stránku **výchozí** a vyberte **OK**.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na *Default.html* a vyberte **nastavit jako úvodní stránku**.

1. Nahraďte kód v *Default.html* soubor s tímto kódem:

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.html?highlight=14-16)]

1. V **Průzkumníka řešení**, rozbalte **skripty**.

    Jsou viditelné v projektu knihovny skriptů pro knihovny jQuery a SignalR.

    > [!IMPORTANT]
    > Správce balíčků nainstaluje novější verzi skriptů SignalR.

1. Aktualizujte odkazy na skript v bloku kódu tak, aby odpovídala verzi skriptů soubory v projektu.

Tento kód HTML a JavaScript vytvoří červený `div` volá `shape`. Přetahování chování tvaru pomocí knihovny jQuery a používá `drag` události a umístění obrazce odeslat na server.

## <a name="run-the-app"></a>Spuštění aplikace

Aplikaci můžete spustit na se'e fungovat. Při přetažení tvar kolem okno prohlížeče, přesune se v jiných prohlížečích příliš.

1. Na panelu nástrojů, zapněte **ladění skriptů** a pak vyberte tlačítko Přehrát akci spustíte aplikaci v režimu ladění.

    ![Snímek obrazovky uživatele, zapnutí režimu ladění a vyberete play.](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)

    Otevře se okno prohlížeče s červenou tvar v pravém horním rohu.

1. Zkopírujte adresu URL stránky.

1. Otevřete jiný prohlížeč a vložte adresu URL do adresního řádku.

1. Přetáhněte obrazec v jednom z okna prohlížeče. Následuje tvar v druhém okně prohlížeče.

Při použití funkce touto metodou, není doporučenou programovací model. Neexistuje žádná horní limit počtu získání odeslaných zpráv. V důsledku toho klienty a serverem získat zahlcen zprávy a snižuje výkon. Také aplikace zobrazí odděleném animací na straně klienta. Tato přehrávat nepravidelně animace se stane, protože tvar přesune okamžitě každé metody. Je lepší, pokud tvar proběhnout do každého nového umístění. V dalším kroku se dozvíte, jak tyto problémy napravit.

## <a name="add-the-client-loop"></a>Přidat cyklus klienta

Odesílání umístění tvar na každou událost pohybu myší vytvoří zbytečné objemu síťových přenosů. Aplikace je potřeba omezit zprávy z klienta.

Použití jazyka javascript `setInterval` funkce nastavit smyčku, která odešle nové informace o umístění na server s pevnou sazbou. Smyčka je základní reprezentace "herní cyklus." Je opakovaně volaná funkce, která řídí všechny funkce, které jsou součástí hru.

1. Nahraďte kód klienta v *Default.html* soubor s tímto kódem:

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.html?highlight=14-16)]

    > [!IMPORTANT]
    > Je nutné nahradit odkazy na skript znovu. Musí se shodovat verze skriptů v projektu.

    Tento nový kód přidá `updateServerModel` funkce. Je volána na frekvenci dlouhodobého. Funkce odešle data umístění serveru vždy, když `moved` příznak určuje, že je nová umístění dat k odeslání.

1. Vyberte tlačítko Přehrát a spusťte tak aplikaci

1. Zkopírujte adresu URL stránky.

1. Otevřete jiný prohlížeč a vložte adresu URL do adresního řádku.

1. Přetáhněte obrazec v jednom z okna prohlížeče. Následuje tvar v druhém okně prohlížeče.

Protože aplikace omezuje počet zpráv, které odesílání na server, se nezobrazí jako plynulé animace se nespustil na první.

## <a name="add-the-server-loop"></a>Přidat cyklus serveru

V aktuální aplikaci zprávy odeslané ze serveru klientovi chodit přímo tak často, jak jste dostali. Tento provoz sítě představuje problém podobné, jak vidíme na straně klienta.

Aplikace může odesílat zprávy častěji, než je budete potřebovat. Díky tomu můžou stát zahlcenou připojení. Tato část popisuje postup aktualizace serveru přidat časovač, který omezuje počet odchozích zpráv.

1. Nahraďte obsah `MoveShapeHub.cs` s tímto kódem:

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

1. Vyberte tlačítko Přehrát a spusťte tak aplikaci.

1. Zkopírujte adresu URL stránky.

1. Otevřete jiný prohlížeč a vložte adresu URL do adresního řádku.

1. Přetáhněte obrazec v jednom z okna prohlížeče.

Tento kód rozšiřuje klienta k přidání `Broadcaster` třídy. Nová třída omezuje odchozí zprávy pomocí `Timer` třídy z rozhraní .NET framework.

Je dobré se dozvíte, že Centrum samotného je přechodné. Vytvoří se pokaždé, když je to potřeba. Proto vytvoří aplikaci `Broadcaster` jako typ singleton. Opožděná inicializace využívá k odložení `Broadcaster`pro vytváření, dokud nebude potřeba. To zaručuje, že aplikace vytvoří první instanci rozbočovače zcela před zahájením časovač.

Volání klientů `UpdateShape` funkce je poté přesunut mimo centra `UpdateModel` metody. Už je volána ihned pokaždé, když aplikace přijme příchozí zprávy. Místo toho aplikace odesílá zprávy klientům ve výši 25 volání za sekundu. Spravuje proces `_broadcastLoop` časovač v rámci `Broadcaster` třídy.

A konečně, namísto volání metody klienta z centra přímo, `Broadcaster` třídy je potřeba získat odkaz na aktuálně spuštěné `_hubContext` rozbočovače. Získá odkaz se `GlobalHost`.

## <a name="add-smooth-animation"></a>Přidat plynulou animaci

Aplikace je téměř dokončena, ale jsme dokonce vytvářet jeden další vylepšení. Aplikace přesune tvar na straně klienta v reakci na zprávu serveru. Namísto nastavení pozice tvar do nového umístění uvedena v každém serveru, použijte uživatelské rozhraní JQuery knihovnu `animate` funkce. To můžete tvar přesunout plynule mezi jeho aktuální a nové pozice.

1. Také aktualizovat klienta sady `updateShape` metodu *Default.html* souboru, aby vypadala jako zvýrazněný kód:

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.html?highlight=33-40)]

1. Vyberte tlačítko Přehrát a spusťte tak aplikaci.

1. Zkopírujte adresu URL stránky.

1. Otevřete jiný prohlížeč a vložte adresu URL do adresního řádku.

1. Přetáhněte obrazec v jednom z okna prohlížeče.

Pohyb tvaru v druhém okně zobrazí méně přehrávat nepravidelně. Aplikace argument pohyb interpolaci přes čas Místo nastavování jednou za příchozí zprávy.

Tento kód přesune tvaru z původního umístění do nové. Server poskytuje pozici obrazce v průběhu intervalu animace. V tomto případě to je 100 ms. Aplikace vymaže všechny předchozí animace spuštěna s tvarem před zahájením nové animace.

## <a name="get-the-code"></a>Získat kód

[Stáhnout dokončený projekt](http://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a)

## <a name="additional-resources"></a>Další zdroje

Právě jste se dozvěděli o paradigma komunikace je užitečné pro vývoj online hry a další simulace, jako je třeba [ShootR hru vytvořili s knihovnou SignalR](https://shootr.azurewebsites.net/).

Další informace o funkci SignalR naleznete v následujících zdrojích:

* [Projekt SignalR](http://signalr.net)

* [Funkce SignalR Githubu a ukázky](https://github.com/SignalR/SignalR)

* [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení projektu
> * Vytvoření základní aplikace
> * Mapovat na rozbočovači při spuštění aplikace
> * Přidání klienta
> * Spuštění aplikace
> * Přidání smyčky klienta
> * Přidat server smyčky
> * Přidání plynulou animaci

Přejděte k dalším článku se dozvíte, jak vytvořit webovou aplikaci, která používá ASP.NET SignalR 2 pro zajištění všesměrového vysílání funkce serveru.
> [!div class="nextstepaction"]
> [Funkce SignalR 2 a vysílání serveru](tutorial-server-broadcast-with-signalr.md)