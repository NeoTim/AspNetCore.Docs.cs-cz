---
uid: signalr/overview/getting-started/real-time-web-applications-with-signalr
title: 'Praktické cvičení: Aplikace webu v reálném čase s knihovnou SignalR | Dokumentace Microsoftu'
author: bradygaster
description: Webové aplikace v reálném čase funkcí možnost na straně serveru nabízet obsah připojeným klientům, jakmile k ní dojde, v reálném čase. Pro vývojáře využívající technologii ASP.NET, ASP...
ms.author: bradyg
ms.date: 07/16/2014
ms.assetid: ba07958c-42e1-4da0-81db-ba6925ed6db0
msc.legacyurl: /signalr/overview/getting-started/real-time-web-applications-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: d4998c8b739b4b1a06699a17464a7399a87a8595
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837504"
---
<a name="hands-on-lab-real-time-web-applications-with-signalr"></a>Praktické cvičení: Webové aplikace v reálném čase s knihovnou SignalR
====================

podle [Campy Web týmu](https://twitter.com/webcamps)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[Stáhněte si Web Campy školení Kit](http://aka.ms/webcamps-training-kit)

> Webové aplikace v reálném čase funkcí možnost na straně serveru nabízet obsah připojeným klientům, jakmile k ní dojde, v reálném čase. Pro vývojáře využívající technologii ASP.NET **funkce SignalR technologie ASP.NET** je knihovny k přidání funkcí v reálném čase do svých aplikací. Využívá několik přenosů, automaticky výběr nejlepší k dispozici přenos klienta a serveru nejlépe k dispozici přenos. Využívá **protokolu WebSocket**, rozhraní API HTML5, které umožňuje obousměrnou komunikaci mezi prohlížečem a serverem.
> 
> **Funkce SignalR** také poskytuje jednoduché rozhraní API vysoké úrovně pro provádění serveru na klienta vzdáleného volání Procedur (volají funkce JavaScript v prohlížečích vašich klientů z kódu .NET na straně serveru) v aplikaci ASP.NET, jakož i přidáním užitečné háky pro správu připojení například události připojení/odpojení, seskupování připojení a autorizaci.
> 
> **Funkce SignalR** je abstrakcí přes některé přenosy, které jsou potřeba k práci v reálném čase mezi klientem a serverem. A **SignalR** připojení se spustí jako HTTP a pak je povýšen na **protokolu WebSocket** připojení, pokud je k dispozici. **Protokol WebSocket** je ideální přenos pro **SignalR**, protože je nejefektivnější využití paměti serveru má nejnižší latenci a má nejvíce základní funkce (jako je například plně duplexní komunikace mezi klientem a Server), ale má také nejpřísnějšími požadavky na: **Protokol WebSocket** vyžaduje, aby používat server **systému Windows Server 2012** nebo **Windows 8**, spolu s **rozhraní .NET Framework 4.5**. Pokud tyto požadavky nejsou splněny, **SignalR** se pokusí použít další přenosy, aby jeho připojení (například *Ajax dlouhý interval dotazování*).
> 
> **SignalR** rozhraní API obsahuje dva modely pro komunikaci mezi klienty a servery: **Trvalá připojení** a **rozbočovače**. A **připojení** představuje jednoduchý koncový bod pro odesílání jednoho příjemce, seskupené nebo zpráv všesměrového vysílání. A **centra** více základní kanál postavené na rozhraní API připojení, které umožňuje klientem a serverem pro volání metod na sobě navzájem přímo.
> 
> ![Architektura SignalR](real-time-web-applications-with-signalr/_static/image1.png)
> 
> Všechny ukázky kódu a fragmenty kódu jsou součástí této webové Campy školicí sady, k dispozici na [ http://aka.ms/webcamps-training-kit ](http://aka.ms/webcamps-training-kit).


<a id="Overview"></a>
## <a name="overview"></a>Přehled

<a id="Objectives"></a>
### <a name="objectives"></a>Cíle

V této praktická cvičení se dozvíte, jak:

- Odesílání oznámení ze serveru do klienta pomocí funkce SignalR.
- Škálování aplikace SignalR pomocí **systému SQL Server**.

<a id="Prerequisites"></a>
### <a name="prerequisites"></a>Požadavky

K dokončení této praktické testovací prostředí jsou vyžadovány následující položky:

- [Visual Studio Express 2013 for Web](https://www.microsoft.com/visualstudio/) nebo vyšší

<a id="Setup"></a>
### <a name="setup"></a>Instalace

Chcete-li spustit praktická cvičení v této praktické testovací prostředí, musíte nejdřív nastavit prostředí.

1. Otevřete okno Průzkumníka Windows a přejděte do testovacího prostředí **zdroj** složky.
2. Klikněte pravým tlačítkem na **Setup.cmd** a vyberte **spustit jako správce** ke spuštění procesu instalace, který bude konfiguraci prostředí a nainstalujte Visual Studio fragmenty kódu pro toto testovací prostředí.
3. Pokud se zobrazí dialogové okno Řízení uživatelských účtů, zkontrolujte akce, aby bylo možné pokračovat.

> [!NOTE]
> Ujistěte se, že jste zaškrtli všechny závislosti pro toto testovací prostředí před spuštěním instalace.


<a id="CodeSnippets"></a>
### <a name="using-the-code-snippets"></a>Používání fragmentů kódu

V celém dokumentu testovacího prostředí budete vyzváni k vložení bloky kódu. Pro usnadnění práce většina tento kód je k dispozici jako Visual Studio fragmenty kódu, který se dá dostat z v rámci Visual Studio 2013, abyste ho nemuseli znovu přidat ručně.

> [!NOTE]
> Každý cvičení se sadou počáteční řešení nachází v **začít** složky výkonu, který umožňuje postupovat podle jednotlivých výkon nezávisle na ostatních. Uvědomte si, že chybí z těchto řešení od fragmenty kódu, které se přidávají během cvičení a nemusí fungovat, dokud nedokončíte výkonu. Uvnitř zdrojový kód pro cvičení, můžete také najdete **End** složku, která obsahuje řešení sady Visual Studio s kódem, který je výsledkem dokončení kroků v odpovídající cvičení. Tato řešení můžete použít jako vodítko, pokud potřebujete další pomoc při práci prostřednictvím této praktické vyzkoušení.


* * *

<a id="Exercises"></a>
## <a name="exercises"></a>Cvičení

Toto praktické testovací prostředí obsahuje následující praktická cvičení:

1. [Práce s daty v reálném čase s použitím SignalR](#Exercise1)
2. [Horizontální navýšení kapacity pomocí SQL serveru](#Exercise2)

Odhadovaný čas dokončení tohoto testovacího prostředí: **60 minut**

> [!NOTE]
> Při prvním spuštění sady Visual Studio, musíte vybrat jednu z předdefinovaných nastavení kolekce. Každé předdefinované kolekce je navržená tak, aby odpovídala konkrétním vývojářským styl a určuje rozložení oken, chování editoru, fragmenty kódu technologie IntelliSense a možnosti dialogového okna. Postupy v tomto testovacím prostředí jsou uvedené akce potřebné k provedení dané úlohy v sadě Visual Studio při použití **obecným vývojovým nastavením** kolekce. Pokud se rozhodnete různá nastavení kolekce pro vaše vývojové prostředí, mohou existovat rozdíly v krocích, které byste měli vzít v úvahu.


<a id="Exercise1"></a>
### <a name="exercise-1-working-with-real-time-data-using-signalr"></a>Cvičení 1: Práce s daty v reálném čase s použitím SignalR

Když jako příklad se často používá chatu, vám pomůžou celek mnoho dalších akcí s funkcí Web v reálném čase. Když uživatel aktualizuje na webové stránce Nová data nebo implementuje stránky Ajax dlouhý interval dotazování pro načtení nových dat, můžete použít SignalR.

Podporuje SignalR **server nabízených** nebo **všesměrové vysílání** funkce; automaticky zpracovává připojení správy. V klasickém připojení protokolu HTTP pro komunikaci klient server se připojení znovu naváže pro každý požadavek, ale funkce SignalR poskytuje trvalé připojení mezi klientem a serverem. V knihovně SignalR, která do kódu serveru zdůrazňuje pro klientský kód v prohlížeči pomocí vzdáleného volání procedur (RPC) ne model typu žádost odpověď víme ještě dnes.

V tomto cvičení, můžete nakonfigurovat **kvíz Informatik** aplikace používat funkci SignalR k zobrazení řídicího panelu statistiky o aktualizované metriky bez nutnosti aktualizovat celou stránku.

<a id="Ex1Task1"></a>
#### <a name="task-1--exploring-the-geek-quiz-statistics-page"></a>Úloha 1 – zkoumání stránce Statistika kvíz Informatik

V této úloze budete projít aplikace a zkontrolujte, jak je znázorněno na stránce Statistika a jak můžete zvýšit tak, jak informace se aktualizuje.

1. Otevřete **Visual Studio Express 2013 for Web** a otevřete **GeekQuiz.sln** řešení nachází v **Source\Ex1 WorkingWithRealTimeData\Begin** složky.
2. Stisknutím klávesy **F5** ke spuštění řešení. **Přihlášení** stránka by se měla zobrazit v prohlížeči.

    ![Spuštění řešení](real-time-web-applications-with-signalr/_static/image2.png "spuštění řešení")

    *Spuštění řešení*
3. Klikněte na tlačítko **zaregistrovat** v pravém horním rohu stránky vytvořte nového uživatele v aplikaci.

    ![Zaregistrujte odkaz](real-time-web-applications-with-signalr/_static/image3.png "odkaz registrovat")

    *Odkaz registrovat*
4. V **zaregistrovat** stránky, zadejte **uživatelské jméno** a **heslo**a potom klikněte na tlačítko **zaregistrovat**.

    ![Registrace uživatele](real-time-web-applications-with-signalr/_static/image4.png "registrace uživatele")

    *Registrace uživatele*
5. Aplikace registruje nový účet a uživatel je ověřený a přesměrován zpět na domovskou stránku zobrazující první otázku kvízu s časovým limitem.
6. Otevřít **statistiky** stránce v novém okně a umístí **Domů** stránky a **statistiky** stránky vedle sebe.

    ![Vedle sebe windows](real-time-web-applications-with-signalr/_static/image5.png "souběžně na straně windows")

    *Windows vedle sebe*
7. V **Domů** stránce, odpověď na otázku, kliknutím na jednu z možností.

    ![Odpovídání na otázku](real-time-web-applications-with-signalr/_static/image6.png "odpovídání na otázku")

    *Odpovídání na otázku*
8. Po kliknutí na jedno z tlačítek, by se zobrazit odpověď.

    ![Zodpovězené otázky správné](real-time-web-applications-with-signalr/_static/image7.png "otázku odpovědi správné")

    *Správně zodpovězení dotazu*
9. Všimněte si, že informace uvedené na stránce Statistika je zastaralý. Aktualizujte stránku, aby bylo možné zobrazit aktualizované výsledky.

    ![Stránka statistiky](real-time-web-applications-with-signalr/_static/image8.png "stránka statistiky")

    *Stránka statistiky*
10. Přejděte zpět do sady Visual Studio a Zastavit ladění.

<a id="Ex1Task2"></a>
#### <a name="task-2--adding-signalr-to-geek-quiz-to-show-online-charts"></a>Úloha 2 – Přidání funkce SignalR pro nadšence testu můžete zobrazit Online grafy

V této úloze budou do řešení přidat SignalR a odeslání aktualizací pro klienty automaticky při odeslání nové odpovědi na server.

1. Z **nástroje** v aplikaci Visual Studio, vyberte v nabídce **Správce balíčků NuGet**a potom klepněte na **konzoly Správce balíčků**.
2. V **Konzola správce balíčků** okno, že spustíte následující příkaz:

    [!code-powershell[Main](real-time-web-applications-with-signalr/samples/sample1.ps1)]

    ![Instalace balíčku SignalR](real-time-web-applications-with-signalr/_static/image9.png "instalace balíčku SignalR")

    *Instalace balíčku SignalR*

   > [!NOTE]
   > Při instalaci **SignalR** NuGet balíčky verzí bodu 2.0.2 od zcela nové aplikace MVC 5, budete muset ručně aktualizovat **OWIN** balíčky na verzi 2.0.1 (nebo novější) před instalací funkce SignalR. K tomuto účelu můžete spustit následující skript v **Konzola správce balíčků**:
   > 
   > [!code-powershell[Main](real-time-web-applications-with-signalr/samples/sample2.ps1)]
   > 
   > V budoucí verzi SignalR OWIN závislosti automaticky aktualizují.
3. V **Průzkumníku řešení**, rozbalte **skripty** složky a Všimněte si, které funkce SignalR *js* soubory byly přidány k řešení.

    ![Odkazuje na funkci SignalR JavaScript](real-time-web-applications-with-signalr/_static/image10.png "odkazuje SignalR JavaScript")

    *Odkazuje na funkci SignalR JavaScript*
4. V **Průzkumníku řešení**, klikněte pravým tlačítkem myši **GeekQuiz** projekt, vyberte **přidat** | **novou složku**a pojmenujte ho  **Rozbočovače**.
5. Klikněte pravým tlačítkem myši **rozbočovače** a pak zvolte položku **přidat | Nová položka**.

    ![Přidat novou položku](real-time-web-applications-with-signalr/_static/image11.png "přidat novou položku")

    *Přidat novou položku*
6. V **přidat novou položku** dialogové okno, vyberte **Visual C# | Web | Funkce SignalR** uzlu v levém podokně vyberte **třída rozbočovače SignalR (v2)** v prostředním podokně zadejte název souboru **StatisticsHub.cs** a klikněte na tlačítko **přidat**.

    ![Přidat novou položku dialogové okno](real-time-web-applications-with-signalr/_static/image12.png "přidat novou položku dialogové okno")

    *Přidat novou položku – dialogové okno*
7. Nahraďte kód v **StatisticsHub** třídy následujícím kódem.

    (Fragment - kódu *RealTimeSignalR - Ex1 - StatisticsHubClass*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample3.cs)]
8. Otevřít **Startup.cs** a přidejte následující řádek na konci **konfigurace** metody.

    (Fragment - kódu *RealTimeSignalR - Ex1 - MapSignalR*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample4.cs)]
9. Otevřít **StatisticsService.cs** stránky uvnitř **služby** složky a přidejte následující direktivy using.

    (Fragment - kódu *RealTimeSignalR - Ex1 - UsingDirectives*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample5.cs)]
10. K upozornění klientů připojených aktualizací, můžete nejdřív načtěte **kontextu** objektů pro aktuální připojení. **Centra** objekt obsahuje metody pro odesílání zpráv do jednoho klienta nebo všesměrového vysílání na všechny připojené klienty. Přidejte následující metodu do **StatisticsService** třídy k vysílání statistická data.

    (Fragment - kódu *RealTimeSignalR - Ex1 - NotifyUpdatesMethod*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample6.cs)]

    > [!NOTE]
    > Ve výše uvedeném kódu, jsou pomocí názvu libovolného metoda k volání funkce na straně klienta (například: *updateStatistics*). Název metody, který zadáte, je interpretován jako dynamický objekt, což znamená, že odpadá technologie IntelliSense a ověření za kompilace pro něj. Výraz je vyhodnocen v době běhu. Při volání metody, které se spustí, odešle SignalR názvu metody a hodnoty parametrů do klienta. Pokud má klient metodu, která odpovídá názvu, tato metoda je volána a hodnoty parametru jsou předány do něj. Pokud na straně klienta se nenašla žádná odpovídající metoda, je vyvolána žádná chyba. Další informace najdete v [pokyny k rozhraní API Center SignalR technologie ASP.NET](../guide-to-the-api/hubs-api-guide-server.md).
11. Otevřít **TriviaController.cs** stránky uvnitř **řadiče** složky a přidejte následující direktivy using.

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample7.cs)]
12. Přidejte následující zvýrazněný kód do **příspěvek** metody akce.

    (Fragment - kódu *RealTimeSignalR - Ex1 - NotifyUpdatesCall*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample8.cs)]
