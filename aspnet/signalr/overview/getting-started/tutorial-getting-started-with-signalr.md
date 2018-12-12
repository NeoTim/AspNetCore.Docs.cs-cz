---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr
title: 'Kurz: Začínáme s knihovnou SignalR 2 | Dokumentace Microsoftu'
author: pfletcher
description: V tomto kurzu se naučíte používat funkci SignalR k vytvoření aplikace pro chatování v reálném čase. Přidáte SignalR prázdná webová aplikace ASP.NET a vytvoření pa HTML...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: a8b3b778-f009-4369-85c7-e90f9878d8b4
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 3b06e7d0a602e061112adbceba92276f836f6311
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287335"
---
<a name="tutorial-getting-started-with-signalr-2"></a>Kurz: Začínáme s funkcí SignalR 2
====================
podle [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[Stáhnout dokončený projekt](http://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9)

> V tomto kurzu se naučíte používat funkci SignalR k vytvoření aplikace pro chatování v reálném čase. Přidáte SignalR prázdná webová aplikace ASP.NET a vytvořte stránku HTML k odeslání a zobrazení zprávy. 
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads)
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

V tomto kurzu vám ukazuje, jak vytvořit jednoduchý založené na prohlížeči chatovací aplikaci představí vývoj pro funkci SignalR. Přidání knihovny SignalR na prázdnou webovou aplikaci ASP.NET, vytvoříte třídu hub pro odesílání zpráv do klientů a vytvořte stránku HTML, který umožňuje uživatelům odesílat a přijímat zprávy chatu. Podobný kurz, který ukazuje, jak vytvořit chatovací aplikaci MVC 5 pomocí zobrazení MVC, naleznete v tématu [Začínáme s knihovnou SignalR 2 a MVC 5](tutorial-getting-started-with-signalr-and-mvc.md).

> [!NOTE]
> Tento kurz ukazuje, jak vytvářet aplikace SignalR ve verzi 2. Podrobnosti o změny mezi knihovnou SignalR 1.x a 2, najdete v části [projektů upgrade SignalR 1.x](../releases/upgrading-signalr-1x-projects-to-20.md) a [Visual Studio 2013 – poznámky k](../../../visual-studio/overview/2013/release-notes.md#TOC13).

SignalR je open source knihovna .NET pro vytváření webových aplikací, které vyžadují aktualizace dat v reálném čase nebo interakci uživatelů za provozu. Mezi příklady patří sociální aplikace, hry pro více uživatelů, obchodní spolupráci a novinky, počasí nebo finanční aktualizace aplikace. Nazývají se často aplikací v reálném čase.

Funkce SignalR zjednodušuje proces vytváření aplikace v reálném čase. Zahrnuje server knihovny ASP.NET a Javascriptovou klientskou knihovnu, aby bylo snazší spravovat připojení klient server a push aktualizace obsahu pro klienty. Knihovny SignalR můžete přidat do stávající aplikace ASP.NET k získání funkcí v reálném čase.

Tento kurz demonstruje následující úkoly vývoje SignalR:

- Přidání knihovny SignalR k webové aplikaci ASP.NET.
- Vytvoření třídy centra tak, aby nabízel obsah pro klienty.
- Vytvoření třídy pro spuštění OWIN konfigurace aplikace.
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

Tato část ukazuje, jak pomocí sady Visual Studio 2013 a technologie SignalR verze 2 můžete vytvořit prázdnou webovou aplikaci ASP.NET, přidejte SignalR a vytvořit chatovací aplikaci.

Požadavky:

- Visual Studio 2013. Pokud nemáte Visual Studio, přečtěte si téma [ASP.NET stáhne](https://www.asp.net/downloads) získat bezplatné Visual Studio 2013 Express vývojový nástroj.

Následující kroky slouží k vytvoření prázdná webová aplikace ASP.NET a přidání knihovny SignalR Visual Studio 2013:

1. V sadě Visual Studio vytvořte webovou aplikaci ASP.NET.

    ![Vytvořte web](tutorial-getting-started-with-signalr/_static/image2.png)
2. V **nový projekt ASP.NET** okně, ponechejte tuto položku **prázdný** vybraný a klikněte na tlačítko **vytvořit projekt**.

    ![Vytvoření prázdného webu](tutorial-getting-started-with-signalr/_static/image3.png)
3. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt, vyberte **přidat | Třída rozbočovače SignalR (v2)**. Název třídy **ChatHub.cs** a přidejte ho do projektu. Tento krok vytvoří **ChatHub** třídy a přidá do projektu sadu souborů skriptů a odkazy na sestavení, podporující funkci SignalR.

    > [!NOTE]
    > Funkce SignalR můžete také přidat do projektu tak, že otevřete **nástroje > Správce balíčků NuGet > Konzola správce balíčků** a spuštění příkazu:

    `install-package Microsoft.AspNet.SignalR`

    Pokud používáte konzolu pro přidání SignalR, vytvořte třída rozbočovače SignalR jako samostatný krok po přidání SignalR.

    > [!NOTE]
    > Pokud používáte sadu Visual Studio 2012, **třída rozbočovače SignalR (v2)** šablonu nebude k dispozici. Můžete přidat prostého **třídy** volá `ChatHub` místo.
4. V **Průzkumníka řešení**, rozbalte uzel skripty. Jsou viditelné v projektu knihovny skriptů pro knihovny jQuery a SignalR.
5. Nahraďte kód v novém **ChatHub** třídy následujícím kódem.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]
6. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **přidat | Třídy pro spuštění OWIN**. Pojmenujte novou třídu `Startup` a klikněte na tlačítko OK.

    > [!NOTE]
    > Pokud používáte sadu Visual Studio 2012, **třídy pro spuštění OWIN** šablonu nebude k dispozici. Můžete přidat prostého **třídy** volá `Startup` místo.
7. Změňte obsah nová třída při spuštění následující.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample2.cs)]
8. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **přidat | Stránka HTML**. Zadejte název nové stránky `index.html`.
    >[!NOTE]
    >Možná budete muset změnit čísla verzí pro odkazy na knihovny JQuery a technologie SignalR
