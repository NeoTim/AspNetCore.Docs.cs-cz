---
uid: signalr/overview/older-versions/tutorial-getting-started-with-signalr
title: 'Kurz: Začínáme s knihovnou SignalR 1.x | Dokumentace Microsoftu'
author: bradygaster
description: Použijte knihovnu ASP.NET SignalR k sestavení aplikace chat v reálném čase na stránce HTML.
ms.author: bradyg
ms.date: 02/18/2013
ms.assetid: fdc3599a-5217-44c1-951f-0eec9812dce7
msc.legacyurl: /signalr/overview/older-versions/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: b4b632a84e40aa0b73dfc7a30da0cf28249cc5b4
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836629"
---
<a name="tutorial-getting-started-with-signalr-1x"></a>Kurz: Začínáme s funkcí SignalR 1.x
====================
podle [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> V tomto kurzu se naučíte používat funkci SignalR k vytvoření aplikace pro chatování v reálném čase. Přidáte SignalR prázdná webová aplikace ASP.NET a vytvořte stránku HTML k odeslání a zobrazení zprávy.


## <a name="overview"></a>Přehled

V tomto kurzu vám ukazuje, jak vytvořit jednoduchý založené na prohlížeči chatovací aplikaci představí vývoj pro funkci SignalR. Přidání knihovny SignalR na prázdnou webovou aplikaci ASP.NET, vytvoříte třídu hub pro odesílání zpráv do klientů a vytvořte stránku HTML, který umožňuje uživatelům odesílat a přijímat zprávy chatu. Podobný kurz, který ukazuje, jak vytvoření chatovací aplikace v MVC 4 pomocí zobrazení MVC, naleznete v tématu [Začínáme s knihovnou SignalR a MVC 4](index.md).

> [!NOTE]
> Tento kurz používá verzi vydání (1.x) ze systému SignalR. Podrobnosti o změny mezi knihovnou SignalR 1.x a 2.0, najdete v části [projektů upgrade SignalR 1.x](../releases/upgrading-signalr-1x-projects-to-20.md).

SignalR je open source knihovna .NET pro vytváření webových aplikací, které vyžadují aktualizace dat v reálném čase nebo interakci uživatelů za provozu. Mezi příklady patří sociální aplikace, hry pro více uživatelů, obchodní spolupráci a novinky, počasí nebo finanční aktualizace aplikace. Nazývají se často aplikací v reálném čase.

Funkce SignalR zjednodušuje proces vytváření aplikace v reálném čase. Zahrnuje server knihovny ASP.NET a Javascriptovou klientskou knihovnu, aby bylo snazší spravovat připojení klient server a push aktualizace obsahu pro klienty. Knihovny SignalR můžete přidat do stávající aplikace ASP.NET k získání funkcí v reálném čase.

Tento kurz demonstruje následující úkoly vývoje SignalR:

- Přidání knihovny SignalR k webové aplikaci ASP.NET.
- Vytvoření třídy centra tak, aby nabízel obsah pro klienty.
- K odesílání zpráv a zobrazení aktualizací z centra pomocí knihovny jQuery SignalR na webové stránce.

Na následujícím snímku obrazovky je vidět chatovací aplikace spuštěné v prohlížeči. Všichni noví uživatelé mohou připomínky a naleznete v tématu komentáře přidané po uživatel připojí chat.

![Instance chatu](tutorial-getting-started-with-signalr/_static/image1.png)

Oddíly:

- [Nastavení projektu](#setup)
- [Spuštění ukázky](#run)
- [Prozkoumejte kód](#code)
- [Další kroky](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a>Nastavení projektu

Tato část ukazuje, jak vytvořit prázdnou webovou aplikaci ASP.NET, přidejte SignalR a vytvořit chatovací aplikaci.

Požadavky:

- Visual Studio 2010 SP1 nebo 2012. Pokud nemáte Visual Studio, přečtěte si téma [ASP.NET stáhne](https://www.asp.net/downloads) získat bezplatné Visual Studio 2012 Express vývojový nástroj.
- [Microsoft ASP.NET a Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=279941). Tento instalační program sady Visual Studio 2012, přidá nové funkce technologie ASP.NET, včetně šablon SignalR k sadě Visual Studio. Pro Visual Studio 2010 SP1 instalační program není k dispozici, ale tento kurz můžete dokončit tak jak je popsáno v krocích instalační program instaluje se balíček SignalR NuGet.

Následující kroky slouží k vytvoření prázdná webová aplikace ASP.NET a přidání knihovny SignalR Visual Studio 2012:

1. V sadě Visual Studio vytvořte prázdnou webovou aplikaci ASP.NET.

    ![Vytvoření prázdného webu](tutorial-getting-started-with-signalr/_static/image2.png)
2. Otevřít **Konzola správce balíčků** tak, že vyberete **nástroje | Správce balíčků NuGet | Konzola správce balíčků**. Do okna konzoly zadejte následující příkaz:

    `Install-Package Microsoft.AspNet.SignalR -Version 1.1.3`

    Tento příkaz nainstaluje nejnovější verzi SignalR 1.x.
3. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt, vyberte **přidat | Třída**. Pojmenujte novou třídu **ChatHub**.
4. V **Průzkumníka řešení** rozbalte uzel skripty. Jsou viditelné v projektu knihovny skriptů pro knihovny jQuery a SignalR.

    ![Odkazy na knihovny](tutorial-getting-started-with-signalr/_static/image3.png)
5. Nahraďte kód v **ChatHub** třídy následujícím kódem.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]
6. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **přidat | Nová položka**. V **přidat novou položku** dialogového okna, vyberte **Global Application Class** a klikněte na tlačítko **přidat**.

    ![Přidání globální](tutorial-getting-started-with-signalr/_static/image4.png)
7. Přidejte následující `using` příkazy po zadaných `using` příkazy ve třídě Global.asax.cs.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample2.cs)]
8. Přidejte následující řádek kódu `Application_Start` metoda globální třídy k registraci výchozí trasu rozbočovače SignalR.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample3.cs)]
9. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **přidat | Nová položka**. V **přidat novou položku** dialogového okna, vyberte stránka Html a klikněte na tlačítko **přidat**.
10. V **Průzkumníka řešení**, klikněte pravým tlačítkem na stránku HTML, který jste právě vytvořili a klikněte na tlačítko **nastavit jako úvodní stránku**.
11. Nahraďte kód výchozí stránku HTML s následujícím kódem.

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample4.html)]
12. **Uložit vše** pro projekt.

