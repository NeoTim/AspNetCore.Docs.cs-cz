---
uid: signalr/overview/getting-started/tutorial-server-broadcast-with-signalr
title: 'Kurz: Serverové vysílání s knihovnou SignalR 2 | Dokumentace Microsoftu'
author: tdykstra
description: Tento kurz ukazuje, jak vytvořit webovou aplikaci, která používá ASP.NET SignalR 2 pro zajištění všesměrového vysílání funkce serveru.
ms.author: riande
ms.date: 01/02/2019
ms.topic: tutorial
ms.assetid: 1568247f-60b5-4eca-96e0-e661fbb2b273
msc.legacyurl: /signalr/overview/getting-started/tutorial-server-broadcast-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: a6014e604613492db91b2dc6f846c3c73d938d99
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099296"
---
# <a name="tutorial-server-broadcast-with-signalr-2"></a>Kurz: Server vysílání s knihovnou SignalR 2

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

Tento kurz ukazuje, jak vytvořit webovou aplikaci, která používá ASP.NET SignalR 2 pro zajištění všesměrového vysílání funkce serveru. Server vysílání znamená, že je server spuštěn komunikace klientů.

Aplikace, kterou vytvoříte v tomto kurzu simuluje akciích Typický scénář pro vysílání funkce serveru. Server pravidelně, náhodně aktualizuje ceny akcií a vysílat aktualizace na všechny připojené klienty. V prohlížeči, čísla a symboly **změnit** a **%** dynamicky měnit sloupce v reakci na oznámení ze serveru. Pokud otevřete další prohlížeče pro stejnou adresu URL, všechny zobrazit stejná data a stejné změny dat současně.

![Vytvořte web](tutorial-server-broadcast-with-signalr/_static/image1.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření projektu
> * Nastavte si do kódu serveru
> * Zkoumání kódu serveru
> * Nastavit kód klienta
> * Zkoumání kódu klienta
> * Testování aplikace
> * Povolit protokolování

> [!IMPORTANT]
> Pokud nechcete, aby pro seznámení se základními kroky při vytváření aplikace, můžete nainstalovat balíček SignalR.Sample v novém projektu prázdná webová aplikace ASP.NET. Pokud balíček NuGet nainstalovat bez provedení kroků v tomto kurzu, postupujte podle pokynů v *readme.txt* souboru. Ke spuštění balíčku je třeba přidat OWIN startup třídy, který volá `ConfigureSignalR` metoda ve nainstalovaným balíčkem. Pokud nemůžete přidat třídu pro spuštění OWIN, dojde k chybě. Zobrazit [nainstalovat vzorovou StockTicker](#install-the-stockticker-sample) části tohoto článku.


## <a name="prerequisites"></a>Požadavky

 * [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) s **vývoj pro ASP.NET a web** pracovního vytížení.

## <a name="create-the-project"></a>Vytvoření projektu

Tato část ukazuje, jak vytvořit prázdná webová aplikace ASP.NET pomocí sady Visual Studio 2017.

1. V sadě Visual Studio vytvořte webovou aplikaci ASP.NET.

    ![Vytvořte web](tutorial-server-broadcast-with-signalr/_static/image2.png)

1. V **nová webová aplikace ASP.NET - SignalR.StockTicker** okně, ponechejte tuto položku **prázdný** vybraný a vyberte **OK**.

## <a name="set-up-the-server-code"></a>Nastavte si do kódu serveru

V této části můžete nastavit kód, který běží na serveru.

### <a name="create-the-stock-class"></a>Vytvořte třídu akcií

Začněte vytvořením *akcie* model třídy, které budete používat k ukládání a přenášení informací o stejných akcií.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **třídy**.

1. Název třídy *akcie* a přidejte ho do projektu.

1. Nahraďte kód v *Stock.cs* soubor s tímto kódem:

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample1.cs)]

    Jsou dvě vlastnosti, které nastavíte při vytváření akcie `Symbol` (například MSFT pro Microsoft) a `Price`. Ostatní vlastnosti závisí na kdy a jak nastavit `Price`. Při prvním nastavíte `Price`, získá hodnotu rozšíří na `DayOpen`. Poté, pokud nastavíte `Price`, vypočítá aplikace `Change` a `PercentChange` hodnoty vlastností podle rozdíl mezi `Price` a `DayOpen`.

