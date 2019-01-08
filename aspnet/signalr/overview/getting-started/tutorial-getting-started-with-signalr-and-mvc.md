---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
title: 'Kurz: Chatování v reálném čase s knihovnou SignalR 2 a MVC 5 | Dokumentace Microsoftu'
author: pfletcher
description: Tento kurz ukazuje použití funkcí SignalR 2 technologie ASP.NET k vytvoření aplikace pro chatování v reálném čase. Přidáte do aplikace MVC 5 SignalR.
ms.author: riande
ms.date: 01/02/2019
ms.assetid: 80bfe5fb-bdfc-41fe-ac43-2132e5d69fac
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: eb4b7e1403f4070d65702b756bf98c5294c7fb17
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098601"
---
# <a name="tutorial-real-time-chat-with-signalr-2-and-mvc-5"></a>Kurz: Chatování v reálném čase s knihovnou SignalR 2 a MVC 5

Tento kurz ukazuje použití funkcí SignalR 2 technologie ASP.NET k vytvoření aplikace pro chatování v reálném čase. Přidat funkci SignalR k aplikaci MVC 5 a vytvořte zobrazení chatu k odesílání a zobrazení zprávy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení projektu
> * Spuštění ukázky
> * Prozkoumejte kód

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a>Požadavky

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) s **vývoj pro ASP.NET a web** pracovního vytížení.

## <a name="set-up-the-project"></a>Nastavení projektu

Tato část ukazuje, jak pomocí sady Visual Studio 2017 a knihovnou SignalR 2 vytvořit prázdnou aplikaci ASP.NET MVC 5 a přidejte knihovny SignalR, vytvořit chatovací aplikaci.

1. V sadě Visual Studio vytvořit aplikaci C# ASP.NET, který cílí na rozhraní .NET Framework 4.5, pojmenujte ho SignalRChat a klikněte na tlačítko OK.

    ![Vytvořte web](tutorial-getting-started-with-signalr-and-mvc/_static/image1.png)

1. V **nová webová aplikace ASP.NET - SignalRMvcChat**vyberte **MVC** a pak vyberte **změna ověřování**.

1. V **změna ověřování**vyberte **bez ověřování** a klikněte na tlačítko **OK**.

    ![Vyberte bez ověřování](tutorial-getting-started-with-signalr-and-mvc/_static/image2.png)

1. V **nová webová aplikace ASP.NET - SignalRMvcChat**vyberte **OK**.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **nová položka**.

1. V **přidat novou položku - SignalRChat**vyberte **nainstalováno** > **Visual C#**   >  **webové**  >  **SignalR** a pak vyberte **třída rozbočovače SignalR (v2)**.

1. Název třídy *ChatHub* a přidejte ho do projektu.

    Tento krok vytvoří *ChatHub.cs* třídy soubor a přidá sadu souborů skriptů a odkazy na sestavení, podporující funkci SignalR k projektu.

1. Nahraďte kód v novém *ChatHub.cs* soubor třídy s tímto kódem:

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample1.cs)]

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a vyberte **přidat** > **třídy**.

1. Pojmenujte novou třídu *spuštění* a přidejte ho do projektu.

1. Nahraďte kód v *Startup.cs* soubor třídy s tímto kódem:

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample2.cs)]

1. V **Průzkumníka řešení**vyberte **řadiče** > **HomeController.cs**.

1. Přidejte tuto metodu za účelem *HomeController.cs*.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample3.cs)]

    Tato metoda vrátí **Chat** zobrazení, které vytvoříte v pozdějším kroku.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na **zobrazení** > **Domů**a vyberte **přidat**  >    **Zobrazení**.

1. V **přidat zobrazení**, pojmenujte nové zobrazení **Chat** a vyberte **přidat**.

1. Nahraďte obsah **Chat.cshtml** s tímto kódem:

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample4.cshtml)]

1. V **Průzkumníka řešení**, rozbalte **skripty**.

    Jsou viditelné v projektu knihovny skriptů pro knihovny jQuery a SignalR.

    > [!IMPORTANT]
    > Správce balíčků nainstalovanou novější verzi skriptů SignalR.

1. Zkontrolujte, jestli odkazy na skript v bloku kódu, které odpovídají verze souborů skript v projektu.

    Odkazy na skript z původního bloku kódu:

    ```cshtml
    <!--Script references. -->
    <!--The jQuery library is required and is referenced by default in _Layout.cshtml. -->
    <!--Reference the SignalR library. -->
    <script src="~/Scripts/jquery.signalR-2.1.0.min.js"></script>
    ```

1. Pokud se neshodují, aktualizujte *.cshtml* souboru.

1. V panelu nabídky vyberte **souboru** > **Uložit vše**.

## <a name="run-the-sample"></a>Spuštění ukázky

1. Na panelu nástrojů, zapněte **ladění skriptů** a pak vyberte tlačítko Přehrát akci spustíte ukázku v režimu ladění.

    ![Zadejte uživatelské jméno.](tutorial-getting-started-with-signalr-and-mvc/_static/image3.png)

1. Pokud v prohlížeči se otevře, zadejte název pro svou identitu konverzace.