<a id="run"></a>

## <a name="run-the-sample"></a>Spuštění ukázky

1. Stisknutím klávesy F5 spusťte projekt v režimu ladění. HTML stránka načte v instanci prohlížeče a vyzve k zadání uživatelského jména.

    ![Zadejte uživatelské jméno.](tutorial-getting-started-with-signalr/_static/image5.png)
2. Zadejte uživatelské jméno.
3. Zkopírujte adresu URL v adresním řádku prohlížeče a můžete otevřít dvě další instance prohlížeče. V každé instanci prohlížeče zadejte jedinečné uživatelské jméno.
4. V každé instanci prohlížeče, přidejte komentář a klikněte na tlačítko **odeslat**. Zobrazit komentáře ve všech instancích prohlížeče.

    > [!NOTE]
    > Tento jednoduchý chatovací aplikaci nespravuje kontext diskuse na serveru. Centrum vysílá poznámky pro všechny aktuálního uživatele. Uživatelé, kteří později připojit chat uvidí zprávy přidány od okamžiku, že se že připojí.

    Následující snímek obrazovky ukazuje chatovací aplikaci spuštěnou v tři instance prohlížeče, které aktualizují po jedné instance odešle zprávu:

    ![Chat prohlížeče](tutorial-getting-started-with-signalr/_static/image6.png)
5. V **Průzkumníka řešení**, zkontrolujte **dokumenty skriptu** uzel pro běžící aplikaci. Existuje soubor skriptu s názvem **rozbočovače** generující knihovně SignalR dynamicky za běhu. Tento soubor skladuje komunikaci mezi jQuery skriptu a kódem na straně serveru.

    ![Vygenerovaný centra skriptů](tutorial-getting-started-with-signalr/_static/image7.png)

<a id="code"></a>

## <a name="examine-the-code"></a>Prozkoumejte kód

Chatovací aplikace SignalR ukazuje dvě základní úkoly vývoje SignalR: vytváří se centrum jako objekt hlavního koordinace na serveru a pomocí knihovny jQuery SignalR k odesílání a příjem zpráv.