### <a name="create-the-stocktickerhub-and-stockticker-classes"></a>Vytvoření tříd StockTickerHub a StockTicker

Rozhraní API pro rozbočovače SignalR budete používat ke zpracování server klient interakce. A `StockTickerHub` třídu odvozenou od `SignalRHub` třídy bude zpracovávat připojení a volání metody přijímají od klientů. Také je potřeba udržovat uložených dat a spustit `Timer` objektu. `Timer` Objektu bude pravidelně aktivovat aktualizaci cen nezávisle na připojení klientů. Tyto funkce nejde umístit `Hub` třídy, protože jsou přechodné rozbočovače. Vytvoří aplikaci `Hub` instance třídy pro každý úkol v rozbočovači, jako je připojení a volání od klienta k serveru. Mechanismus, který zajišťuje uložených dat, aktualizuje ceny a vysílá aktualizaci cen má ke spuštění v samostatné třídě. Zobrazí název třídy `StockTicker`.

![Všesměrové vysílání z StockTicker](tutorial-server-broadcast-with-signalr/_static/image3.png)

Chcete jenom jednu instanci `StockTicker` má třída spustit na serveru, takže budete muset nastavit odkaz z každého `StockTickerHub` instanci typu singleton `StockTicker` instance. `StockTicker` Třída má vysílat klientům, protože má uložených dat a aktivuje aktualizace, ale `StockTicker` není `Hub` třídy. `StockTicker` Třída má k získání odkazu na objekt kontextu připojení rozbočovače SignalR. Pak můžete objekt context připojení SignalR na klienty.

#### <a name="create-stocktickerhubcs"></a>Vytvoření StockTickerHub.cs

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**.

1. V **přidat novou položku - SignalR.StockTicker**vyberte **nainstalováno** > **Visual C#**   >  **webové**  >  **SignalR** a pak vyberte **třída rozbočovače SignalR (v2)**.

1. Název třídy *StockTickerHub* a přidejte ho do projektu.

    Tento krok vytvoří *StockTickerHub.cs* souboru třídy. Současně přidá sadu souborů skriptů a odkazy na sestavení podporující funkci SignalR k projektu.

1. Nahraďte kód v *StockTickerHub.cs* soubor s tímto kódem:

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample2.cs)]

1. Uložte soubor.

Tato aplikace používá [centra](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx) třída definuje metody, které klienty můžete volat na serveru. Definujete jednu metodu: `GetAllStocks()`. Když klient se nejprve připojí k serveru, bude volat tuto metodu za účelem získání seznamu všech zásob s jejich aktuální ceny. Můžete spustit synchronně a vrátí metodu `IEnumerable<Stock>` vzhledem k tomu, že ji vrací data z paměti.

Pokud má metodu k získání dat tímto způsobem něco, co by vyžadovalo čekání, jako je vyhledávání v databázi nebo volání webové služby, zadali byste `Task<IEnumerable<Stock>>` jako návratovou hodnotu k povolení asynchronního zpracování. Další informace najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR - Server - kdy spustit asynchronně](../guide-to-the-api/hubs-api-guide-server.md#asyncmethods).

`HubName` Atribut určuje, jak aplikace bude odkazovat centra v kódu jazyka JavaScript na straně klienta. Výchozí jméno v klientovi, pokud nepoužijete tento atribut je verze camelCase název třídy, kterou v tomto případě by `stockTickerHub`.

Jak uvidíte později při vytváření `StockTicker` třídy, aplikace vytvoří instanci typu singleton této třídy v jeho statické `Instance` vlastnost. Tuto instanci typu singleton `StockTicker` se v paměti bez ohledu na to, kolik klientů připojit nebo odpojit. Co je instance `GetAllStocks()` metoda používá k vrácení aktuální uložených informací.

#### <a name="create-stocktickercs"></a>Vytvoření StockTicker.cs

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **třídy**.

1. Název třídy *StockTicker* a přidejte ho do projektu.

1. Nahraďte kód v *StockTicker.cs* soubor s tímto kódem:

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample3.cs)]