9. V **Průzkumníka řešení**, klikněte pravým tlačítkem na stránku HTML, který jste právě vytvořili a klikněte na tlačítko **nastavit jako úvodní stránku**.
10. Nahraďte kód výchozí stránku HTML s následujícím kódem.

    > [!NOTE]
    > Novější verzi SignalR skriptů, které mohou být nainstalovány službou Správce balíčků. Ověřte, že odkazy na skript níže odpovídají verzi souborů skript v projektu (budou lišit, když jste přidali pomocí nástroje NuGet a místo přidávání rozbočovači SignalR.)

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample3.html)]
11. **Uložit vše** pro projekt.

<a id="run"></a>

## <a name="run-the-sample"></a>Spuštění ukázky

1. Stisknutím klávesy F5 spusťte projekt v režimu ladění. HTML stránka načte v instanci prohlížeče a vyzve k zadání uživatelského jména.

    ![Zadejte uživatelské jméno.](tutorial-getting-started-with-signalr/_static/image4.png)
2. Zadejte uživatelské jméno.
3. Zkopírujte adresu URL v adresním řádku prohlížeče a můžete otevřít dvě další instance prohlížeče. V každé instanci prohlížeče zadejte jedinečné uživatelské jméno.
4. V každé instanci prohlížeče, přidejte komentář a klikněte na tlačítko **odeslat**. Zobrazit komentáře ve všech instancích prohlížeče.

    > [!NOTE]
    > Tento jednoduchý chatovací aplikaci nespravuje kontext diskuse na serveru. Centrum vysílá poznámky pro všechny aktuálního uživatele. Uživatelé, kteří později připojit chat uvidí zprávy přidány od okamžiku, že se že připojí.

    Následující snímek obrazovky ukazuje chatovací aplikaci spuštěnou v tři instance prohlížeče, které aktualizují po jedné instance odešle zprávu:

    ![Chat prohlížeče](tutorial-getting-started-with-signalr/_static/image5.png)