13. Otevřít **Statistics.cshtml** stránky uvnitř **zobrazení | Domů** složky. Vyhledejte **skripty** a přidejte následující odkazy na skript na začátku části.

    (Fragment - kódu *RealTimeSignalR - Ex1 - SignalRScriptReferences*)

    [!code-cshtml[Main](real-time-web-applications-with-signalr/samples/sample9.cshtml)]

    > [!NOTE]
    > Když přidáte SignalR a další skript knihovny do projektu sady Visual Studio, může správce balíčků nainstalovat verzi souboru skriptu SignalR, která je novější než verze uvedené v tomto tématu. Ujistěte se, že odkaz na skript v kódu odpovídá verzi nainstalovanou ve vašem projektu knihovnu skriptu.
14. Přidejte následující zvýrazněný kód k připojení klienta k rozbočovači SignalR a aktualizaci statistiky dat při přijetí nové zprávy z centra.

    (Fragment - kódu *RealTimeSignalR - Ex1 - SignalRClientCode*)

    [!code-cshtml[Main](real-time-web-applications-with-signalr/samples/sample10.cshtml)]

    V tomto kódu jsou vytváření proxy server rozbočovače a registraci obslužné rutiny události k naslouchání pro zprávy odeslané serverem. V takovém případě budete přijímat zprávy odeslané přes *updateStatistics* metody.

