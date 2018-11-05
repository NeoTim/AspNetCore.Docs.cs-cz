---
uid: web-pages/overview/getting-started/aspnet-web-pages-razor-faq
title: Webové stránky ASP.NET (Razor) – nejčastější dotazy | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento článek uvádí některé časté otázky ke správě webových stránek ASP.NET (Razor) a službě WebMatrix. Verze softwaru použít kurz ASP.NET Web Pages (R...
ms.author: riande
ms.date: 02/07/2014
ms.assetid: b137bd04-25e1-47cb-9d96-ef2e179ecf1f
msc.legacyurl: /web-pages/overview/getting-started/aspnet-web-pages-razor-faq
msc.type: authoredcontent
ms.openlocfilehash: 27bfbe782455a5f8cf5096953c91712988b8dcab
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021050"
---
<a name="aspnet-web-pages-razor-faq"></a>Webové stránky ASP.NET (Razor) – časté otázky
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> > [!NOTE] 
> > Služba WebMatrix už nedoporučuje jako integrované vývojové prostředí pro ASP.NET Web Pages. Použití [sady Visual Studio](xref:aspnet/web-pages/overview/getting-started/program-asp-net-web-pages-in-visual-studio) nebo [Visual Studio Code](https://code.visualstudio.com/).
>
> Tento článek uvádí některé časté otázky ke správě webových stránek ASP.NET (Razor) a službě WebMatrix.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Webové stránky ASP.NET (Razor) 3
> - Visual Studio 2013
> - Služba WebMatrix 3
>   
> 
> V tomto kurzu se také pracuje s ASP.NET Web Pages 2, služba WebMatrix 2 a sady Visual Studio 2012.


- [Jaký je rozdíl mezi webových stránek ASP.NET, webových formulářů ASP.NET a ASP.NET MVC?](#Whats_the_difference_between_ASP.NET_Web_Pages,_ASP.NET_Web_Forms,_and_ASP.NET_MVC)
- [Potřebuji k práci s webovými stránkami služby WebMatrix?](#Do_I_need_WebMatrix_in_order_to_work_with_Web_Pages)
- [Můžete použít ovládací prvky webových formulářů ASP.NET na stránku webové stránky?](#Can_I_use_ASP.NET_Web_Forms_controls_on_a_Web_Pages_page)
- [Můžete nasadit na webu rozhraní ASP.NET Web Pages bez použití služby WebMatrix](#Can_I_deploy_an_ASP.NET_Web_Pages_site_without_using_WebMatrix)
- [Je nutné použít metodu WebSecurity pomocné rutiny pro podporu přihlášení?](#Do_I_have_to_use_the_WebSecurity_helper_to_support_logins)
- [Podporuje rozhraní ASP.NET Web Pages HTML5?](#Does_ASP.NET_Web_Pages_support_HTML5)
- [Můžete použít jazyk JavaScript a jQuery s webovými stránkami?](#Can_I_use_JavaScript_and_jQuery_with_Web_Pages)
- [Další prostředky](#AdditionalResources)

Otázky týkající se chyby a další problémy, najdete v článku [Průvodce odstraňováním potíží pro webové stránky ASP.NET (Razor)](https://go.microsoft.com/fwlink/?LinkId=253001).

<a id="Whats_the_difference_between_ASP.NET_Web_Pages,_ASP.NET_Web_Forms,_and_ASP.NET_MVC"></a>
## <a name="whats-the-difference-between-aspnet-web-pages-aspnet-web-forms-and-aspnet-mvc"></a>Jaký je rozdíl mezi webových stránek ASP.NET, webových formulářů ASP.NET a ASP.NET MVC?

Jsou všechny tři technologie ASP.NET pro vytvoření dynamické webové aplikace:

- ASP.NET Web Pages se zaměřuje na přidání stránky HTML a funkce jednoduché a jednoduché syntaxi dynamický kód (na straně serveru) a přístup k databázi.
- Webové formuláře ASP.NET je založen na objektový model stránky a ovládací prvky tradiční okno – typ (tlačítka, seznamy, atd.). Webové formuláře používá model založený na událostech, které jsou známy všem uživatelům, kteří pracovali s vývojem na základě klienta (Windows forms).
- ASP.NET MVC implementuje vzor model-view-controller pro technologii ASP.NET. Důraz je na "oddělení oblastí zájmu" (zpracování dat a vrstvy uživatelského rozhraní).

Všechny tři architektury jsou plně podporované a i nadále být vyvinuto týmem ASP.NET. Obecně platí, volba které framework použít závisí na pozadí a vyzkoušejte si technologie ASP.NET.

ASP.NET Web Pages zejména je navržená k tomu, aby pro lidi, kteří už, chcete-li přidat zpracování na serveru na jejich stránky HTML. Je to Dobrá volba pro studenty, nadšence, obecně uživatelům, kteří jsou programování. Také může být dobrou volbou pro vývojáře, kteří mají zkušenosti s technologií jinou než ASP.NET webové technologie.

<a id="Do_I_need_WebMatrix_in_order_to_work_with_Web_Pages"></a>
## <a name="do-i-need-webmatrix-in-order-to-work-with-web-pages"></a>Potřebuji k práci s webovými stránkami služby WebMatrix?

Ne. Služba WebMatrix už nedoporučuje jako integrované vývojové prostředí pro ASP.NET Web Pages. Použití [sady Visual Studio](program-asp-net-web-pages-in-visual-studio.md) nebo [Visual Studio Code](https://code.visualstudio.com/).

Pokud už nechcete používat Visual Studio nebo Visual Studio Code, můžete nainstalovat součásti produkty samostatně pomocí [instalačního programu webové platformy Microsoft](https://www.microsoft.com/web/downloads/platform.aspx). Budete potřebovat následující produkty:

- Microsoft .NET Framework 4.5
- ASP.NET MVC 5 (který se nainstaluje rozhraní ASP.NET Web Pages)
- Služba IIS Express (web server)
- Microsoft SQL Server Compact 4.0 (databáze)

Můžete použít textový editor k úpravě *.cshtml* (nebo *.vbhtml*) stránky.

Správa databází systému SQL Server Compact (*SDF* soubory) bez nástroj je o něco složitější. Visual Studio containds nástroje pro správu *SDF* databází. Můžete také spustit příkazy SQL v kódu provádět mnoho úkolů správy systému SQL Server.

K otestování *.cshtml* stránky bez použití integrovaného vývojového prostředí (IDE), je můžete nasadit na aktivní server. (Viz [můžete nasadit na webu rozhraní ASP.NET Web Pages bez pomocí služby WebMatrix?](#Can_I_deploy_an_ASP.NET_Web_Pages_site_without_using_WebMatrix))

### <a name="running-iis-express-without-using-an-ide"></a>Spuštění služby IIS Express bez použití integrované vývojové prostředí

-Li nainstalovat službu IIS Express do počítače jako webový server, který můžete použít k otestování stránky. Můžete spustit službu IIS Express z příkazového řádku a přidružte jej k určité číslo portu. Potom zadejte tento port při žádosti o *.cshtml* soubory ve vašem prohlížeči.

Ve Windows, otevřete příkazový řádek s oprávněními správce a změňte na *C:\Program Files\IIS Express.* (Pro 64bitové systémy, použijte složku *C:\Program Files (x86) \IIS Express.)* Zadejte následující příkaz s použitím skutečné cesty k webu:

`iisexpress.exe /port:35896 /path:C:\BasicWebSite`

Můžete použít libovolné číslo portu, který se už rezervovaná službou nějaký jiný proces. (Čísla portů výše 1 024 jsou obvykle zdarma). Pro `path` použijte cestu ke složce webu kde *.cshtml* jsou soubory.

Po spuštění tohoto příkazu k nastavení služby IIS Express pro obsluhu stránky můžete otevřete prohlížeč a přejděte *.cshtml* souboru. Použijte adresu URL takto:

`http://localhost:35896/default.cshtml`

Získat nápovědu služby IIS Express možnosti příkazového řádku, zadejte `iisexpress.exe /?` na příkazovém řádku.

<a id="Can_I_use_ASP.NET_Web_Forms_controls_on_a_Web_Pages_page"></a>
## <a name="can-i-use-aspnet-web-forms-controls-on-a-web-pages-page"></a>Můžete použít ovládací prvky webových formulářů ASP.NET na stránku webové stránky?

Ne. Ovládací prvky webových formulářů stejně jako [zaškrtávací políčko](https://msdn.microsoft.com/library/system.web.ui.webcontrols.checkbox) ovládací prvek, [validačních ovládacích prvků](https://msdn.microsoft.com/library/bwd43d0x)a [GridView](https://msdn.microsoft.com/library/system.web.ui.webcontrols.gridview) řízení fungovat pouze v stránky webových formulářů (*.aspx* soubory). Tyto ovládací prvky vyžadují rozhraní stránky webových formulářů.

<a id="Can_I_deploy_an_ASP.NET_Web_Pages_site_without_using_WebMatrix"></a>
## <a name="can-i-deploy-an-aspnet-web-pages-site-without-using-webmatrix"></a>Můžete nasadit na webu rozhraní ASP.NET Web Pages bez použití služby WebMatrix

Ano. Soubory webové stránky můžete ručně zkopírovat na server (obvykle s použitím FTP). Pokud provádíte ruční export, máte také kopírovat soubory, které podporují SQL Server Compact (databáze). Podrobnosti najdete v příspěvku na blogu [nasazení webových stránek aplikací bez nástroj](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2317).

<a id="Do_I_have_to_use_the_WebSecurity_helper_to_support_logins"></a>
## <a name="do-i-have-to-use-the-websecurity-helper-to-support-logins"></a>Je nutné použít metodu WebSecurity pomocné rutiny pro podporu přihlášení?

Ne. `SimpleMembership` Zprostředkovatele, který je součástí sady rozhraní ASP.NET Web Pages je jednou z možností. Zprostředkovatele zabezpečení, které jsou součástí technologie ASP.NET (které mohou sloužit k práci s ve webových formulářů) jsou také k dispozici. Například můžete použít ověřování pomocí formulářů v ASP.NET Web Pages stejně jako ve webových formulářů. Jedním z příkladů, jak používat ověřování pomocí formulářů, naleznete v tématu Článek Microsoft Support [jak ověřování ve vaší aplikaci technologie ASP.NET pomocí C# .NET](https://support.microsoft.com/kb/301240). Stáhněte si jednoduchý příklad, najdete v článku [verzi technologie ASP.NET "přihlášení &amp; heslo](http://www.codeguru.com/csharp/.net/net_asp/scripting/article.php/c19295/ASPNET-version-of-Login--Password.htm).

Informace o tom, jak používat ověřování Windows, naleznete v příspěvku blogu [ověřování pomocí Windows na webových stránkách ASP.NET](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2298).

<a id="Does_ASP.NET_Web_Pages_support_HTML5"></a>
## <a name="does-aspnet-web-pages-support-html5"></a>Podporuje rozhraní ASP.NET Web Pages HTML5?

Ano. Na stránkách vytvoříte s webovými stránkami ASP.NET (*.cshtml* nebo *.vbhtml* stránky) jsou v podstatě stránky HTML, které také obsahovat kód, který běží na serveru, před vykreslením stránky. Tak dlouho, dokud webového prohlížeče podporuje HTML5, můžete použít prvky HTML5 *.cshtml* nebo *.vbhtml* stránky.

<a id="Can_I_use_JavaScript_and_jQuery_with_Web_Pages"></a>
## <a name="can-i-use-javascript-and-jquery-with-web-pages"></a>Můžete použít jazyk JavaScript a jQuery s webovými stránkami?

Absolutně. Na stránkách vytvoříte s webovými stránkami ASP.NET (*.cshtml* nebo *.vbhtml* stránky) jsou jenom HTML stránky se serverovým kódem v nich. Proto cokoli, co můžete udělat v normální stránku HTML pomocí pomocí jazyka JavaScript nebo knihovny jQuery můžete provést také *.cshtml* nebo *.vbhtml* stránky.

**Starter Site** šablony v nástroji WebMatrix obsahuje řadu knihovny jQuery. Pokud pomocí této šablony vytvoříte web *skripty* složka obsahuje základní knihovny jQuery (*jquery 1.6.2.js)* a knihovny pro ověřování jQuery (*jquery.validate.js*atd.).

Tady jsou některé blogové příspěvky, které ukazují způsoby použití jQuery s webovými stránkami ASP.NET:

- [Přidání jQuery ještě lepší pro webové stránky ASP.NET pomocí Webmatrixu](http://rachelappel.com/jquery/adding-jquery-goodness-to-asp-net-web-pages-using-webmatrix/) podle Rachel Appel
- [5 minut: Služba WebMatrix uživatelské rozhraní jQuery + json + jQuery šablony](http://joeriks.com/2011/01/30/5-min-webmatrix-jquery-ui-json-jquery-templates/) podle Jonas Eriksson
- [Služba WebMatrix a formulářů jQuery](http://mikesdotnetting.com/Article/155/WebMatrix-And-jQuery-Forms) podle Mike Brind

<a id="AdditionalResources"></a>
## <a name="additional-resources"></a>Další prostředky


[Webové stránky ASP.NET (Razor) – průvodce řešením potíží](https://go.microsoft.com/fwlink/?LinkId=253001)

[Fórum pro službu WebMatrix a webové stránky ASP.NET](https://forums.asp.net/1224.aspx/1?WebMatrix) na webu technologie ASP.NET
