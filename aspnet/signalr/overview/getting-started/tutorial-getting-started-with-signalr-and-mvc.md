---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
title: 'Kurz: Začínáme s knihovnou SignalR 2 a MVC 5 | Dokumentace Microsoftu'
author: pfletcher
description: Tento kurz ukazuje použití funkcí SignalR 2 technologie ASP.NET k vytvoření aplikace pro chatování v reálném čase. Přidáte funkci SignalR k aplikaci MVC 5 a vytvořit zobrazení chat...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: 80bfe5fb-bdfc-41fe-ac43-2132e5d69fac
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
msc.type: authoredcontent
ms.openlocfilehash: 568f82daa67f33736c2bf7a45a3e1339f265c487
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287508"
---
<a name="tutorial-getting-started-with-signalr-2-and-mvc-5"></a>Kurz: Začínáme s knihovnou SignalR 2 a MVC 5
====================
podle [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[Stáhnout dokončený projekt](http://code.msdn.microsoft.com/Getting-Started-with-c366b2f3)

> Tento kurz ukazuje použití funkcí SignalR 2 technologie ASP.NET k vytvoření aplikace pro chatování v reálném čase. Přidáte funkci SignalR k aplikaci MVC 5 a vytvořit zobrazení chatu k odesílání a zobrazení zprávy.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - MVC 5
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

Tento kurz vás seznámí s vývoj aplikací webu v reálném čase s knihovnou SignalR 2 technologie ASP.NET a ASP.NET MVC 5. V tomto kurzu použijete stejný kód aplikace chat jako [kurzu Začínáme se SignalR](tutorial-getting-started-with-signalr.md), ale ukazuje, jak přidat do aplikace MVC 5.

V tomto tématu se dozvíte následujících úkolů SignalR:

- Přidání knihovny SignalR pro aplikaci MVC 5.
- Vytvoření rozbočovače a spouštěcí třídy OWIN tak, aby nabízel obsah pro klienty.
- K odesílání zpráv a zobrazení aktualizací z centra pomocí knihovny jQuery SignalR na webové stránce.

Následující snímek obrazovky ukazuje dokončené chatovací aplikaci spuštěnou v prohlížeči.

![Instance chatu](tutorial-getting-started-with-signalr-and-mvc/_static/image1.png)

Oddíly:

- [Nastavení projektu](#setup)
- [Spuštění ukázky](#run)
- [Prozkoumejte kód](#code)
- [Další kroky](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a>Nastavení projektu

Požadavky:

- Visual Studio 2013. Pokud nemáte Visual Studio, přečtěte si téma [ASP.NET stáhne](https://www.asp.net/downloads) získat bezplatné Visual Studio 2013 Express vývojový nástroj.

Tato část ukazuje, jak vytvořit aplikaci ASP.NET MVC 5 a přidejte knihovny SignalR, vytvořit chatovací aplikaci.

1. V sadě Visual Studio vytvořit aplikaci C# ASP.NET, který cílí na rozhraní .NET Framework 4.5, pojmenujte ho SignalRChat a klikněte na tlačítko OK.

    ![Vytvořte web](tutorial-getting-started-with-signalr-and-mvc/_static/image2.png)
2. V `New ASP.NET Project` dialogového okna a vyberte **MVC**a klikněte na tlačítko **změna ověřování**.

    ![Vytvořte web](tutorial-getting-started-with-signalr-and-mvc/_static/image3.png)
3. Vyberte **bez ověřování** v **změna ověřování** dialogových oken a klikněte na tlačítko **OK**.

    ![Vyberte bez ověřování](tutorial-getting-started-with-signalr-and-mvc/_static/image4.png)

    > [!NOTE]
    > Pokud vyberete jiný ověřování zprostředkovatele pro vaši aplikaci `Startup.cs` třída se vytvoří pro vás; nebudete muset vytvořit vlastní `Startup.cs` třídy v kroku 10 níže.
4. Klikněte na tlačítko **OK** v **nový projekt ASP.NET** dialogového okna.
5. Otevřít **nástroje > Správce balíčků NuGet > Konzola správce balíčků** a spusťte následující příkaz. Tento krok přidává do projektu sadu souborů skriptů a odkazy na sestavení, které umožňují funkce SignalR.

    `install-package Microsoft.AspNet.SignalR`
6. V **Průzkumníka řešení**, rozbalte složku skripty. Všimněte si, že byly přidány do projektu knihovny skriptů pro funkci SignalR.

    ![Složka skripty](tutorial-getting-started-with-signalr-and-mvc/_static/image5.png)
7. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt, vyberte **přidat | Nová složka**, a přidejte novou složku s názvem **rozbočovače**.
8. Klikněte pravým tlačítkem myši **rozbočovače** složky, klikněte na tlačítko **přidat | Nová položka**, vyberte **Visual C# | Web | SignalR** uzlu **nainstalováno** vyberte **třída rozbočovače SignalR (v2)** v prostředním podokně a vytvoříte nové centrum s názvem **ChatHub.cs**. Tato třída bude používat jako server rozbočovače SignalR, která odesílá zprávy na všechny klienty.

    ![Vytvoření nového centra](tutorial-getting-started-with-signalr-and-mvc/_static/image6.png)
9. Nahraďte kód v **ChatHub** třídy následujícím kódem.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample1.cs)]
10. Vytvořte novou třídu s názvem souboru Startup.cs. Změňte obsah souboru následujícím způsobem.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample2.cs)]
11. Upravit `HomeController` třída součástí **Controllers/HomeController.cs** a přidejte následující metodu do třídy. Tato metoda vrátí **Chat** zobrazení, které vytvoříte v pozdějším kroku.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample3.cs)]
12. Klikněte pravým tlačítkem myši **zobrazení Domů** a pak zvolte položku **přidat... | Zobrazení**.
13. V **přidat zobrazení** dialogového okna, název nového zobrazení **Chat**.

    ![Přidání zobrazení](tutorial-getting-started-with-signalr-and-mvc/_static/image7.png)
14. Nahraďte obsah **Chat.cshtml** následujícím kódem.

    > [!IMPORTANT]
    > Když přidáte SignalR a další skript knihovny do projektu sady Visual Studio, může správce balíčků nainstalovat verzi souboru skriptu SignalR, která je novější než verze uvedené v tomto tématu. Ujistěte se, že odkaz na skript v kódu odpovídá verzi nainstalovanou ve vašem projektu knihovnu skriptu.

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample4.cshtml)]
15. **Uložit vše** pro projekt.