<a id="Ex1Task3"></a>
#### <a name="task-3--running-the-solution"></a>Úloha 3 – spuštění řešení

V této úloze budete spouštět řešení Chcete-li ověřit, že se aktualizuje zobrazení statistiky automaticky pomocí nástroje SignalR po zvolení odpovědi novou otázku.

1. Stisknutím klávesy **F5** ke spuštění řešení.

    > [!NOTE]
    > Pokud už nejste přihlášení k aplikaci, přihlaste se pomocí uživatele, kterého jste vytvořili v úloze 1.
2. Otevřít **statistiky** stránce v novém okně a umístí **Domů** stránky a **statistiky** stránky vedle sebe, jako jste to udělali v úloze 1.
3. V **Domů** stránce, odpověď na otázku, kliknutím na jednu z možností.

    ![Odpovědi na jinou otázku](real-time-web-applications-with-signalr/_static/image13.png "odpovídání na další otázku")

    *Odpovědi na jinou otázku*
4. Po kliknutí na jedno z tlačítek, by se zobrazit odpověď. Všimněte si, že statistické informace na stránce se aktualizuje automaticky po zadání odpovědi na otázku aktualizované informace bez nutnosti aktualizovat celou stránku.

    ![Stránka statistiky aktualizují po provedení odpovědí](real-time-web-applications-with-signalr/_static/image14.png "stránka statistiky aktualizují po provedení odpovědí")

    *Statistiky stránku aktualizovat za odpovědí*