Vzhledem k tomu, že všechna vlákna budou používat stejnou instanci StockTicker kódu, StockTicker třídy musí být bezpečné pro vlákna.

### <a name="examine-the-server-code"></a>Zkoumání kódu serveru

Když si zblízka do kódu serveru, pomůže vám pochopit, jak aplikace funguje.

#### <a name="storing-the-singleton-instance-in-a-static-field"></a>Instanci typu singleton ukládání do statických polí

Kód inicializuje statické `_instance` pole, která zálohuje `Instance` vlastnost s instancí třídy. Protože konstruktor není soukromý, je pouze instance třídy, která může vytvářet aplikace. Tato aplikace používá [opožděné inicializace](/dotnet/framework/performance/lazy-initialization) pro `_instance` pole. Není z důvodů výkonu. Je zajistit, aby že vytvoření instance je bezpečná pro vlákno.

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample4.cs)]

Pokaždé, když klient připojí k serveru, novou instanci třídy StockTickerHub spuštěna v samostatném vlákně získá instanci typu singleton StockTicker z `StockTicker.Instance` statické vlastnosti, jako jste viděli v `StockTickerHub` třídy.

#### <a name="storing-stock-data-in-a-concurrentdictionary"></a>Ukládání dat uložených v ConcurrentDictionary

Konstruktor inicializuje `_stocks` kolekce s ukázkovými daty zásob, a `GetAllStocks` vrátí zásob. Jak jste viděli již dříve, je vrácena této kolekce akcie `StockTickerHub.GetAllStocks`, což je metoda serveru v `Hub` třídu, která může volat klientů.

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample5.cs)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample6.cs)]

Kolekce akcie je definován jako [ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx) typ pro bezpečný přístup z více vláken. Jako alternativu můžete použít [slovníku](https://msdn.microsoft.com/library/xfhwa508.aspx) objektu a explicitně zamknout slovníku, když provedete změny.

V této ukázkové aplikaci je OK k ukládání dat aplikací v paměti a přijít o data, když aplikace uvolní `StockTicker` instance. V reálné aplikaci když pracujete s back endovým datům úložiště, jako jsou databáze.

#### <a name="periodically-updating-stock-prices"></a>Pravidelně aktualizuje cenami akcií

Konstruktor spuštění `Timer` objektu, která pravidelně volá metody, které aktualizují cenami akcií v náhodných intervalech.

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample7.cs)]

 `Timer` volání `UpdateStockPrices`, která předá vynulujte v parametru state. Před aktualizací ceny, aplikace převezme Zámek `_updateStockPricesLock` objektu. Kód zkontroluje, jestli jiné vlákno se už aktualizuje ceny, a pak zavolá `TryUpdateStockPrice` na každé populace v seznamu. `TryUpdateStockPrice` Metoda rozhodne, zda se má změnit akcií a kolik ho změnit. Pokud se změní ceny akcie v aplikaci označuje jako `BroadcastStockPrice` vysílat minimální cenu akcie změny na všechny připojené klienty.

`_updatingStockPrices` Příznak určené [volatile](https://msdn.microsoft.com/library/x13ttww7.aspx) k zkontrolujte, zda je bezpečná pro vlákno.

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample8.cs)]

V reálné aplikaci `TryUpdateStockPrice` by volání metody webové služby k vyhledání cena. V tomto kódu aplikace používá generátor náhodných čísel náhodně provádět změny.

#### <a name="getting-the-signalr-context-so-that-the-stockticker-class-can-broadcast-to-clients"></a>Získávání kontextu SignalR tak, aby třída StockTicker můžete vysílat pro klienty

Protože změny cen pocházet tady `StockTicker` objekt, je objekt, který potřebuje volat `updateStockPrice` metodu na všechny připojené klienty. V `Hub` třídy, máte rozhraní API pro volání metody klienta, ale `StockTicker` není odvozen od `Hub` třídy a nemá odkaz na jakýkoli `Hub` objektu. Na připojení klienti, `StockTicker` třída má pro získání instance kontextu SignalR pro `StockTickerHub` třídy, který budete používat pro volání metod na klientských počítačích.

