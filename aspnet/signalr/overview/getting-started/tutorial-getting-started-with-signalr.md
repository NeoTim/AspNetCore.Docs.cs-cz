---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr
title: 'Kurz: Chatování v reálném čase s knihovnou SignalR 2 | Dokumentace Microsoftu'
author: pfletcher
description: V tomto kurzu se naučíte používat funkci SignalR k vytvoření aplikace pro chatování v reálném čase. Přidáte do prázdná webová aplikace ASP.NET SignalR.
ms.author: riande
ms.date: 01/02/2019
ms.assetid: a8b3b778-f009-4369-85c7-e90f9878d8b4
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: aa015abc47bb2450e04e167c0404aaa1d119ba2c
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098621"
---
# <a name="tutorial-real-time-chat-with-signalr-2"></a>Kurz: Chatování v reálném čase s knihovnou SignalR 2

V tomto kurzu se dozvíte, jak používat funkci SignalR k vytvoření aplikace pro chatování v reálném čase. Přidat prázdnou webovou aplikaci ASP.NET SignalR a vytvořte stránku HTML k odeslání a zobrazení zprávy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení projektu
> * Spuštění ukázky
> * Prozkoumejte kód

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a>Požadavky

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) s **vývoj pro ASP.NET a web** pracovního vytížení.

## <a name="set-up-the-project"></a>Nastavení projektu

Tato část ukazuje, jak pomocí sady Visual Studio 2017 a knihovnou SignalR 2 můžete vytvořit prázdnou webovou aplikaci ASP.NET, přidejte SignalR a vytvořit chatovací aplikaci.

1. V sadě Visual Studio vytvořte webovou aplikaci ASP.NET.

    ![Vytvořte web](tutorial-getting-started-with-signalr/_static/image2.png)

1. V **nový projekt ASP.NET – SignalRChat** okně, ponechejte tuto položku **prázdný** vybraný a vyberte **OK**.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**.

1. V **přidat novou položku - SignalRChat**vyberte **nainstalováno** > **Visual C#**   >  **webové**  >  **SignalR** a pak vyberte **třída rozbočovače SignalR (v2)**.

1. Název třídy *ChatHub* a přidejte ho do projektu.

    Tento krok vytvoří *ChatHub.cs* třídy soubor a přidá sadu souborů skriptů a odkazy na sestavení, podporující funkci SignalR k projektu.

1. Nahraďte kód v novém *ChatHub.cs* soubor třídy s tímto kódem:

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**.

1. V **přidat novou položku - SignalRChat** vyberte **nainstalováno** > **Visual C#**   >  **webové** a pak Vyberte **třídy pro spuštění OWIN**.

1. Název třídy *spuštění* a přidejte ho do projektu.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **stránku HTML**.

1. Zadejte název nové stránky *index* a vyberte **OK**.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na stránku HTML, který jste vytvořili a vyberte **nastavit jako úvodní stránku**.

1. Nahraďte kód výchozí stránku HTML s tímto kódem:

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample3.html)]

1. V **Průzkumníka řešení**, rozbalte **skripty**.

    Jsou viditelné v projektu knihovny skriptů pro knihovny jQuery a SignalR.

    > [!IMPORTANT]
    > Správce balíčků nainstalovanou novější verzi skriptů SignalR.

1. Zkontrolujte, jestli odkazy na skript v bloku kódu, které odpovídají verze souborů skript v projektu.

    Odkazy na skript z původního bloku kódu:
    ```html
    <!--Script references. -->
    <!--Reference the jQuery library. -->
    <script src="Scripts/jquery-3.1.1.min.js" ></script>
    <!--Reference the SignalR library. -->
    <script src="Scripts/jquery.signalR-2.2.1.min.js"></script>
    ```

1. Pokud se neshodují, aktualizujte *.html* souboru.

1. V panelu nabídky vyberte **souboru** > **Uložit vše**.

## <a name="run-the-sample"></a>Spuštění ukázky

1. Na panelu nástrojů, zapněte **ladění skriptů** a pak vyberte tlačítko Přehrát akci spustíte ukázku v režimu ladění.

    ![Zadejte uživatelské jméno.](tutorial-getting-started-with-signalr/_static/image3.png)

1. Pokud v prohlížeči se otevře, zadejte název pro svou identitu konverzace.

1. Zkopírujte adresu URL z prohlížeče, otevřete dvou dalších prohlížečích a adresy URL vložte do řádku s adresou.

1. V každé prohlížeče zadejte jedinečný název.