<a id="Exercise2"></a>
### <a name="exercise-2-scaling-out-using-sql-server"></a>Cvičení 2: Horizontální navýšení kapacity pomocí SQL serveru

Při škálování webové aplikace, je obecně možné mezi *vertikální navýšení kapacity* a *horizontální navýšení kapacity* možnosti. *Vertikálně navýšit kapacitu* znamená větší server pomocí více prostředků (procesoru, paměti RAM, atd.) při *horizontální navýšení kapacity* znamená, že přidáte další servery pro zpracování zátěže. Problém s ten je, že klienti můžete získat směrovat na různé servery. Klient, který je připojený k jednomu serveru nebude přijímat zprávy odeslané z jiného serveru.

Tyto problémy můžete vyřešit použitím komponenty s názvem *propojovací rozhraní systému*, aby předával zprávy mezi servery. Propojovací rozhraní povolená každá instance aplikace odesílá zprávy do propojovacího rozhraní a propojovacího rozhraní předává je do jiné instance aplikace.

Aktuálně existují tři druhy backplanes pro funkci SignalR:

- **Windows Azure Service Bus**. Service Bus je infrastruktura zasílání zpráv, který umožňuje součástem odesílat volně zprávy.
- **SQL Server**. Propojovací rozhraní systému SQL Server zapisuje zprávy do tabulky SQL. Propojovacího rozhraní používá pro efektivní zasílání zpráv služby Service Broker. Ale to funguje také v případě služby Service Broker není povolená.
- **Redis**. Redis je úložiště klíč / hodnota v paměti. Redis podporuje vzorec publikovat/odebírat ("pub/sub") pro odesílání zpráv.