Kód získá odkaz na kontext SignalR při vytváření instance třídy singleton, předá, které odkazují konstruktor a konstruktor umístí ho `Clients` vlastnost.

Existují dva důvody, proč chcete přijímat pouze jednou kontextu: získání kontextu je nákladné úlohy a jak ji získat, jakmile je, že aplikace zachová zamýšleném pořadí zpráv odeslaných do klientů.

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample9.cs)]

Začínáme `Clients` vlastnost kontextu a umístit ho do `StockTickerClient` vlastnost umožňuje napsat kód pro volání metody klienta, která vypadá stejně, jako by tomu bylo v `Hub` třídy. Například vysílat pro všechny klienty můžete napsat `Clients.All.updateStockPrice(stock)`.

`updateStockPrice` Metodu, která voláte v `BroadcastStockPrice` ještě neexistuje. Přidáte je později při psaní kódu, který běží na straně klienta. Můžete se podívat do `updateStockPrice` zde protože `Clients.All` je dynamická, což znamená, že aplikace vyhodnotí výraz v době běhu. Při volání této metody se spustí, SignalR pošle názvu metody a hodnota parametru klienta, a pokud má klient metodu s názvem `updateStockPrice`, aplikace bude volat tuto metodu a předat hodnotu parametru.

`Clients.All` znamená, že odesílání pro všechny klienty. Funkce SignalR poskytuje další možnosti k určení, které klienti nebo skupiny klientů k odeslání. Další informace najdete v tématu [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx).

### <a name="register-the-signalr-route"></a>Zaregistrujte směrování funkce SignalR

Server musí znát adresu URL, která je pro zachycení a přístupem k systému SignalR. K tomu, přidejte třídu OWIN při spuštění:

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**.

1. V **přidat novou položku - SignalR.StockTicker** vyberte **nainstalováno** > **Visual C#**   >  **webové** a potom vyberte **třídy pro spuštění OWIN**.

1. Název třídy *spuštění* a vyberte **OK**.

1. Nahraďte kód v *Startup.cs* soubor s tímto kódem:

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample10.cs)]

Nyní dokončili jste nastavování do kódu serveru. V další části které nastavíte klienta.

## <a name="set-up-the-client-code"></a>Nastavit kód klienta

V této části můžete nastavit kód, který běží na straně klienta.

### <a name="create-the-html-page-and-javascript-file"></a>Vytvoření stránky HTML a JavaScript souboru

Na stránce HTML se zobrazí data a soubor jazyka JavaScript uspořádá data.

#### <a name="create-stocktickerhtml"></a>Vytvoření StockTicker.html

Nejprve přidejte klienta HTML.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **stránku HTML**.

1. Název souboru *StockTicker* a vyberte **OK**.

1. Nahraďte kód v *StockTicker.html* soubor s tímto kódem:

    [!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample11.html?highlight=40-43)]

    Kód HTML vytvoří tabulku se pět sloupců, řádek záhlaví a řádek dat s jedinou buňku, která překlenuje všechny pět sloupců. Řádek dat se zobrazí "načítání..." okamžité při spuštění aplikace. Kód jazyka JavaScript se odebrat tento řádek a přidat v místě řádky s uložených dat načtených ze serveru.

    Zadejte značky skriptu:

    * Soubor skriptu jQuery.

    * Soubor skriptu základní funkce SignalR.

    * Soubor skriptu proxy služby SignalR.

    * StockTicker soubor skriptu, který později vytvoříte.

    Aplikace dynamicky generuje soubor skriptu proxy služby SignalR. Určuje adresu URL "/ signalr/centra" a definuje metody proxy pro metody u třídy rozbočovače, v takovém případě pro `StockTickerHub.GetAllStocks`. Pokud dáváte přednost, tento soubor JavaScript vygenerovat ručně pomocí [nástroje SignalR](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/). Nezapomeňte zakázat vytváření dynamického souboru `MapHubs` volání metody.

1. V **Průzkumníka řešení**, rozbalte **skripty**.

    Jsou viditelné v projektu knihovny skriptů pro knihovny jQuery a SignalR.

    > [!IMPORTANT]
    > Správce balíčků bude nainstalujte novější verzi skriptů SignalR.

