---
uid: signalr/overview/older-versions/tutorial-getting-started-with-signalr-and-mvc-4
title: 'Kurz: Začínáme s knihovnou SignalR 1.x a MVC 4 | Dokumentace Microsoftu'
author: bradygaster
description: Pomocí funkce SignalR technologie ASP.NET a ASP.NET MVC 4 k sestavení aplikace pro chatování v reálném čase.
ms.author: bradyg
ms.date: 03/29/2013
ms.assetid: eeef9f73-6de3-49f9-b50b-9af22108f2ce
msc.legacyurl: /signalr/overview/older-versions/tutorial-getting-started-with-signalr-and-mvc-4
msc.type: authoredcontent
ms.openlocfilehash: dd55ca22004b7e3899f6a8789494c842b984787f
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837790"
---
<a name="tutorial-getting-started-with-signalr-1x-and-mvc-4"></a>Kurz: Začínáme s knihovnou SignalR 1.x a MVC 4
====================
podle [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Tento kurz ukazuje, jak používat knihovnu ASP.NET SignalR k vytvoření aplikace pro chatování v reálném čase. Přidáte funkci SignalR k aplikaci MVC 4 a vytvořit zobrazení chatu k odesílání a zobrazení zprávy.


## <a name="overview"></a>Přehled

Tento kurz vás seznámí s vývoj webu v reálném čase aplikace s použitím funkce SignalR technologie ASP.NET a ASP.NET MVC 4. V tomto kurzu použijete stejný kód aplikace chat jako [kurzu Začínáme se SignalR](tutorial-getting-started-with-signalr.md), ale ukazuje, jak přidat do aplikace MVC 4 na základě šablony Internet.

V tomto tématu se dozvíte následujících úkolů SignalR:

- Přidání knihovny SignalR pro aplikaci MVC 4.
- Vytvoření třídy centra tak, aby nabízel obsah pro klienty.
- K odesílání zpráv a zobrazení aktualizací z centra pomocí knihovny jQuery SignalR na webové stránce.

Následující snímek obrazovky ukazuje dokončené chatovací aplikaci spuštěnou v prohlížeči.

![Instance chatu](tutorial-getting-started-with-signalr-and-mvc-4/_static/image2.png)

Oddíly:

- [Nastavení projektu](#setup)
- [Spuštění ukázky](#run)
- [Prozkoumejte kód](#code)
- [Další kroky](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a>Nastavení projektu

Požadavky:

- Visual Studio 2010 SP1, Visual Studio 2012 nebo Visual Studio 2012 Express. Pokud nemáte Visual Studio, přečtěte si téma [ASP.NET stáhne](https://www.asp.net/downloads) získat bezplatné Visual Studio 2012 Express vývojový nástroj.
- Pro sadu Visual Studio 2010, nainstalujte [ASP.NET MVC 4](https://www.microsoft.com/download/details.aspx?id=30683).

Tato část ukazuje, jak vytvořit aplikaci ASP.NET MVC 4, přidejte knihovny SignalR a vytvořit chatovací aplikaci.

1. 1. V sadě Visual Studio vytvořit aplikaci ASP.NET MVC 4, pojmenujte ho SignalRChat a klikněte na tlačítko OK.

        > [!NOTE]
        > Ve VS 2010, vyberte **rozhraní .NET Framework 4** v ovládacím prvku rozevíracího seznamu verzi rozhraní Framework. SignalR kód běží na rozhraní .NET Framework verze 4 a 4.5.

        ![Vytvořte mvc web](tutorial-getting-started-with-signalr-and-mvc-4/_static/image3.png)
      2. Vyberte šablonu aplikace Internet, zrušte zaškrtnutí políčka pro **vytvořit projekt testování částí**a klikněte na tlačítko OK.

         ![Vytvořte web na Internetu mvc](tutorial-getting-started-with-signalr-and-mvc-4/_static/image4.png)
      3. Otevřít **nástroje > Správce balíčků NuGet > Konzola správce balíčků** a spusťte následující příkaz. Tento krok přidává do projektu sadu souborů skriptů a odkazy na sestavení, které umožňují funkce SignalR.

         `install-package Microsoft.AspNet.SignalR -Version 1.1.3`
      4. V **Průzkumníka řešení** rozbalte složku skripty. Všimněte si, že byly přidány do projektu knihovny skriptů pro funkci SignalR.

         ![Odkazy na knihovny](tutorial-getting-started-with-signalr-and-mvc-4/_static/image6.png)
      5. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt, vyberte **přidat | Nová složka**, a přidejte novou složku s názvem **rozbočovače**.
      6. Klikněte pravým tlačítkem myši **rozbočovače** složky, klikněte na tlačítko **přidat | Třída**a vytvořte novou třídu C# s názvem **ChatHub.cs**. Tato třída bude používat jako server rozbočovače SignalR, která odesílá zprávy na všechny klienty.

> [!NOTE]
> Pokud používáte sadu Visual Studio 2012 a nainstalovali [ASP.NET and Web Tools 2012.2 aktualizace](../../../visual-studio/overview/2012/aspnet-and-web-tools-20122-release-notes-rtw.md#_Installation), můžete použít novou šablonu položky SignalR k vytvoření třídy rozbočovače. To mohli udělat, klikněte pravým tlačítkem myši **rozbočovače** složky, klikněte na tlačítko **přidat | Nová položka**vyberte **třída rozbočovače SignalR (v1)** a název třídy **ChatHub.cs**.


1. Nahraďte kód v **ChatHub** třídy následujícím kódem.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample1.cs)]
2. Otevřít **Global.asax** souboru pro projekt a přidejte volání do metody `RouteTable.Routes.MapHubs();` jako první řádek kódu v `Application_Start` metody. Tento kód zaregistruje výchozí trasu rozbočovače SignalR a musí být volán předtím, než zaregistrujete jakýchkoli jiných tras. Dokončené `Application_Start` metoda vypadá jako v následujícím příkladu.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample2.cs)]
3. Upravit `HomeController` třída součástí **Controllers/HomeController.cs** a přidejte následující metodu do třídy. Tato metoda vrátí **Chat** zobrazení, které vytvoříte v pozdějším kroku.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample3.cs)]
4. Klikněte pravým tlačítkem `Chat` metoda jste právě vytvořili a klikněte na tlačítko **přidat zobrazení** k vytvoření nového zobrazení souboru.
5. V **přidat zobrazení** dialogového okna, ujistěte se, že políčko k **použít rozložení stránky předlohy** (zrušte zaškrtnutí ostatních políček) a potom klikněte na tlačítko **přidat**.

    ![Přidání zobrazení](tutorial-getting-started-with-signalr-and-mvc-4/_static/image8.png)
6. Upravte nový soubor zobrazení s názvem **Chat.cshtml**. Po &lt;h2&gt; značku, vložte následující &lt;div&gt; oddílu a `@section scripts` blok kódu do stránky. Tímto skriptem povolíte stránku k odeslání zprávy chatu a zobrazení zprávy ze serveru. Kompletní kód pro chat zobrazení se zobrazí v následující blok kódu.

    > [!IMPORTANT]
    > Když přidáte SignalR a další skript knihovny do projektu sady Visual Studio, může nainstalovat Správce balíčků verze skripty, které jsou novější než verze uvedené v tomto tématu. Ujistěte se, že skript odkazy ve vašem kódu shodovat s verzemi knihovny skriptů, které jsou nainstalovány ve vašem projektu.

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample4.cshtml)]
7. **Uložit vše** pro projekt.