Každá zpráva se odesílá prostřednictvím sběrnice zpráv. Implementuje sběrnice zpráv [IMessageBus](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.imessagebus(v=vs.100).aspx) rozhraní, které poskytuje abstrakci se publikovat/odebírat. Backplanes fungovat tak, že nahradíte výchozí **IMessageBus** se sběrnicí navržené pro propojovací rozhraní.

Každá instance serveru se připojí k propojovací rozhraní systému přes Service bus. Při odesílání zprávy přejde do propojovacího rozhraní a odesílá je propojovacího rozhraní na každý server. Pokud server přijme zprávu z propojovacího rozhraní, ukládá zprávy v místní mezipaměti. Server poté předává zprávy klientů z místní mezipaměti.

Další informace o tom, jak propojovací rozhraní systému SignalR funguje, najdete v tomto [článku](../performance/scaleout-in-signalr.md).

> [!NOTE]
> Existují některé scénáře, kde propojovací rozhraní se může stát kritickým bodem. Tady jsou některé typické scénáře SignalR:
> 
> - [Server vysílání](tutorial-server-broadcast-with-signalr.md) (například akciích): Backplanes fungovat dobře pro tento scénář, protože rychlost, jakou jsou odesílány zprávy pro ovládací prvky server.
> - [Klient klient](tutorial-getting-started-with-signalr.md) (například konverzace): V tomto scénáři propojovacího rozhraní může být kritickým bodem v případě, že počet zpráv, které se škáluje s počtem klientů; To znamená, pokud roste počet zpráv proporcionálně Další klienti se připojují k.
> - [Vysokofrekvenční Reálný čas](tutorial-high-frequency-realtime-with-signalr.md) (například hry v reálném čase): Propojovací rozhraní se nedoporučuje pro tento scénář.