1. Aktualizujte odkazy na skript v bloku kódu tak, aby odpovídala verzi skriptů soubory v projektu.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na *StockTicker.html*a pak vyberte **nastavit jako úvodní stránku**.

#### <a name="create-stocktickerjs"></a>Vytvoření StockTicker.js

Teď vytvořte soubor jazyka JavaScript.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **soubor JavaScript**.

1. Název souboru *StockTicker* a vyberte **OK**.

1. Přidejte tento kód *StockTicker.js* souboru:

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample12.js)]

### <a name="examine-the-client-code"></a>Zkoumání kódu klienta

Když si zblízka klientský kód, pomůže vám zjistěte, jak kód klienta komunikuje s kód serveru tak, aby aplikace funguje.

#### <a name="starting-the-connection"></a>Spouští se připojení

`$.connection` odkazuje na proxy služby SignalR. Kód získá odkaz na proxy serveru pro `StockTickerHub` třídy a umístí jej `ticker` proměnné. Název proxy serveru je název, který nastavil `HubName` atribut:

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample13.js)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample14.cs)]

Po definování proměnné a funkce, poslední řádek kódu v souboru inicializuje připojení SignalR voláním funkce SignalR `start` funkce. `start` Funkce provádí asynchronně a vrací [jQuery odloženo objekt](http://api.jquery.com/category/deferred-object/). Můžete volat funkci Hotovo k určení funkce, která má být volána po dokončení asynchronní akce aplikaci.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample15.js)]

#### <a name="getting-all-the-stocks"></a>Získání všech akcie.

`init` Volání funkce `getAllStocks` funkce na serveru a používá informace, který server vrátí aktualizace základní tabulky. Všimněte si, že ve výchozím nastavení, budete muset použít camelCasing na straně klienta i v případě, že název metody rozlišuje jazyka Pascal – na serveru. Pravidlo camelCasing platí pouze pro metody, ne objekty. Například můžete odkazovat na `stock.Symbol` a `stock.Price`, nikoli `stock.symbol` nebo `stock.price`.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample16.js)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample17.cs)]

V `init` metodu, aplikace vytvoří HTML pro řádek tabulky pro každý skladový objekt přijatou ze serveru voláním `formatStock` formát vlastnosti `stock` objekt a potom volala `supplant` nahradit zástupné symboly v `rowTemplate` proměnnou `stock` hodnot vlastností objektu. Výsledného souboru HTML se pak připojí k základní tabulky.

> [!NOTE]
> Volání `init` ji jako `callback` funkci, která spustí po asynchronní `start` funkce dokončí. Pokud jste volali `init` jako samostatný příkaz jazyka JavaScript po volání `start`, funkce selže, protože by spustit okamžitě bez čekání na spuštění funkce dokončete navazování připojení. V takovém případě `init` funkce by se pokusil zavolat `getAllStocks` funkce předtím, než aplikace naváže připojení k serveru.

#### <a name="getting-updated-stock-prices"></a>Získání aktualizované ceny akcií

Na serveru změní cena akcie společnosti, zavolá `updateStockPrice` na připojené klienty. Aplikace přidá funkce do vlastnosti klienta `stockTicker` proxy, aby byla k dispozici k volání ze serveru.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample18.js)]

`updateStockPrice` Formáty funkce stejně jako v řádku skladový objekt přijatou ze serveru do tabulky `init` funkce. Místo přidávání řádku do tabulky, najde skladě aktuální řádek v tabulce a tento řádek nahradí novým.

## <a name="test-the-application"></a>Testování aplikace

Můžete testovat aplikaci, aby zajistili, že funguje. Zobrazí se vám zobrazit živé základní tabulku s cenami akcií kolísá všechna okna prohlížeče.

1. Na panelu nástrojů, zapněte **ladění skriptů** a pak vyberte tlačítko Přehrát akci spustíte aplikaci v režimu ladění.

    ![Snímek obrazovky uživatele, zapnutí režimu ladění a vyberete play.](tutorial-server-broadcast-with-signalr/_static/image4.png)

    Otevře se okno prohlížeče, zobrazí **Live tabulky akcie**. Základní tabulky zpočátku ukazuje řádku "..." načítání"poté, po krátkou dobu, aplikace bude zobrazovat úvodní uložených dat a spusťte ceny akcií, chcete-li změnit.