<a id="run"></a>

## <a name="run-the-sample"></a>Spuštění ukázky

1. Stisknutím klávesy F5 spusťte projekt v režimu ladění.
2. V adresním řádku prohlížeče připojit **/home/chat** odeslané na adresu výchozí stránky pro projekt. Chat stránka načte v instanci prohlížeče a vyzve k zadání uživatelského jména.

    ![Zadejte uživatelské jméno.](tutorial-getting-started-with-signalr-and-mvc-4/_static/image9.png)
3. Zadejte uživatelské jméno.
4. Zkopírujte adresu URL v adresním řádku prohlížeče a můžete otevřít dvě další instance prohlížeče. V každé instanci prohlížeče zadejte jedinečné uživatelské jméno.
5. V každé instanci prohlížeče, přidejte komentář a klikněte na tlačítko **odeslat**. Zobrazit komentáře ve všech instancích prohlížeče.

    > [!NOTE]
    > Tento jednoduchý chatovací aplikaci nespravuje kontext diskuse na serveru. Centrum vysílá poznámky pro všechny aktuálního uživatele. Uživatelé, kteří později připojit chat uvidí zprávy přidány od okamžiku, že se že připojí.
6. Na následujícím snímku obrazovky je vidět chatovací aplikace spuštěné v prohlížeči.

    ![Chat prohlížeče](tutorial-getting-started-with-signalr-and-mvc-4/_static/image11.png)