V tomto cvičení použijete **systému SQL Server** k distribuci zpráv napříč **kvíz Informatik** aplikace. Tyto úlohy poběží na jeden testovací počítač Další informace o nastavení konfigurace, ale pokud chcete získat plný vliv, budete muset nasadit aplikace SignalR pro dva nebo víc serverů. SQL Server musíte nainstalovat také na některý server nebo na samostatný vyhrazený server.

![Horizontální navýšení kapacity pomocí SQL serveru diagramu](real-time-web-applications-with-signalr/_static/image15.png)

<a id="Ex2Task1"></a>
#### <a name="task-1---understanding-the-scenario"></a>Úloha 1 – informace o scénář

V této úloze budete spouštět 2 instance **kvíz Informatik** simulaci IIS více instancí na místním počítači. V tomto scénáři při odpovídání na dotazy triviální prvek na jedné aplikace aktualizace nebudete nijak upozorněni na stránce Statistika druhou instanci. Se podobá této simulaci prostředí, ve kterém je vaše aplikace nasazena na více instancí a použití nástroje pro vyrovnávání zatížení ke komunikaci s nimi.

1. Otevřít **Begin.sln** řešení nachází v **zdroj/Ex2-ScalingOutWithSQLServer/Begin** složky. Po načtení, uvidíte na **Průzkumníka serveru** , řešení obsahuje dva projekty s identické struktury ale různými názvy. To bude simulovat systémem dvě instance stejné aplikace na svém místním počítači.

    ![Zahájit řešení simulaci 2 instance Informatik kvíz](real-time-web-applications-with-signalr/_static/image16.png "zahájit řešení simulaci 2 instance kvíz Informatik")

    *Zahájit řešení simulaci 2 instance kvíz Informatik*
2. Otevřete stránku vlastností řešení tak, že kliknete pravým tlačítkem uzel řešení a vyberete **vlastnosti**. V části **spouštěný projekt**vyberte **více projektů po spuštění** a změnit **akce** hodnoty pro oba projekty do *Start*.

    ![Spuštění více projektů](real-time-web-applications-with-signalr/_static/image17.png "spuštění více projektů")

    *Spuštění více projektů*
3. Stisknutím klávesy **F5** ke spuštění řešení. Aplikace se spustí dvě instance **kvíz Informatik** v jiné porty, které simulují několik instancí stejné aplikace. Připne jeden z těchto prohlížečů na levé straně a jiné na pravé straně obrazovky. Přihlaste se pomocí svých přihlašovacích údajů nebo registraci nového uživatele. Po přihlášení zachovat triviální prvek stránky na levé straně a přejděte **statistiky** stránku v prohlížeči na pravé straně.

    ![Informatik kvíz vedle sebe](real-time-web-applications-with-signalr/_static/image18.png)

    *Informatik kvíz vedle sebe*

    ![Informatik kvíz v jiné porty](real-time-web-applications-with-signalr/_static/image19.png)

    *Informatik kvíz v jiné porty*
4. Spustit zodpovězení otázek v levém prohlížeče a zjistíte, že **statistiky** stránku v prohlížeči správné není aktualizován. Důvodem je, že **SignalR** použití místní mezipaměti k distribuci zpráv do svých klientech a v tomto scénáři je budete jen simulovat více instancí, proto není mezi nimi sdílet mezipaměť. Můžete ověřit, že **SignalR** funguje, testování stejným způsobem, ale pomocí jedné aplikace. V následujících úloh můžete nakonfigurovat propojovacího rozhraní k replikaci zprávy napříč instancemi.
5. Přejděte zpět do sady Visual Studio a Zastavit ladění.