1. Teď přidejte komentář a vyberte **odeslat**. Opakování, který v dalších prohlížečích. Komentáře se zobrazí v reálném čase.

    > [!NOTE]
    > Tento jednoduchý chatovací aplikaci nespravuje kontext diskuse na serveru. Centrum vysílá poznámky pro všechny aktuálního uživatele. Uživatelé, kteří později připojit chat uvidí zprávy přidány od okamžiku, že se že připojí.

    Podívejte se jak chatovací aplikaci spustí ve třech různých prohlížečích. Při Tom Anand a Susan odesílat zprávy, všechny prohlížeče aktualizace v reálném čase:

    ![Všechny tři prohlížeče zobrazit historii stejné konverzace](tutorial-getting-started-with-signalr/_static/image4.png)

1. V **Průzkumníka řešení**, zkontrolujte **dokumenty skriptu** uzel pro běžící aplikaci. Existuje soubor skriptu s názvem *rozbočovače* generující knihovně SignalR v době běhu. Tento soubor skladuje komunikaci mezi jQuery skriptu a kódem na straně serveru.

    ![automaticky generované hubs skript v uzlu dokumenty skriptu](tutorial-getting-started-with-signalr/_static/image5.png)

## <a name="examine-the-code"></a>Prozkoumejte kód

Aplikace SignalRChat ukazuje dvě základní úlohy vývoj pro funkci SignalR. To ukazuje, jak vytvořit centrum. Server používá jako objekt hlavního koordinace daném rozbočovači. Centrum používá knihovny jQuery SignalR k odesílání a příjem zpráv.

### <a name="signalr-hubs-in-the-chathubcs"></a>Rozbočovače SignalR v ChatHub.cs

V ukázkovém kódu výše `ChatHub` třída odvozena z `Microsoft.AspNet.SignalR.Hub` třídy. Odvozování z `Hub` třída je užitečný způsob, jak vytvořit aplikaci SignalR. Můžete vytvořit veřejné metody na třídě centra a potom použít tyto metody jejich voláním z skripty na webové stránce.

V kódu, konverzace, klienti volání `ChatHub.Send` metoda odesílá nová zpráva. Centrum pak odešle zprávu pro všechny klienty voláním `Clients.All.broadcastMessage`.

`Send` Metoda ukazuje několik konceptů hub:

* Deklarujte veřejné metody v rozbočovači, tak, aby klienti mohou volat.

* Použití `Microsoft.AspNet.SignalR.Hub.Clients` dynamických vlastností pro komunikaci se všemi klienty připojené pro toto centrum.

* Volání funkce na straně klienta (například `broadcastMessage` funkce) aktualizovat klienty.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample4.cs)]

### <a name="signalr-and-jquery-in-the-indexhtml"></a>SignalR a jQuery v index.html

*Index.html* stránky ve vzorovém kódu ukazuje, jak používat knihovny jQuery SignalR pro komunikaci se rozbočovače SignalR. Kód provede celou řadu důležitých úloh. Deklaruje proxy server tak, aby odkazovaly rozbočovači, deklaruje funkci, že server může volat do předávaný obsah pro klienty a spustí připojení k odeslání zprávy do centra.

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample5.js)]

> [!NOTE]
> V jazyce JavaScript odkaz na třídu serveru a jeho členy musí být camelCase. Odkazy na ukázkový kód C# *ChatHub* třídy v jazyce JavaScript jako `chatHub`.

V tomto bloku kódu můžete vytvořit funkce zpětného volání ve skriptu.

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample6.html)]

Třída rozbočovače na serveru volá tuto funkci tak, aby nabízel obsah aktualizací pro jednotlivé klienty. Dva řádky, že kódování HTML obsah před jeho zobrazení jsou volitelné a zobrazit vhodný způsob, jak brání injektáži skriptu.

Tento kód otevře připojení v centru.

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample7.js)]

> [!NOTE]
> Tento přístup zajišťuje, že kód naváže připojení před provedením obslužné rutiny události.

Kód spustí připojení a pak ji předá funkci pro zpracování události kliknutí na **odeslat** tlačítko na stránce HTML.

## <a name="additional-resources"></a>Další zdroje

Další informace o funkci SignalR naleznete v následujících zdrojích:

* [Projekt SignalR](http://signalr.net)

* [Funkce SignalR Githubu a ukázky](https://github.com/SignalR/SignalR)

* [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Nastavení projektu
> * Spuštění ukázky
> * Prozkoumat kód

Přejděte k dalším článku se dozvíte, jak používat funkci SignalR a MVC 5.
> [!div class="nextstepaction"]
> [Knihovnou SignalR 2 a MVC 5](tutorial-getting-started-with-signalr-and-mvc.md)