1. Zkopírujte adresu URL z prohlížeče, otevřete dvou dalších prohlížečích a adresy URL vložte do řádku s adresou.

    Počáteční uložených zobrazení je stejný jako první prohlížeče a změny probíhají souběžně.

1. Zavřete všechny prohlížeče, otevřete nový prohlížeč a přejděte na stejnou adresu URL.

    Objekt typu singleton StockTicker pokračování ke spuštění na serveru. **Live tabulky akcie** odhalí populací zlepšovalo, chcete-li změnit. Nevidíte počáteční tabulka s nulou změnit hodnoty.

1. Zavřete prohlížeč.

## <a name="enable-logging"></a>Povolit protokolování

SignalR má vestavěné protokolování funkci, kterou můžete povolit na straně klienta na podporu při řešení potíží. V této části povolte protokolování a podívejte se na příklady, které ukazují, jak protokoly, že jste které z následujících metod přenosu pomocí funkce SignalR:

* [Protokoly Websocket](http://en.wikipedia.org/wiki/WebSocket)podporovaný IIS 8 a aktuální prohlížeče.

* [Události odeslané serverem](http://en.wikipedia.org/wiki/Server-sent_events)podporovaný prohlížečích než Internet Explorer.

* [Navždy rámec](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe)podporovaný aplikace Internet Explorer.

* [Dlouhý interval dotazování AJAX](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling)podporovaná ve všech prohlížečích.

Pro jakékoli dané připojení SignalR vybere nejlepší metody přenosu, které podporují server i klient.

1. Otevřít *StockTicker.js*.

1. Přidejte následující zvýrazněný řádek kód pro povolení protokolování bezprostředně před kódem, který inicializuje připojení na konci souboru:

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample19.js?highlight=2)]

1. Stisknutím klávesy **F5** spusťte projekt.

1. Otevřete okno vývojářských nástrojů v prohlížeči a vyberte v konzole naleznete v protokolech. Budete muset aktualizovat stránku, aby zobrazil protokoly vyjednávání přepravy pro nové připojení SignalR.

    * Pokud používáte Internet Explorer 10 v systému Windows 8 (IIS 8), je způsob přepravy **objekty Websocket**.

    * Pokud používáte Internet Explorer 10 na Windows 7 (službu IIS 7.5), je způsob přepravy **iframe**.

    * Pokud používáte Firefox 19 ve Windows 8 (IIS 8), je způsob přepravy **objekty Websocket**.

        > [!TIP]
        > V aplikaci Firefox nainstalujte doplněk Firebug okno konzoly.

    * Pokud používáte Firefox 19 ve Windows 7 (službu IIS 7.5), je způsob přepravy **server odeslal** události.

## <a name="install-the-stockticker-sample"></a>Nainstalovat vzorovou StockTicker

[Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) nainstaluje aplikaci StockTicker. Balíček NuGet obsahuje víc funkcí než zjednodušenou verzi, kterou jste vytvořili úplně od začátku. V této části kurzu nainstalujte balíček NuGet a projděte si nové funkce a kód, který je implementuje.

> [!IMPORTANT]
> Pokud instalace balíčku bez provedení předchozích kroků v tomto kurzu je nutné přidat do vašeho projektu třídy pro spuštění OWIN. Tento soubor readme.txt pro balíček NuGet popisuje tento krok.

### <a name="install-the-signalrsample-nuget-package"></a>Nainstalujte balíček SignalR.Sample NuGet

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**.

1. V **Správce balíčků NuGet: SignalR.StockTicker**vyberte **Procházet**.

1. Z **zdroj balíčku**vyberte **nuget.org**.

1. Zadejte *SignalR.Sample* vyhledávacího pole a vyberte **Microsoft.AspNet.SignalR.Sample** > **nainstalovat**.