<a id="Ex2Task2"></a>
#### <a name="task-2--creating-the-sql-server-backplane"></a>Úloha 2 – Vytvoření propojovací rozhraní systému serveru SQL

V této úloze se vytvoří databáze, která bude sloužit jako propojovací rozhraní pro **kvíz Informatik** aplikace. Budete používat **Průzkumník objektů systému SQL Server** procházet váš server a inicializovat v databázi. Kromě toho vám umožní **služby Service Broker**.

1. V **sady Visual Studio**, otevřete nabídku **zobrazení** a vyberte **Průzkumník objektů systému SQL Server**.
2. Připojte se k instanci LocalDB kliknutím pravým tlačítkem myši **systému SQL Server** uzlu a vyberete **přidat SQL Server...**  možnost.

    ![Přidání Instance serveru SQL](real-time-web-applications-with-signalr/_static/image20.png "přidání Instance serveru SQL")

    *Přidání instance serveru SQL Server do Průzkumník objektů systému SQL Server*
3. Nastavte **název serveru** k *(localdb) \v11.0* a nechat **ověřování Windows** jako režim ověřování. Klikněte na tlačítko **připojit** pokračujte.

    ![Připojování na instanci LocalDB](real-time-web-applications-with-signalr/_static/image21.png "připojení na instanci LocalDB")

    *Připojování na instanci LocalDB*
4. Teď, když jste připojení k vaší instanci LocalDB, je potřeba vytvořit databázi, která bude představovat propojovací rozhraní systému SQL Server pro funkci SignalR. Chcete-li to provést, klikněte pravým tlačítkem **databází** uzel a vyberte možnost **přidat novou databázi**.

    ![Přidání nové databáze](real-time-web-applications-with-signalr/_static/image22.png "přidávání nové databáze")

    *Přidání nové databáze*
5. Nastavte název databáze *SignalR* a klikněte na tlačítko **OK** k jeho vytvoření.

    ![Vytváří se databáze SignalR](real-time-web-applications-with-signalr/_static/image23.png "vytváří se databáze SignalR")

    *Vytváří se databáze SignalR*

    > [!NOTE]
    > Můžete zvolit libovolný název databáze.
6. Efektivněji přijímat aktualizace z propojovacího rozhraní, se doporučuje povolí službu Service Broker pro databázi. Služba Service Broker poskytuje nativní podporu pro zasílání zpráv nebo řazení do fronty v systému SQL Server. Propojovacího rozhraní také funguje bez služby Service Broker. Otevřete nové okno dotazu kliknutím pravým tlačítkem myši na databázi a vyberte **nový dotaz**.

    ![Otevřete nový dotaz](real-time-web-applications-with-signalr/_static/image24.png "otevřete nový dotaz")

    *Otevřete nový dotaz*
7. Pokud chcete zkontrolovat, zda je povolena služba Service Broker, dotazování **je\_zprostředkovatele\_povolené** sloupec v **zobrazení sys.databases** zobrazení katalogu. V okně naposledy otevřeným dotazu spusťte následující skript.

    [!code-sql[Main](real-time-web-applications-with-signalr/samples/sample11.sql)]

    ![Dotazování na stav služby Service Broker](real-time-web-applications-with-signalr/_static/image25.png "dotazování na stav služby Service Broker")

    *Dotazování na stav služby Service Broker*
8. Pokud hodnota **je\_zprostředkovatele\_povolené** sloupec v databázi je &quot;0&quot;, použijte následující příkaz, aby je. Nahraďte **&lt;YOUR DATABASE&gt;** s názvem, který jste nastavili při vytváření databáze (například: Funkce SignalR).

    [!code-sql[Main](real-time-web-applications-with-signalr/samples/sample12.sql)]

    ![Povolení služby Service Broker](real-time-web-applications-with-signalr/_static/image26.png "povolení služby Service Broker")

    *Povolení služby Service Broker*

    > [!NOTE]
    > Pokud tento dotaz se zdá, zablokování, ujistěte se, že nejsou žádné aplikace, připojení k databázi.