<a id="run"></a>

## <a name="run-the-sample"></a>Spuštění ukázky

1. Stisknutím klávesy F5 spusťte projekt v režimu ladění.
2. V adresním řádku prohlížeče připojit **/home/chat** odeslané na adresu výchozí stránky pro projekt. Chat stránka načte v instanci prohlížeče a vyzve k zadání uživatelského jména.

    ![Zadejte uživatelské jméno.](tutorial-getting-started-with-signalr-and-mvc/_static/image8.png)
3. Zadejte uživatelské jméno.
4. Zkopírujte adresu URL v adresním řádku prohlížeče a můžete otevřít dvě další instance prohlížeče. V každé instanci prohlížeče zadejte jedinečné uživatelské jméno.
5. V každé instanci prohlížeče, přidejte komentář a klikněte na tlačítko **odeslat**. Zobrazit komentáře ve všech instancích prohlížeče.

    > [!NOTE]
    > Tento jednoduchý chatovací aplikaci nespravuje kontext diskuse na serveru. Centrum vysílá poznámky pro všechny aktuálního uživatele. Uživatelé, kteří později připojit chat uvidí zprávy přidány od okamžiku, že se že připojí.
6. Na následujícím snímku obrazovky je vidět chatovací aplikace spuštěné v prohlížeči.

    ![Chat prohlížeče](tutorial-getting-started-with-signalr-and-mvc/_static/image9.png)
7. V **Průzkumníka řešení**, zkontrolujte **dokumenty skriptu** uzel pro běžící aplikaci. Tento uzel je viditelný v režimu ladění, pokud používáte Internet Explorer jako svůj prohlížeč. Existuje soubor skriptu s názvem **rozbočovače** generující knihovně SignalR dynamicky za běhu. Tento soubor skladuje komunikaci mezi jQuery skriptu a kódem na straně serveru. Pokud používáte prohlížeč než Internet Explorer, se dá dostat taky dynamické **rozbočovače** souboru tak, že přejdete na ni přímo, například http://mywebsite/signalr/hubs.

