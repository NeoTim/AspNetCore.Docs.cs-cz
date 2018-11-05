---
uid: aspnet/overview/web-development-best-practices/what-not-to-do-in-aspnet-and-what-to-do-instead
title: Co nedělat v ASP.NET a jak to udělat správně | Dokumentace Microsoftu
author: Rick-Anderson
description: Toto téma popisuje několik běžných chyb, které uživatelé provést v rámci webové projekty ASP.NET. Poskytuje doporučení pro co dělat, aby se zabránilo tyto commo...
ms.author: riande
ms.date: 05/08/2014
ms.assetid: c39b9965-545c-4b04-8f55-21be7f28a9e5
msc.legacyurl: /aspnet/overview/web-development-best-practices/what-not-to-do-in-aspnet-and-what-to-do-instead
msc.type: authoredcontent
ms.openlocfilehash: 69040ca6a1ddeaf029062da45475dd2171b1afa6
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021440"
---
<a name="what-not-to-do-in-aspnet-and-what-to-do-instead"></a>Co nedělat v ASP.NET a jak to udělat správně
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Toto téma popisuje několik běžných chyb, které uživatelé provést v rámci webové projekty ASP.NET. Poskytuje doporučení pro co dělat, aby se zabránilo těchto běžných chyb. Je založen na [prezentace](http://vimeo.com/68390507) podle **Damianem Edwardsem** na konferenci Ndc vývojáři.


## <a name="disclaimer"></a>Právní omezení

Toto téma není určené úplnou příručku k zajištění, že aplikace je zabezpečené a efektivní. Stále musíte dodržovat osvědčené postupy pro zabezpečení a výkonu, které nejsou uvedené v tomto tématu. Navrhne pouze jak se vyhnout běžné chyby týkající se třídy rozhraní .NET a procesy.

## <a name="overview"></a>Přehled

Toto téma obsahuje následující oddíly:

- [Standardy dodržování předpisů](#standards)

    - [Adaptérů ovládacích prvků](#adapters)
    - [Vlastnosti stylu v ovládacích prvcích](#styleprop)
    - [Stránky a zpětná volání ovládacího prvku](#callback)
    - [Zjišťování schopností prohlížeče](#browsercap)
- [Zabezpečení](#security)

    - [Žádost o ověření](#validation)
    - [Ověřování pomocí formulářů bez souborů cookie a relace](#cookieless)
    - [EnableViewStateMac](#viewstatemac)
    - [Úrovni Medium Trust](#medium)
    - [&lt;appSettings&gt;](#appsettings)
    - [UrlPathEncode](#urlpathencode)
- [Spolehlivost a výkon](#performance)

    - [PreSendRequestHeaders a PreSendRequestContent](#presend)
    - [Události asynchronní stránky s webovými formuláři](#asyncevents)
    - [Oheň a zapomenout práce](#fire)
    - [Obsah Entity žádosti](#requestentity)
    - [Response.Redirect a metody Response.End](#redirect)
    - [EnableViewState a ViewStateMode](#viewstatemode)
    - [SqlMembershipProvider](#sqlprovider)
    - [Dlouhé žádosti spuštění (> 110 sekund)](#long)

<a id="standards"></a>

## <a name="standards-compliance"></a>Standardy dodržování předpisů

<a id="adapters"></a>

### <a name="control-adapters"></a>Adaptérů ovládacích prvků

Doporučení: Přestat používat adaptérů ovládacích prvků pro adaptivní vykreslování a místo toho použít dotazy na média šablon stylů CSS a HTML standardům.

Ovládací prvky adaptéry byly zavedeny v rozhraní .NET 2.0 k vykreslení prezentaci kódu, který byl přizpůsoben pro různá zařízení a prostředí. Nyní tento adaptivního vykreslování můžete provést pomocí šablon stylů CSS a HTML. Měli přestat používat adaptérů ovládacích prvků a převeďte všechny existující adaptéry šablon stylů CSS a HTML.

Další informace najdete v tématu [dotazy na média](http://www.w3.org/TR/css3-mediaqueries/) a [postupy: Přidání mobilní stránky na vaše webové formuláře ASP.NET / aplikace MVC](../../../whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application.md).

<a id="styleprop"></a>

### <a name="style-properties-on-controls"></a>Vlastnosti stylu v ovládacích prvcích

Doporučení: Zastavte nastavit styl hodnoty do ovládacího prvku a místo toho nastavit formátování hodnoty v šablon stylů CSS.

Ovládací prvky webového serveru obsahují desítky vlastnosti, které můžete použít k nastavení vlastnosti stylu v řádku. Například vlastnosti ForeColor nastaví barvu textu pro ovládací prvek. Můžete provést tento stejný účinek efektivněji pomocí šablon stylů CSS. Šablony stylů umožňují centralizovat hodnoty stylu a vyhněte se nastavení tyto hodnoty v rámci aplikace.

Následující příklad ukazuje třídu šablony stylů CSS nastaví text, který se červené.

[!code-css[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample1.css)]

Následující příklad ukazuje, jak dynamicky nastavit třídu CSS.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample2.cs)]

<a id="callback"></a>

### <a name="page-and-control-callbacks"></a>Stránky a zpětná volání ovládacího prvku

Doporučení: Přestat používat stránky a ovládací prvek zpětná volání a místo toho použít některý z následujících akcí: AJAX, UpdatePanel, metody akce MVC, webové rozhraní API nebo SignalR.

V předchozích verzích technologie ASP.NET umožňovala stránky a ovládací prvek metody zpětného volání aktualizovat část webové stránky bez aktualizace celé stránky. Teď můžete provádět částečné aktualizace stránky prostřednictvím [AJAX](../../../ajax/index.md), [UpdatePanel](https://msdn.microsoft.com/library/bb386454.aspx), [MVC](../../../mvc/index.md), [webového rozhraní API](../../../web-api/index.md) nebo [SignalR](../../../signalr/index.md). By se měla zastavit pomocí metod zpětného volání, protože může způsobit problémy s přátelské adresy URL a směrování. Ve výchozím nastavení ovládací prvky nepovolujte metody zpětného volání, ale pokud povolíte tuto funkci v ovládacím prvku, měli byste zakázat.

<a id="browsercap"></a>

### <a name="browser-capability-detection"></a>Zjišťování schopností prohlížeče

Doporučení: Přestat používat zjišťování schopností statické prohlížeče a místo toho použít funkce Dynamická detekce.

V předchozích verzích technologie ASP.NET podporované funkce pro každým prohlížečem byly uloženy do souboru XML. Rozpoznání podporu prostřednictvím statických vyhledávání není nejlepším řešením. Nyní můžete dynamicky zjistit pomocí funkce zjišťování rozhraní, jako je funkce podporované prohlížeče [Modernizr](http://modernizr.com/). Funkce detekce, zda podporu tím pokouší použít metodu nebo vlastnost a potom kontroluje se, pokud prohlížeč vytvořený požadovaný výsledek. Ve výchozím nastavení Modernizr je součástí šablony webové aplikace.

<a id="security"></a>

## <a name="security"></a>Zabezpečení

<a id="validation"></a>

### <a name="request-validation"></a>Žádost o ověření

Doporučení: Ověření vstupu uživatele a kódování výstupu od uživatelů.

Žádost o ověření je funkce technologie ASP.NET, která kontroluje každý požadavek a požadavek se zastaví, pokud se nachází zjištěné hrozby. Není závislý na ověření žádosti pro zabezpečení aplikace před útoky skriptování napříč weby. Místo toho ověřte všechny vstupy od uživatele a kódování výstupu. V některých omezených případech regulární výrazy můžete použít k ověření vstupu, ale ve složitější případů, měli byste ověřit vstup pomocí třídy rozhraní .NET, které určí, zda hodnota odpovídá povolené hodnoty.

Následující příklad ukazuje způsob použití statickou metodu ve třídě identifikátoru Uri k určení, zda je zadaný uživatelem identifikátor Uri platný.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample3.cs)]

Ale dostatečně ověření identifikátoru Uri, podívejte se na Ujistěte se, že Určuje `http` nebo `https`. Následující příklad používá k ověření, že je platný identifikátor Uri metody instance.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample4.cs)]

Před vykreslování uživatelský vstup ve formátu HTML nebo včetně uživatelského vstupu v dotazu SQL, kódování hodnoty tak, aby Ujistěte se, že škodlivý kód není zahrnutý.

HTML můžete kódovat hodnotu v kódu s &lt;%: %&gt; syntaxe, jak je znázorněno níže.

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample5.aspx?highlight=1)]

Nebo v syntaxi Razor HTML můžete kódovat s @, jak je znázorněno níže.

[!code-cshtml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample6.cshtml?highlight=1)]

Další příklad ukazuje jak do formátu HTML kódovat hodnotu v kódu.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample7.cs)]

Ke kódování bezpečně hodnotu pro příkazy SQL, použijte parametry příkazu [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx). <a id="cookieless"></a>

### <a name="cookieless-forms-authentication-and-session"></a>Ověřování pomocí formulářů bez souborů cookie a relace

Doporučení: Vyžaduje soubory cookie.

Předání ověřovacích informací v řetězci dotazu není zabezpečený. Proto vyžaduje soubory cookie, pokud vaše aplikace obsahuje ověřování. Pokud vaše soubory cookie jsou uloženy citlivé informace, zvažte vyžadování SSL pro soubor cookie.

Následující příklad ukazuje, jak zadat v souboru Web.config, že ověřování pomocí formulářů vyžaduje soubor cookie, který se přenáší přes protokol SSL.

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample8.xml)]

<a id="viewstatemac"></a>

### <a name="enableviewstatemac"></a>EnableViewStateMac

Doporučení: Nikdy nastaven na hodnotu false.

Ve výchozím nastavení, EnbableViewStateMac nastavena na hodnotu true. I v případě, že vaše aplikace nepoužívá stav zobrazení, nenastavujte EnableViewStateMac na hodnotu false. Tuto hodnotu nastavíte na hodnotu false bude ohrožovat zabezpečení aplikace pro skriptování napříč weby.

Spouštění pomocí technologie ASP.NET 4.5.2, runtime modul vynucuje **EnableViewStateMac = true**. I v případě, že ji nastavíte na hodnotu false, modul runtime bude ignorovat tuto hodnotu a pokračuje s hodnotou nastavenou na hodnotu true. Další informace najdete v tématu [ASP.NET 4.5.2 a EnableViewStateMac](https://blogs.msdn.com/b/webdev/archive/2014/05/07/asp-net-4-5-2-and-enableviewstatemac.aspx).

Následující příklad ukazuje, jak nastavit EnableViewStateMac na hodnotu true. Nemusíte skutečně nastavte tuto hodnotu na true, protože je ve výchozím nastavení hodnotu true. Nicméně pokud jste ji nastavíte na hodnotu false na libovolné stránce v aplikaci, musíte okamžitě opravit tuto hodnotu.

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample9.aspx)]

<a id="medium"></a>

### <a name="medium-trust"></a>Úrovni Medium Trust

Doporučení: Nezávisí na střední důvěryhodnosti (nebo jiné úroveň důvěryhodnosti) jako hranice zabezpečení.

Částečným vztahem důvěryhodnosti adekvátní ochranu aplikace by se neměl používat. Místo toho používat plnou důvěryhodnost a izolovat nedůvěryhodné aplikace v samostatných fondech aplikací. Navíc spouštět každý fond aplikací v rámci jedinečnou identitu. Další informace najdete v tématu [ASP.NET částečném vztahu důvěryhodnosti nezaručuje izolace aplikací](https://support.microsoft.com/kb/2698981).

<a id="appsettings"></a>

### <a name="ltappsettingsgt"></a>&lt;appSettings&gt;

Doporučení: Nezakazujte nastavení zabezpečení v &lt;appSettings&gt; elementu.

Prvek appSettings obsahuje mnoho hodnot, které jsou požadovány pro aktualizace zabezpečení. Nesmí změnit nebo zakázat tyto hodnoty. Pokud tyto hodnoty je třeba zakázat při nasazování aktualizace, okamžitě znovu povolte po dokončení nasazení.

Podrobnosti najdete v tématu [ASP.NET appSettings Element](https://msdn.microsoft.com/library/hh975440.aspx).

<a id="urlpathencode"></a>

### <a name="urlpathencode"></a>UrlPathEncode

Doporučení: Použijte [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx) místo.

Metoda UrlPathEncode byl přidán do rozhraní .NET Framework, chcete-li vyřešit potíže s kompatibilitou velmi určitého webového prohlížeče. Odpovídajícím způsobem kódování adresy URL a není ochrana aplikací před skriptování napříč weby. Nikdy používejte ji v aplikaci. Místo toho použijte [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx).

Následující příklad ukazuje, jak předat adresu URL kódovaný jako parametru řetězce dotazu pro ovládací prvek hypertextového odkazu.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample10.cs)]

<a id="performance"></a>

## <a name="reliability-and-performance"></a>Spolehlivost a výkon

<a id="presend"></a>

### <a name="presendrequestheaders-and-presendrequestcontent"></a>PreSendRequestHeaders a PreSendRequestContent

Doporučení: Nepoužívejte tyto události s spravované moduly. Místo toho napište nativní modul služby IIS k provedení požadované úlohy. Zobrazit [vytváření modulů HTTP v nativním kódu](https://msdn.microsoft.com/library/ms693629.aspx).

Můžete použít [PreSendRequestHeaders](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestheaders.aspx) a [PreSendRequestContent](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestcontent.aspx) události s nativní moduly služby IIS.
> [!WARNING]
> Nepoužívejte `PreSendRequestHeaders` a `PreSendRequestContent` s spravované moduly, které implementují `IHttpModule`. Nastavení těchto vlastností, může způsobit problémy s asynchronní požadavků. Kombinace požadovaný směrování žádostí na aplikace a protokoly websocket může vést k výjimky porušení přístupu, které může způsobit pád w3wp. Například iiscore! W3_CONTEXT_BASE::GetIsLastNotification + 68 v iiscore.dll způsobila výjimku narušení přístupu (0xC0000005).

<a id="asyncevents"></a>

### <a name="asynchronous-page-events-with-web-forms"></a>Události asynchronní stránky s webovými formuláři

Doporučení: Ve webových formulářů, vyhněte se zápis async void metody pro události životního cyklu stránky použijte raději [Page.RegisterAsyncTask](https://msdn.microsoft.com/library/system.web.ui.page.registerasynctask.aspx) pro asynchronní kód.

Pokud označíte událostí stránky s **asynchronní** a **void**, nelze určit dokončení asynchronního kódu. Místo toho použijte Page.RegisterAsyncTask ke spuštění asynchronního kódu způsobem, který umožňuje sledovat jeho dokončení.

Následující příklad ukazuje a tlačítko klikněte na obslužnou rutinu, která obsahuje asynchronní kód. Tento příklad zahrnuje čtení řetězcovou hodnotu asynchronně, který je k dispozici pouze jako zjednodušený příklad asynchronní úlohy a ne jako doporučený postup.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample11.cs)]

Pokud používáte asynchronních úloh, nastavte cílovou architekturu Http runtime 4.5 v souboru Web.config. Nastavení cílové architektury na 4.5 změní na nový kontext synchronizace, který byl přidán v rozhraní .NET 4.5. Tato hodnota je nastaven ve výchozím nastavení v nových projektech v sadě Visual Studio 2012, ale není možné nastavit při práci s existující projekt.

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample12.xml)]

<a id="fire"></a>

### <a name="fire-and-forget-work"></a>Oheň a zapomenout práce

Doporučení: Při zpracovávání konkrétní žádosti v rámci technologie ASP.NET, zabránilo spouštění fire a zapomenout práce (odpovídající volání metody ThreadPool.QueueUserWorkItem nebo vytváření časovač, který opakovaně volání delegáta).

Pokud má vaše aplikace fire a zapomenout práce, na kterém běží v rámci technologie ASP.NET, aplikace můžete získat synchronizovaný. V každém okamžiku domény aplikace lze zničit to znamená, že vaše probíhající proces už neodpovídá aktuální stav aplikace.

Měli byste přejít tohoto typu práce mimo prostředí ASP.NET. Můžete provádět probíhající práce pomocí webových úloh, služba Windows nebo role pracovního procesu v Azure a spustit tento kód z jiného procesu.

Pokud je třeba provést tuto práci v rámci technologie ASP.NET, můžete přidat balíček Nuget s názvem [WebBackgrounder](http://www.nuget.org/packages/webbackgrounder) spuštění kódu.

<a id="requestentity"></a>

### <a name="request-entity-body"></a>Obsah Entity žádosti

Doporučení: Vyhněte se čtení Request.Form nebo Request.InputStream před obslužnou rutinu události.

Nejdřívější, které byste si měli přečíst z Request.Form nebo Request.InputStream je během obslužnou rutinu spusťte událost. V aplikaci MVC Kontroleru je obslužná rutina a spouštění událostí je při spuštění metody akce. Ve webových formulářů na stránce je obslužná rutina a je execute událost, když se aktivuje událost Page.Init. Pokud načtete starší než událost execute obsah entity žádosti, ovlivňovat zpracování požadavku.

Pokud budete potřebovat číst obsah entity žádosti před událostí spouštění, použijte buď [Request.GetBufferlessInputStream](https://msdn.microsoft.com/library/ff406798.aspx) nebo [Request.GetBufferedInputStream](https://msdn.microsoft.com/library/system.web.httprequest.getbufferedinputstream.aspx). Při použití GetBufferlessInputStream získat nezpracovaný datový proud z požadavku a převzít odpovědnost za zpracování celé požadavku. Po volání GetBufferlessInputStream, Request.Form a Request.InputStream nejsou k dispozici, protože nebyly byl vyplněn technologie ASP.NET. Když použijete GetBufferedInputStream, získáte kopii datového proudu z požadavku. Request.Form a Request.InputStream jsou stále k dispozici později v požadavku, protože technologie ASP.NET naplní druhou kopii.

<a id="redirect"></a>

### <a name="responseredirect-and-responseend"></a>Response.Redirect a metody Response.End

Doporučení: Znát rozdíly ve zpracování vlákna po volání [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx).

[Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx) metoda volá metodu metody Response.End. V synchronní zpracování volání Request.Redirect způsobí, že aktuální vlákno, okamžitě zrušit. Ale u asynchronního procesu, metoda Response.Redirect není přerušení aktuálního vlákna, tak pokračuje v provádění kódu pro daný požadavek. U asynchronního procesu musíte se vrátit úlohu z metody na svém vyvolání zastaví provádění kódu.

V projektu aplikace MVC neměli by jste volat Response.Redirect. Místo toho vrátí RedirectResult.

<a id="viewstatemode"></a>

### <a name="enableviewstate-and-viewstatemode"></a>EnableViewState a ViewStateMode

Doporučení: ViewStateMode použijte místo EnableViewState zajistit detailní kontrolu nad tím, které pomocí ovládacích prvků stavu zobrazení.

EnableViewState nastavená na hodnotu false v direktivě stránky stav zobrazení je zakázaná pro všechny ovládací prvky v rámci stránky a není možné. Pokud chcete povolit stav zobrazení pro pouze některé ovládací prvky na stránce, nastavte pro stránku ViewStateMode zakázáno.

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample13.aspx)]

Potom nastavte ViewStateMode povoleno na pouze ovládací prvky, které skutečně potřebují stavu zobrazení.

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample14.aspx)]

Tím, že stav zobrazení pro pouze ovládací prvky, které potřebujete, můžete zmenšit velikost zobrazení stavu pro vaše webové stránky.

<a id="sqlprovider"></a>

### <a name="sqlmembershipprovider"></a>SqlMembershipProvider

Doporučení: Použijte balíček Universal Providers.

V aktuální šablony projektu se nahradil SqlMembershipProvider [balíčku ASP.NET Universal Providers](http://www.nuget.org/packages/Microsoft.AspNet.Providers), která je k dispozici jako balíček NuGet. Pokud používáte SqlMembershipProvider v projektu, který byl vytvořen v dřívější verzi šablon, byste měli přejít na Universal Providers. Balíček Universal Providers pracovat všechny databáze, které jsou podporovány rozhraním Entity Framework.

Další informace najdete v tématu [Úvod do ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx).

<a id="long"></a>

### <a name="long-running-requests-110-seconds"></a>Dlouho běžící požadavky (> 110 sekund)

Doporučení: Použijte [objekty Websocket](https://msdn.microsoft.com/library/system.net.websockets.websocket.aspx) nebo [SignalR](../../../signalr/index.md) pro připojené klienty a použití asynchronní vstupně-výstupních operací.

Dlouhodobé požadavky může způsobit nepředvídatelné výsledky a slabým výkonem ve webové aplikaci. Výchozí nastavení časového limitu pro žádost je 110 sekund. Pokud používáte stav relace se dlouho běžící žádostí, vydá ASP.NET po sekundách 110 zámek na objekt relace. Však může být vaše aplikace provádí operaci u objektu relace, pokud zámek je uvolněn a operace nemusí dokončit úspěšně. Pokud druhou žádost od uživatele se zablokuje při spuštění prvního požadavku, druhou žádost může získat přístup k objektu Session v nekonzistentním stavu.

Pokud vaše aplikace obsahuje blokování (nebo asynchronní) vstupně-výstupních operací, bude aplikace reagovat.

Ke zlepšení výkonu, použijte asynchronní vstupně-výstupních operací v rozhraní .NET Framework. Také můžete použijte objekty Websocket nebo SignalR pro připojení klientů k serveru. Tyto funkce jsou navržené pro efektivní zpracování dlouhodobé požadavky.