<a id="Ex2Task3"></a>
#### <a name="task-3--configuring-the-signalr-application"></a>Úloha 3 – konfigurace aplikace SignalR

V této úloze nakonfigurujete **kvíz Informatik** pro připojení k propojovací rozhraní systému SQL Server. Nejprve přidejte **SignalR.SqlServer** balíček NuGet a sady připojení řetězec k databázi propojovacího rozhraní.

1. Otevřít **Konzola správce balíčků** z **nástroje** > **Správce balíčků NuGet**. Ujistěte se, že **GeekQuiz** projekt určený v **výchozí projekt** rozevíracího seznamu. Zadejte následující příkaz k instalaci **Microsoft.AspNet.SignalR.SqlServer** balíček NuGet.

    [!code-powershell[Main](real-time-web-applications-with-signalr/samples/sample13.ps1)]
2. Opakujte předchozí krok, ale tentokrát pro projekt **GeekQuiz2**.
3. Chcete-li konfigurovat propojovací rozhraní systému SQL Server, otevřete **Startup.cs** soubor **GeekQuiz** projekt a přidejte následující kód, který **konfigurovat** – metoda. Nahraďte **&lt;YOUR DATABASE&gt;** názvem vaší databáze, jste použili při vytváření propojovací rozhraní systému SQL Server. Opakujte tento krok pro **GeekQuiz2** projektu.

    (Fragment - kódu *RealTimeSignalR - Ex2 - StartupConfiguration*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample14.cs)]
4. Teď, když oba projekty jsou nakonfigurovány pro použití propojovací rozhraní systému SQL Server, stiskněte klávesu **F5** je spustit současně.
5. Opět **sady Visual Studio** spustí dvě instance **kvíz Informatik** v jiné porty. Připne jeden z těchto prohlížečů na levé straně a jiné na pravé straně obrazovky a přihlaste se pomocí svých přihlašovacích údajů. Zachovat triviální prvek stránky na levé straně a přejděte na **statistiky** pagein správné prohlížeče.
6. Spuštění, odpovídání na dotazy v levém prohlížeče. Tentokrát **statistiky** aktualizaci stránky díky propojovacího rozhraní. Přepínání mezi aplikacemi (**statistiky** je teď na levé straně a **triviální prvek** je na pravé straně) a opakujte test k ověření, že funguje pro obě instance. Propojovacího rozhraní slouží jako *sdílené mezipaměti* zpráv pro každý připojený server a každý server, které budou ukládat zprávy v místní mezipaměti pro distribuci do připojených klientů.
7. Přejděte zpět do sady Visual Studio a Zastavit ladění.
8. Komponenta propojovací rozhraní systému SQL Server automaticky vytvoří nezbytné tabulky se zadanou databází. V **Průzkumník objektů systému SQL Server** panelu, otevřete databázi, který jste vytvořili pro propojovacího rozhraní (např: Funkce SignalR) a rozbalte jeho tabulek. Měli byste vidět v následující tabulce:

    ![Propojovací rozhraní systému vygenerované tabulky](real-time-web-applications-with-signalr/_static/image27.png)

    *Propojovací rozhraní systému vygenerované tabulky*
9. Klikněte pravým tlačítkem myši **SignalR.Messages\_0** tabulce a vybrat **Data zobrazení**.

    ![Zobrazení tabulky zpráv propojovací rozhraní systému SignalR](real-time-web-applications-with-signalr/_static/image28.png)

    *Zobrazení tabulky zpráv propojovací rozhraní systému SignalR*
10. Zobrazí se různé zprávy odeslané do **centra** při odpovídání na dotazy triviální prvek. Propojovacího rozhraní distribuuje tyto zprávy na jakoukoli instanci připojené.

    ![Tabulka zpráv propojovací rozhraní systému](real-time-web-applications-with-signalr/_static/image29.png)

    *Tabulka zpráv propojovací rozhraní systému*

* * *

<a id="Summary"></a>
## <a name="summary"></a>Souhrn

V této praktické laboratoři jste se naučili, jak přidat **SignalR** do aplikace a odeslat oznámení ze serveru na klienty připojené pomocí **rozbočovače**. Kromě toho jste zjistili, jak pro horizontální navýšení kapacity aplikace s využitím *propojovací rozhraní systému* komponenty při nasazení vaší aplikace ve více instancích služby IIS.