1. Zkopírujte adresu URL z prohlížeče, otevřete dvou dalších prohlížečích a adresy URL vložte do řádku s adresou.

1. V každé prohlížeče zadejte jedinečný název.

1. Teď přidejte komentář a vyberte **odeslat**. Opakování, který v dalších prohlížečích. Komentáře se zobrazí v reálném čase.

    > [!NOTE]
    > Tento jednoduchý chatovací aplikaci nespravuje kontext diskuse na serveru. Centrum vysílá poznámky pro všechny aktuálního uživatele. Uživatelé, kteří později připojit chat uvidí zprávy přidány od okamžiku, že se že připojí.

    Podívejte se jak chatovací aplikaci spustí ve třech různých prohlížečích. Při Tom Anand a Susan odesílat zprávy, všechny prohlížeče aktualizace v reálném čase:

    ![Všechny tři prohlížeče zobrazit historii stejné konverzace](tutorial-getting-started-with-signalr-and-mvc/_static/image4.png)

1. V **Průzkumníka řešení**, zkontrolujte **dokumenty skriptu** uzel pro běžící aplikaci. Existuje soubor skriptu s názvem *rozbočovače* generující knihovně SignalR v době běhu. Tento soubor skladuje komunikaci mezi jQuery skriptu a kódem na straně serveru.

    ![automaticky generované hubs skript v uzlu dokumenty skriptu](tutorial-getting-started-with-signalr-and-mvc/_static/image5.png)

## <a name="examine-the-code"></a>Prozkoumejte kód

Chatovací aplikace SignalR ukazuje dvě základní úlohy vývoj pro funkci SignalR. To ukazuje, jak vytvořit centrum. Server používá jako objekt hlavního koordinace daném rozbočovači. Centrum používá knihovny jQuery SignalR k odesílání a příjem zpráv.

### <a name="signalr-hubs-in-the-chathubcs"></a>Rozbočovače SignalR v ChatHub.cs

V ukázce kódu `ChatHub` třída odvozena z `Microsoft.AspNet.SignalR.Hub` třídy. Odvozování z `Hub` třída je užitečný způsob, jak vytvořit aplikaci SignalR. Můžete vytvořit veřejné metody ve třídě centra a potom tyto metody přístup k jejich voláním z skripty na webové stránce.

V kódu, konverzace, klienti volání `ChatHub.Send` metoda odesílá nová zpráva. Centrum pak odešle zprávu pro všechny klienty voláním `Clients.All.addNewMessageToPage`.

`Send` Metoda ukazuje několik konceptů hub:

* Deklarujte veřejné metody v rozbočovači, tak, aby klienti mohou volat.

* Použití `Microsoft.AspNet.SignalR.Hub.Clients` dynamických vlastností pro komunikaci se všemi klienty připojené pro toto centrum.

* Volání funkce na straně klienta (například `addNewMessageToPage` funkce) aktualizovat klienty.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample5.cs)]

### <a name="signalr-and-jquery-chatcshtml"></a>SignalR a jQuery Chat.cshtml

*Chat.cshtml* zobrazení souboru v příkladu kódu ukazuje, jak používat knihovny jQuery SignalR pro komunikaci se rozbočovače SignalR.  Kód provede celou řadu důležitých úloh. Vytvoří odkaz na automaticky vygenerovaný proxy server rozbočovače, deklaruje funkci, že server může volat tak, aby nabízel obsah pro klienty a spustí připojení k odeslání zprávy do centra.

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample6.js)]

> [!NOTE]
> V jazyce JavaScript je odkaz na třídu serveru a jeho členy ve formátu camelCase. Odkazy na ukázkový kód C# `ChatHub` třídy v jazyce JavaScript jako `chatHub`.

V tomto bloku kódu můžete vytvořit funkce zpětného volání ve skriptu.

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample7.html)]

Třída rozbočovače na serveru volá tuto funkci tak, aby nabízel obsah aktualizací pro jednotlivé klienty. Volitelné volání `htmlEncode` funkce ukazuje způsob, jak HTML kódování obsahu zprávy před jejich zobrazením na stránce. Je to způsob, aby se zabránilo injektáži skriptu.

Tento kód otevře připojení v centru.

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample8.js)]

> [!NOTE]
> Tento přístup zajišťuje, před provedením obslužná rutina události navázat připojení.

Kód spustí připojení a pak ji předá funkci pro zpracování události kliknutí na **odeslat** tlačítko na stránce konverzace.

## <a name="additional-resources"></a>Další zdroje

Další informace o funkci SignalR naleznete v následujících zdrojích:

* [Projekt SignalR](http://signalr.net)

* [Funkce SignalR Githubu a ukázky](https://github.com/SignalR/SignalR)

* [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení projektu
> * Spuštění ukázky
> * Prozkoumat kód

Přejděte k dalším článku se naučíte, jak vytvořit webovou aplikaci, která používá ASP.NET SignalR 2 zasílání zpráv nakonfigurovánu vysokou frekvencí.
> [!div class="nextstepaction"]
> [Webová aplikace s vysokou frekvencí zasílání zpráv](tutorial-high-frequency-realtime-with-signalr.md)