### <a name="signalr-hubs"></a>Rozbočovače SignalR

V ukázce kódu **ChatHub** třída odvozena z **Microsoft.AspNet.SignalR.Hub** třídy. Odvozování z **centra** třída je užitečný způsob, jak vytvořit aplikaci SignalR. Můžete vytvořit veřejné metody ve třídě centra a potom tyto metody přístup k jejich voláním z jQuery skripty na webové stránce.

V kódu, konverzace, klienti volání **ChatHub.Send** metoda odesílá nová zpráva. Centrum pak odešle zprávu pro všechny klienty voláním **Clients.All.broadcastMessage**.

**Odeslat** metoda ukazuje několik konceptů hub:

- Deklarujte veřejné metody v rozbočovači, tak, aby klienti mohou volat.
- Použití **Microsoft.AspNet.SignalR.Hub.Clients** dynamických vlastností pro přístup k všechny klienty připojené pro toto centrum.
- Volání funkce jQuery na straně klienta (například `broadcastMessage` funkce) aktualizovat klienty.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a>SignalR a jQuery

Na stránce HTML ve vzorovém kódu ukazuje, jak používat knihovny jQuery SignalR pro komunikaci se rozbočovače SignalR. Proxy server tak, aby odkazovaly rozbočovače, deklarace funkce, která můžete volat na serveru, abyste předávaný obsah pro klienty a spouští se připojení k odeslání zprávy do centra jsou deklarace základních úloh v kódu.

Následující kód deklaruje proxy server rozbočovače.

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample6.js)]

> [!NOTE]
> V jQuery je odkaz na třídu serveru a jeho členy v stylem camel case. Odkazuje na vzorový kód jazyka C# **ChatHub** třídy v jQuery jako **chatHub**.


Následující kód je, jak vytvořit funkci zpětného volání ve skriptu. Třída rozbočovače na serveru volá tuto funkci tak, aby nabízel obsah aktualizací pro jednotlivé klienty. Následující dva řádky, že s kódováním HTML obsah před jeho zobrazení jsou volitelné a zobrazit jednoduchý způsob, jak brání injektáži skriptu.

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample7.html)]

Následující kód ukazuje, jak otevřít připojení v centru. Kód spustí připojení a pak ji předá funkci pro zpracování události kliknutí na **odeslat** tlačítko na stránce HTML.

> [!NOTE]
> Tento přístup zajistí, že připojení před provedením obslužná rutina události.


[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a>Další kroky

Jste zjistili, že SignalR je architektura určená k vytváření aplikací webu v reálném čase. Také jste se naučili několik úloh vývoje SignalR: jak přidat do aplikace ASP.NET SignalR, jak vytvořit třídu centra a jak odesílat a přijímat zprávy z centra.

Můžete zpřístupnit ukázkovou aplikaci v tomto kurzu nebo jiné aplikace SignalR přes Internet podle jejich nasazení do poskytovatele hostitelských služeb. Společnost Microsoft nabízí bezplatné webových hostitelských služeb pro až 10 webových serverů v bezplatné [zkušebního účtu Windows Azure](https://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A443DD604). Podrobný postup nasazení ukázkové aplikace SignalR, naleznete v tématu [publikovat SignalR získávání spuštění ukázky jako webu Windows Azure](https://blogs.msdn.com/b/timlee/archive/2013/02/27/deploy-the-signalr-getting-started-sample-as-a-windows-azure-web-site.aspx). Podrobné informace o tom, jak nasadit webový projekt sady Visual Studio na webu Windows Azure naleznete v tématu [nasazení aplikace ASP.NET na web Windows Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet). (Poznámka: Přenos pomocí protokolu WebSocket není aktuálně podporován pro Windows Azure websites. Přenos při protokolu WebSocket nejsou k dispozici, SignalR používá k dispozici přenosy, jak je popsáno v části přenosy [Úvod do tématu SignalR](index.md).)

Informace o pokročilejších pojmech vývoj SignalR, naleznete na následujících stránkách pro funkci SignalR zdrojový kód a prostředky:

- [Projekt SignalR](http://signalr.net)
- [Funkce SignalR Githubu a ukázky](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)