<a id="code"></a>

## <a name="examine-the-code"></a>Prozkoumejte kód

Chatovací aplikace SignalR ukazuje dvě základní úkoly vývoje SignalR: vytváří se centrum jako objekt hlavního koordinace na serveru a pomocí knihovny jQuery SignalR k odesílání a příjem zpráv.

### <a name="signalr-hubs"></a>Rozbočovače SignalR

V ukázce kódu **ChatHub** třída odvozena z **Microsoft.AspNet.SignalR.Hub** třídy. Odvozování z **centra** třída je užitečný způsob, jak vytvořit aplikaci SignalR. Můžete vytvořit veřejné metody ve třídě centra a potom tyto metody přístup k jejich voláním z skripty na webové stránce.

V kódu, konverzace, klienti volání **ChatHub.Send** metoda odesílá nová zpráva. Centrum pak odešle zprávu pro všechny klienty voláním **Clients.All.addNewMessageToPage**.

**Odeslat** metoda ukazuje několik konceptů hub:

- Deklarujte veřejné metody v rozbočovači, tak, aby klienti mohou volat.
- Použití **Microsoft.AspNet.SignalR.Hub.Clients** vlastnosti pro přístup k všechny klienty připojené pro toto centrum.
- Volání funkce na straně klienta (například `addNewMessageToPage` funkce) aktualizovat klienty.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a>SignalR a jQuery

**Chat.cshtml** zobrazení souboru v příkladu kódu ukazuje, jak používat knihovny jQuery SignalR pro komunikaci se rozbočovače SignalR. Odkaz na automaticky generovaný proxy serveru pro rozbočovač, deklarace funkce, která můžete volat na serveru, abyste předávaný obsah pro klienty a spuštění připojení pro odesílání zpráv do centra vytváření základních úloh v kódu.

Následující kód deklaruje odkaz na proxy server rozbočovače.

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample6.js)]

> [!NOTE]
> V jazyce JavaScript je odkaz na třídu serveru a jeho členy v stylem camel case. Odkazuje na vzorový kód jazyka C# **ChatHub** třídy v jazyce JavaScript jako **chatHub**. Pokud chcete odkazovat `ChatHub` třídy v jQuery s konvenčním Pascal malá a velká písmena stejně jako v jazyce C#, upravte soubor třídy ChatHub.cs. Přidat `using` příkaz tak, aby odkazovaly `Microsoft.AspNet.SignalR.Hubs` oboru názvů. Pak přidejte `HubName` atribut `ChatHub` třídy, například `[HubName("ChatHub")]`. Nakonec aktualizujte referenci jQuery pro `ChatHub` třídy.


Následující kód ukazuje, jak vytvořit funkci zpětného volání ve skriptu. Třída rozbočovače na serveru volá tuto funkci tak, aby nabízel obsah aktualizací pro jednotlivé klienty. Volitelné volání `htmlEncode` funkce ukazuje způsob, jak HTML kódování obsahu zprávy před jejich zobrazením na stránce jako způsob, jak brání injektáži skriptu.

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample7.html)]

Následující kód ukazuje, jak otevřít připojení v centru. Kód spustí připojení a pak ji předá funkci pro zpracování události kliknutí na **odeslat** tlačítko na stránce konverzace.

> [!NOTE]
> Tento přístup zajišťuje, že připojení před provedením obslužná rutina události.


[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a>Další kroky

Jste zjistili, že SignalR je architektura určená k vytváření aplikací webu v reálném čase. Také jste se naučili několik úloh vývoje SignalR: jak přidat do aplikace ASP.NET SignalR, jak vytvořit třídu centra a jak odesílat a přijímat zprávy z centra.

Návod k nasazení ukázkové aplikace SignalR pro Azure najdete v tématu [pomocí SignalR s webovými aplikacemi ve službě Azure App Service](../deployment/using-signalr-with-azure-web-sites.md). Podrobné informace o tom, jak nasadit webový projekt sady Visual Studio na webu Windows Azure naleznete v tématu [vytvoření webové aplikace ASP.NET ve službě Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).

Informace o pokročilejších pojmech vývoj SignalR, naleznete na následujících stránkách pro funkci SignalR zdrojový kód a prostředky:

- [Projekt SignalR](http://signalr.net)
- [Funkce SignalR Githubu a ukázky](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)
