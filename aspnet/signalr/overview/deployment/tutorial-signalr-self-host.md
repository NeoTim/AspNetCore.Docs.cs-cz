---
uid: signalr/overview/deployment/tutorial-signalr-self-host
title: 'Kurz: SignalR v místním | Dokumentace Microsoftu'
author: bradygaster
description: Tento kurz ukazuje, jak vytvořit v místním prostředí serveru SignalR 2 a jak se připojit k němu pomocí klienta jazyka JavaScript. V tomto kurzu V použili verze softwaru...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 400db427-27af-4f2f-abf0-5486d5e024b5
msc.legacyurl: /signalr/overview/deployment/tutorial-signalr-self-host
msc.type: authoredcontent
ms.openlocfilehash: 6a6359d59a4b715e13fe2bbcef57da6d6d6294b5
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835749"
---
<a name="tutorial-signalr-self-host"></a>Kurz: SignalR v místním prostředí
====================
podle [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[Stáhnout dokončený projekt](http://code.msdn.microsoft.com/SignalR-Self-Host-Sample-6da0f383)

> Tento kurz ukazuje, jak vytvořit v místním prostředí serveru SignalR 2 a jak se připojit k němu pomocí klienta jazyka JavaScript.
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
> ## <a name="questions-and-comments"></a>Otázky a komentáře
>
> Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky. Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).


## <a name="overview"></a>Přehled

SignalR server je obvykle hostované v aplikaci ASP.NET ve službě IIS, ale může být v místním prostředí (například konzolové aplikace nebo služby Windows) pomocí knihovny hostování na vlastním serveru. Tato knihovna, jako jsou všechny funkce SignalR 2, je založená na OWIN ([Open Web Interface pro .NET](http://owin.org)). OWIN definuje abstrakce mezi .NET webové servery a webové aplikace. OWIN odpojí webové aplikace ze serveru, který je ideální pro webové aplikace ve vašem vlastním procesu mimo službu IIS s vlastním hostováním OWIN.

Mezi důvody pro hostování není ve službě IIS patří:

- Prostředí, ve kterém služby IIS není k dispozici nebo není žádoucí, jako je například existující serverové farmy bez služby IIS.
- Nároky na výkon služby IIS je třeba se jim vyhnout.
- Funkce SignalR je přidat do existující instanci služby aplikace, která běží ve službě Windows, rolí pracovního procesu systému Azure nebo jiný proces.

Pokud řešení je vyvíjen jako hostování na vlastním serveru z důvodů výkonu, doporučujeme také testovací aplikace hostované ve službě IIS a určí, zlepšuje výkon.

Tento kurz obsahuje následující části:

- [Vytvoření serveru](#server)
- [Přístup k serveru pomocí jazyka JavaScript klienta](#js)

<a id="server"></a>

## <a name="creating-the-server"></a>Vytvoření serveru

V tomto kurzu vytvoříte server, který je hostován v konzolové aplikaci, ale server je možné hostovat v jakýkoli druh procesu, například služby Windows nebo role pracovního procesu Azure. Ukázkový kód pro hostování serveru SignalR ve službě Windows, naleznete v tématu [Self-Hosting SignalR ve službě Windows](https://code.msdn.microsoft.com/SignalR-self-hosted-in-6ff7e6c3).

1. Otevřete Visual Studio 2013 s oprávněními správce. Vyberte **souboru**, **nový projekt**. Vyberte **Windows** pod **Visual C#** uzlu v **šablony** podokně a vyberte **konzolovou aplikaci** šablony. Název nového projektu "SignalRSelfHost" a klikněte na **OK**.

    ![](tutorial-signalr-self-host/_static/image1.png)
2. Otevřete konzoly Správce balíčků NuGet tak, že vyberete **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.
3. V konzole Správce balíčků zadejte následující příkaz:

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample1.ps1)]

    Tento příkaz přidá do projektu knihovny SignalR 2 Self-Host.
4. V konzole Správce balíčků zadejte následující příkaz:

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample2.ps1)]

    Tento příkaz přidá Microsoft.Owin.Cors knihovny do projektu. Tato knihovna se použije pro podporu napříč doménami, které je potřeba pro aplikace, které jsou hostiteli SignalR a webové stránky klienta v různých doménách. Protože je budete hostování serveru SignalR a webový klient na jiném portu, to znamená, že mezi doménami musí být povolené pro komunikaci mezi těmito součástmi.
5. Nahraďte obsah Program.cs následujícím kódem.

    [!code-csharp[Main](tutorial-signalr-self-host/samples/sample3.cs)]

    Ve výše uvedeném kódu obsahuje tři třídy:

    - **Program**, včetně **hlavní** metoda definování primární cesty spuštění. V této metodě, webové aplikace typu **spuštění** je spuštěn na zadané adrese URL (`http://localhost:8080`). Pro potřeby zabezpečení na koncový bod je možné implementovat SSL. Zobrazit [jak: Konfigurace portu s certifikátem SSL](https://msdn.microsoft.com/library/ms733791.aspx) Další informace.
    - **Po spuštění**, třída obsahující konfiguraci serveru funkce SignalR (Tento kurz používá pouze konfigurace je volání `UseCors`) a volání `MapSignalR`, která vytvoří trasy pro všechny objekty centra v projektu.
    - **MyHub**, třída rozbočovače SignalR, která aplikace bude poskytovat klientům. Tato třída obsahuje jedinou metodu **odeslat**, že klienti zavolá k vysílání zprávy do všech ostatních připojených klientů.
6. Kompilace a spuštění aplikace. Adresa, na kterém běží server by měl zobrazit v okně konzoly.

    ![](tutorial-signalr-self-host/_static/image2.png)
7. Pokud se nezdaří spuštění s výjimkou `System.Reflection.TargetInvocationException was unhandled`, budete muset restartovat Visual Studio s oprávněními správce.
8. Zastavte aplikaci, než budete pokračovat k další části.

<a id="js"></a>

## <a name="accessing-the-server-with-a-javascript-client"></a>Přístup k serveru pomocí jazyka JavaScript klienta

V této části použijete stejného klienta JavaScript z [kurzu Začínáme](../getting-started/tutorial-getting-started-with-signalr.md). Teď uděláme pouze jeden změny klienta, který je explicitně definují adresu URL centra. Pomocí aplikace v místním prostředí serveru nemusí být nutně na stejné adrese jako adresu URL připojení (z důvodu reverzních proxy serverů a nástroje pro vyrovnávání zatížení), tak adresy URL musí být definované explicitně.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na řešení a vyberte **přidat**, **nový projekt**. Vyberte **webové** uzel a vyberte **webová aplikace ASP.NET** šablony. Pojmenujte projekt "JavascriptClient" a klikněte na tlačítko **OK**.

    ![](tutorial-signalr-self-host/_static/image3.png)
2. Vyberte **prázdný** šablony a nechte nevybranou zbývající možnosti. Vyberte **vytvoření projektu**.

    ![](tutorial-signalr-self-host/_static/image4.png)
3. V konzole Správce balíčků, vyberte projekt "JavascriptClient" v **výchozí projekt** rozevíracího seznamu a spusťte následující příkaz:

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample4.ps1)]

    Tento příkaz nainstaluje knihovny SignalR a JQuery, které budete potřebovat v klientovi.
4. Klikněte pravým tlačítkem na projekt a vyberte **přidat**, **nová položka**. Vyberte **webové** uzel a vyberte stránku HTML. Pojmenujte stránku **Default.html**.

    ![](tutorial-signalr-self-host/_static/image5.png)
5. Nahraďte obsah novou stránku HTML s následujícím kódem. Ověřte, že tady skriptových odkazů odpovídají skripty ve složce Scripts projektu.

    [!code-html[Main](tutorial-signalr-self-host/samples/sample5.html?highlight=31-32)]

    Následující kód (zvýrazněné v ukázkovém kódu výše) je, jež jste udělali klient použitý v tomto kurzu Začínáme Stared (navíc k upgradu kód na beta verzi 2 SignalR). Tento řádek kódu explicitně nastaví adresu URL připojení databáze pro funkci SignalR na serveru.

    [!code-javascript[Main](tutorial-signalr-self-host/samples/sample6.js)]
6. Klikněte pravým tlačítkem na řešení a vyberte **nastavit projekty po spuštění...** . Vyberte **více projektů po spuštění** přepínač a nastavte oba projekty **akce** k **Start**.

    ![](tutorial-signalr-self-host/_static/image6.png)
7. Klikněte pravým tlačítkem na "Default.html" a vyberte **nastavit jako úvodní stránku**.
8. Spusťte aplikaci. Spustí se server a stránky. Možná budete muset znovu načíst webové stránky (nebo vyberte **pokračovat** v ladicím programu) Pokud se stránka načte předtím, než je server spuštěn.
9. V prohlížeči zadejte uživatelské jméno, po zobrazení výzvy. Zkopírujte adresu URL stránky v jiném okně nebo kartě prohlížeče a zadejte jiné uživatelské jméno. Budete moct posílat zprávy z podokna jeden prohlížeč do jiné, jako v kurzu Začínáme.