1. V **Průzkumníka řešení**, rozbalte *SignalR.Sample* složky.

    Instaluje se balíček SignalR.Sample vytvořena složka a její obsah.

1. V *SignalR.Sample* složky, klikněte pravým tlačítkem na *StockTicker.html*a pak vyberte **nastavit jako úvodní stránku**.

    > [!NOTE]
    > Instalace SignalR.Sample NuGet balíčku může změnit verzi jQuery, který máte v vaše *skripty* složky. Nové *StockTicker.html* soubor, který se nainstaluje balíček *SignalR.Sample* složkou je synchronizovaný s verzí jQuery, který nainstaluje balíček, ale pokud budete chtít spustit váš původním *StockTicker.html* soubor znovu, možná budete muset nejprve aktualizovat odkaz jQuery ve značce skriptu.

### <a name="run-the-application"></a>Spuštění aplikace

 V tabulce, které jste viděli v první aplikace měla užitečných funkcí. Úplné akciích aplikace zobrazuje nové funkce: vodorovně posuvné okno, které zobrazuje uložených dat a zásob, které Změna barvy tak, jak zvýšit a spadají.

1. Stisknutím klávesy **F5** ke spuštění aplikace.

     Při prvním spuštění aplikace, "trhu" je "uzavřený" a uvidíte statickou tabulku a akcie okno, které není posouvání.

1. Vyberte **otevřeném trhu**.

    ![Snímek obrazovky živé akcie.](tutorial-server-broadcast-with-signalr/_static/image5.png)

    * **Live akcie akcie** pole začne posouvat vodorovně a spustí se server pravidelně vysílat změny ceny akcie v náhodných intervalech.

    * Pokaždé, když změny cen akcií, aktualizuje aplikace i **Live tabulky akcie** a **Live akcie akcie**.

    * Změna ceny akcie společnosti po kladná, aplikace bude zobrazovat akcie zeleným pozadím.

    * Při změně je záporný, aplikace bude zobrazovat akcie s červeným pozadím.

1. Vyberte **zavřete trhu**.

    * V tabulce aktualizuje stop.

    * Ukazateli zastaví posouvání.

1. Vyberte **resetování**.

    * Všechny uložených dat se vynuluje.

    * Aplikace obnovení původního stavu před změnami ceny začít.

1. Zkopírujte adresu URL z prohlížeče, otevřete dvou dalších prohlížečích a adresy URL vložte do řádku s adresou.

1. Můžete zobrazit stejná data dynamicky aktualizovat ve stejnou dobu v každým prohlížečem.

1. Když vyberete některý z ovládacích prvků, všechny prohlížeče odpoví stejným způsobem jako ve stejnou dobu.

### <a name="live-stock-ticker-display"></a>Živé akcie časovače, může zobrazení

**Live akcie akcie** zobrazení je neuspořádaný seznam v `<div>` element formátované jako jeden řádek stylů CSS. Aplikace inicializuje a aktualizuje ukazateli stejným způsobem jako v tabulce: tak, že nahradíte zástupný text v `<li>` řetězec šablony a dynamicky přidat `<li>` prvků, které mají `<ul>` elementu. Tato aplikace obsahuje posouvání pomocí jQuery `animate` funkce lišit v rozpětí vlevo neuspořádaný seznam v rámci `<div>`.

#### <a name="signalrsample-stocktickerhtml"></a>SignalR.Sample StockTicker.html

Akciích kód HTML:

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample20.html)]

#### <a name="signalrsample-stocktickercss"></a>SignalR.Sample StockTicker.css

Akciích kód šablony stylů CSS:

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample21.html)]

#### <a name="signalrsample-signalrstocktickerjs"></a>SignalR.Sample SignalR.StockTicker.js

Posuňte se kód jazyka jQuery, která umožňuje:

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample22.js)]

### <a name="additional-methods-on-the-server-that-the-client-can-call"></a>Další metody na serveru, na které můžete volat klienta

Ke zvýšení flexibility aplikace, existují další metody, které může aplikace provést volání metody.

#### <a name="signalrsample-stocktickerhubcs"></a>SignalR.Sample StockTickerHub.cs

`StockTickerHub` Třída definuje čtyři další metody, které můžete volat klienta:

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample23.cs)]