5. V **Průzkumníka řešení**, zkontrolujte **dokumenty skriptu** uzel pro běžící aplikaci. Existuje soubor skriptu s názvem **rozbočovače** generující knihovně SignalR dynamicky za běhu. Tento soubor skladuje komunikaci mezi jQuery skriptu a kódem na straně serveru.

    ![](tutorial-getting-started-with-signalr/_static/image6.png)

<a id="code"></a>

## <a name="examine-the-code"></a>Prozkoumejte kód

Chatovací aplikace SignalR ukazuje dvě základní úkoly vývoje SignalR: vytváří se centrum jako objekt hlavního koordinace na serveru a pomocí knihovny jQuery SignalR k odesílání a příjem zpráv.

### <a name="signalr-hubs"></a>Rozbočovače SignalR

V ukázce kódu **ChatHub** třída odvozena z **Microsoft.AspNet.SignalR.Hub** třídy. Odvozování z **centra** třída je užitečný způsob, jak vytvořit aplikaci SignalR. Můžete vytvořit veřejné metody ve třídě centra a potom tyto metody přístup k jejich voláním z skripty na webové stránce.

V kódu, konverzace, klienti volání **ChatHub.Send** metoda odesílá nová zpráva. Centrum pak odešle zprávu pro všechny klienty voláním **Clients.All.broadcastMessage**.

**Odeslat** metoda ukazuje několik konceptů hub:

- Deklarujte veřejné metody v rozbočovači, tak, aby klienti mohou volat.
- Použití **Microsoft.AspNet.SignalR.Hub.Clients** dynamických vlastností pro přístup k všechny klienty připojené pro toto centrum.
- Volání funkce na straně klienta (například `broadcastMessage` funkce) aktualizovat klienty.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample4.cs)]

### <a name="signalr-and-jquery"></a>SignalR a jQuery

Na stránce HTML ve vzorovém kódu ukazuje, jak používat knihovny jQuery SignalR pro komunikaci se rozbočovače SignalR. Proxy server tak, aby odkazovaly rozbočovače, deklarace funkce, která můžete volat na serveru, abyste předávaný obsah pro klienty a spouští se připojení k odeslání zprávy do centra jsou deklarace základních úloh v kódu.

Následující kód deklaruje odkaz na proxy server rozbočovače.

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample5.js)]

> [!NOTE]
> V jazyce JavaScript je odkaz na třídu serveru a jeho členy v stylem camel case. Odkazuje na vzorový kód jazyka C# **ChatHub** třídy v jazyce JavaScript jako **chatHub**.


Následující kód je, jak vytvořit funkci zpětného volání ve skriptu. Třída rozbočovače na serveru volá tuto funkci tak, aby nabízel obsah aktualizací pro jednotlivé klienty. Následující dva řádky, že s kódováním HTML obsah před jeho zobrazení jsou volitelné a zobrazit jednoduchý způsob, jak brání injektáži skriptu.

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample6.html)]

Následující kód ukazuje, jak otevřít připojení v centru. Kód spustí připojení a pak ji předá funkci pro zpracování události kliknutí na **odeslat** tlačítko na stránce HTML.

> [!NOTE]
> Tento přístup zajišťuje, že připojení před provedením obslužná rutina události.


[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample7.js)]

<a id="next"></a>

## <a name="next-steps"></a>Další kroky

Jste zjistili, že SignalR je architektura určená k vytváření aplikací webu v reálném čase. Také jste se naučili několik úloh vývoje SignalR: jak přidat do aplikace ASP.NET SignalR, jak vytvořit třídu centra a jak odesílat a přijímat zprávy z centra.

Návod k nasazení ukázkové aplikace SignalR pro Azure najdete v tématu [pomocí SignalR s webovými aplikacemi ve službě Azure App Service](../deployment/using-signalr-with-azure-web-sites.md). Podrobné informace o tom, jak nasadit webový projekt sady Visual Studio na webu Windows Azure naleznete v tématu [vytvoření webové aplikace ASP.NET ve službě Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).

Informace o pokročilejších pojmech vývoj SignalR, naleznete na následujících stránkách pro funkci SignalR zdrojový kód a prostředky:

- [Projekt SignalR](http://signalr.net)
- [Funkce SignalR Githubu a ukázky](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)