7. V **Průzkumníka řešení**, zkontrolujte **dokumenty skriptu** uzel pro běžící aplikaci. Tento uzel je viditelný v režimu ladění, pokud používáte Internet Explorer jako svůj prohlížeč. Existuje soubor skriptu s názvem **rozbočovače** generující knihovně SignalR dynamicky za běhu. Tento soubor skladuje komunikaci mezi jQuery skriptu a kódem na straně serveru. Pokud používáte prohlížeč než Internet Explorer, se dá dostat taky dynamické **rozbočovače** souboru tak, že přejdete na ni přímo, například http://mywebsite/signalr/hubs.

    ![Vygenerovaný centra skriptů](tutorial-getting-started-with-signalr-and-mvc-4/_static/image13.png)

<a id="code"></a>

## <a name="examine-the-code"></a>Prozkoumejte kód

Chatovací aplikace SignalR ukazuje dvě základní úkoly vývoje SignalR: vytváří se centrum jako objekt hlavního koordinace na serveru a pomocí knihovny jQuery SignalR k odesílání a příjem zpráv.

### <a name="signalr-hubs"></a>Rozbočovače SignalR

V ukázce kódu **ChatHub** třída odvozena z **Microsoft.AspNet.SignalR.Hub** třídy. Odvozování z **centra** třída je užitečný způsob, jak vytvořit aplikaci SignalR. Můžete vytvořit veřejné metody ve třídě centra a potom tyto metody přístup k jejich voláním z jQuery skripty na webové stránce.

V kódu, konverzace, klienti volání **ChatHub.Send** metoda odesílá nová zpráva. Centrum pak odešle zprávu pro všechny klienty voláním **Clients.All.addNewMessageToPage**.

**Odeslat** metoda ukazuje několik konceptů hub:

- Deklarujte veřejné metody v rozbočovači, tak, aby klienti mohou volat.
- Použití **Microsoft.AspNet.SignalR.Hub.Clients** vlastnosti pro přístup k všechny klienty připojené pro toto centrum.
- Volání funkce jQuery na straně klienta (například `addNewMessageToPage` funkce) aktualizovat klienty.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a>SignalR a jQuery

**Chat.cshtml** zobrazení souboru v příkladu kódu ukazuje, jak používat knihovny jQuery SignalR pro komunikaci se rozbočovače SignalR. Odkaz na automaticky generovaný proxy serveru pro rozbočovač, deklarace funkce, která můžete volat na serveru, abyste předávaný obsah pro klienty a spuštění připojení pro odesílání zpráv do centra vytváření základních úloh v kódu.

Následující kód deklaruje proxy server rozbočovače.

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample6.js)]

> [!NOTE]
> V jQuery je odkaz na třídu serveru a jeho členy v stylem camel case. Odkazuje na vzorový kód jazyka C# **ChatHub** třídy v jQuery jako **chatHub**. Pokud chcete odkazovat `ChatHub` třídy v jQuery s konvenčním Pascal malá a velká písmena stejně jako v jazyce C#, upravte soubor třídy ChatHub.cs. Přidat `using` příkaz tak, aby odkazovaly `Microsoft.AspNet.SignalR.Hubs` oboru názvů. Pak přidejte `HubName` atribut `ChatHub` třídy, například `[HubName("ChatHub")]`. Nakonec aktualizujte referenci jQuery pro `ChatHub` třídy.


Následující kód ukazuje, jak vytvořit funkci zpětného volání ve skriptu. Třída rozbočovače na serveru volá tuto funkci tak, aby nabízel obsah aktualizací pro jednotlivé klienty. Volitelné volání `htmlEncode` funkce ukazuje způsob, jak HTML kódování obsahu zprávy před jejich zobrazením na stránce jako způsob, jak brání injektáži skriptu.

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample7.html)]

Následující kód ukazuje, jak otevřít připojení v centru. Kód spustí připojení a pak ji předá funkci pro zpracování události kliknutí na **odeslat** tlačítko na stránce konverzace.

> [!NOTE]
> Tento přístup zajišťuje, že připojení před provedením obslužná rutina události.


[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a>Další kroky

Jste zjistili, že SignalR je architektura určená k vytváření aplikací webu v reálném čase. Také jste se naučili několik úloh vývoje SignalR: jak přidat do aplikace ASP.NET SignalR, jak vytvořit třídu centra a jak odesílat a přijímat zprávy z centra.

Informace o pokročilejších pojmech vývoj SignalR, naleznete na následujících stránkách pro funkci SignalR zdrojový kód a prostředky:

- [Projekt SignalR](http://signalr.net)
- [Funkce SignalR Githubu a ukázky](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)