Volání aplikace `OpenMarket`, `CloseMarket`, a `Reset` v reakci na tlačítka v horní části stránky. Vysvětlují vzor jednoho klienta, spouštění změnu stavu okamžitě rozšíří na všechny klienty. Každá z těchto metod volá metodu v `StockTicker` třídu, která způsobí, že změnu stavu na trhu a potom vysílá nový stav.

#### <a name="signalrsample-stocktickercs"></a>SignalR.Sample StockTicker.cs

V `StockTicker` třídy aplikace udržuje stav na trh `MarketState` vlastnosti, která vrací `MarketState` hodnota výčtu:

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample24.cs)]

Každá z metod, které ke změně stavu na trhu provést uvnitř bloku zámek protože `StockTicker` třída musí být bezpečné pro vlákna:

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample25.cs)]

Abyste měli jistotu, tento kód je bezpečná pro vlákno, `_marketState` pole, která zálohuje `MarketState` vlastnosti určené `volatile`:

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample26.cs)]

`BroadcastMarketStateChange` a `BroadcastMarketReset` metody jsou podobné BroadcastStockPrice metody, které jste už viděli, s výjimkou volání různých metod definovaných v klientském počítači:

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample27.cs)]

### <a name="additional-functions-on-the-client-that-the-server-can-call"></a>Další funkce na straně klienta, která může volat na serveru

`updateStockPrice` Funkce nyní zpracovává tabulky a zobrazení akcie a používá `jQuery.Color` na flash červenou a zelenou barvy.

Nové funkce v *SignalR.StockTicker.js* povolení a zakázání tlačítka na základě stavu na trhu. Jsou také zastavit nebo spustit **Live akcie akcie** vodorovného posouvání. Protože mnoho funkcí se přidávají do `ticker.client`, tato aplikace používá [jQuery rozšířit funkce](http://api.jquery.com/jQuery.extend/) je přidat.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample28.js)]

### <a name="additional-client-setup-after-establishing-the-connection"></a>Instalace dalších klientských po navázání připojení

Jakmile klient naváže připojení, má provést další úkony:

* Zjistěte, jestli je na trhu otevřeno nebo zavřeno volat odpovídající `marketOpened` nebo `marketClosed` funkce.

* Připojte server volání metod pro tlačítka.

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample29.js)]

Metody serveru nejsou svázanou tlačítka až po aplikace vytváří připojení. Jedná se proto kód nelze volat metody serveru, než jsou k dispozici.

## <a name="additional-resources"></a>Další zdroje

V tomto kurzu jste zjistili, jak programovat aplikace SignalR, který vysílá zprávy ze serveru na všechny připojené klienty. Nyní můžete vysílání zpráv v pravidelných intervalech a v reakci na oznámení z jakéhokoli klienta. Koncept instanci typu singleton vícevláknové můžete použít k údržbě stavu serveru v režimu online her scénáře více hráčů. Příklad najdete v tématu [ShootR hru založenou na oblíbeném SignalR](https://github.com/NTaylorMullen/ShootR).

Kurzy, které ukazují scénáře komunikace peer-to-peer, naleznete v tématu [Začínáme s knihovnou SignalR](introduction-to-signalr.md) a [aktualizace v reálném čase s knihovnou SignalR](tutorial-high-frequency-realtime-with-signalr.md).

Další informace o funkci SignalR naleznete v následujících zdrojích:

* [Funkce SignalR technologie ASP.NET](../../index.md)
* [Projekt SignalR](http://signalr.net/)
* [Funkce SignalR Githubu a ukázky](https://github.com/SignalR/SignalR)
* [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření projektu
> * Nastavte si do kódu serveru
> * Prozkoumat kód serveru
> * Nastavit kód klienta
> * Prozkoumat kód klienta
> * Otestování aplikace
> * Povolené protokolování

Přejděte k dalším článku se naučíte, jak vytvořit aplikaci webu v reálném čase, která používá ASP.NET SignalR 2.
> [!div class="nextstepaction"]
> [Vytvořte aplikaci webu v reálném čase s knihovnou SignalR](real-time-web-applications-with-signalr.md)