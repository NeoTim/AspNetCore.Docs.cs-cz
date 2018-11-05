---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/getting-started
title: Začínáme | Dokumentace Microsoftu
author: Rick-Anderson
description: Služba WebMatrix už nedoporučuje jako integrované vývojové prostředí pro ASP.NET Web Pages. Pomocí sady Visual Studio nebo Visual Studio Code. Tyto doprovodné materiály...
ms.author: riande
ms.date: 05/28/2015
ms.assetid: a36d3bdf-ef1b-47a4-b932-3a0cf4cad716
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/getting-started
msc.type: authoredcontent
ms.openlocfilehash: 79f76410e091b137e2792127b0a42524270761e8
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021492"
---
<a name="getting-started"></a>Začínáme
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE[](~/includes/rp.md)]

> > [!NOTE] 
> > 
> > Služba WebMatrix už nedoporučuje jako integrované vývojové prostředí pro ASP.NET Web Pages. Použití [sady Visual Studio](../program-asp-net-web-pages-in-visual-studio.md) nebo [Visual Studio Code](https://code.visualstudio.com/).
> 
> 
> Tento návod a aplikace poskytuje přehled o webových stránkách ASP.NET (verze 2 nebo novější) a syntaxi Razor, jednoduché rozhraní pro vytváření dynamických webů. Zavádí také služby WebMatrix, nástroj pro vytváření stránek a webů.
> 
> **Úroveň**: Nová rozhraní ASP.NET Web Pages.  
> **Dovednosti předpokládá, že**: HTML, základní šablony stylů CSS (CSS).
> 
> Co se dozvíte v tomto prvním kurzu sady:
> 
> - Jaké technologie ASP.NET Web Pages je a co to je pro.
> - Co je služba WebMatrix.
> - Jak nainstalovat vše, co došlo k chybě.
> - Jak vytvořit web ve službě WebMatrix.
>   
> 
> Popsané funkce a technologie:
> 
> - Instalace webové platformy Microsoft.
> - Služba WebMatrix.
> - *.cshtml* stránky
>   
> 
> Mike Pope původně vytvořil v tomto kurzu. Tom FitzMacken aktualizaci pro sadu Microsoft WebMatrix 3.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Rozhraní ASP.NET Web Pages (Razor) 2
> - Služba WebMatrix 3


## <a name="what-should-you-know"></a>Co můžete vědět?

Předpokládáme, že už znáte:

- **HTML**. Vyžaduje se žádné podrobné znalosti. Nebudou vám vysvětlíme, HTML, ale nebudeme také používat cokoli složitého. Poskytujeme odkazy na kurzy HTML, kde myslíme si, že jsou užitečná.
- **Šablony stylů CSS (CSS)**. Stejným způsobem jako s HTML.
- **Databáze Basic nápady**. Pokud jste použít tabulku pro data a seřadit a filtrovat data, ke kterým je úroveň znalosti obecně předpokládáme pro tuto sérii kurzů.

Také předpokládáme, že vás zajímá výukové základní programování. ASP.NET Web Pages použít programovací jazyk C#. Nemusíte mít žádné na pozadí v programování, stačí zájem v něm. Pokud žádné JavaScript někdy jste napsali na webové stránce před, máte spoustu na pozadí.

Všimněte si, že pokud jste se seznámili s programováním, můžete se setkat zpočátku nastavený v tomto kurzu se pomalu pohybuje, zatímco přeneseme programátory zrychlit. Získáme po prvních několika výukových programech, ale bude méně základní programování vysvětlete a věci se přesunou na rychlejší klipu.

## <a name="what-do-you-need"></a>Co je potřeba?

Zde je, co budete potřebovat:

- Počítač se systémem Windows 8, Windows 7, Windows Server 2008 nebo Windows Server 2012.
- Živé připojení k Internetu.
- Oprávnění správce (požadováno pro instalaci).

## <a name="what-is-aspnet-web-pages"></a>Novinky webových stránek ASP.NET

ASP.NET Web Pages je architektura, která slouží k vytvoření dynamické webové stránky. Jednoduché webové stránky HTML je statická; jeho obsah je určeno pevné značka jazyka HTML, který je na stránce. Dynamických stránek podobné těm, které vytvoříte s webovými stránkami ASP.NET umožňují vytvářet obsah stránky v reálném čase, pomocí kódu.

Dynamických stránek jde dělat nejrůznější věci. Můžete požádat uživatele o vstup pomocí formuláře a změňte na stránce zobrazí a jak vypadá. Můžete využít informace od uživatele, uložit do databáze a seznamu později. Odesílat e-maily z vašeho webu. Můžete pracovat s jinými službami na webu (například mapování služby) a vytvářet stránky, které se integrují informace z těchto zdrojů.

## <a name="what-is-webmatrix"></a>Co je služba WebMatrix?

WebMatrix je nástroj, který integruje editoru webové stránky, nástroje databáze, webový server pro testování stránky a funkce pro publikování webu na Internetu. WebMatrix je bezplatný a je snadné k instalaci a snadno se používá. (Funguje i pro stejně jednoduché stránky HTML, stejně jako pro jiné technologie, jako je PHP.)

Ve skutečnosti nepotřebujete *mají* k práci s webovými stránkami ASP.NET pomocí Webmatrixu. Můžete vytvářet stránky pomocí textového editoru, například a testování stránek s využitím webového serveru, máte přístup. Ale služba WebMatrix usnadňuje všechny velmi, tak tyto kurzy budou používat služby WebMatrix v průběhu.

## <a name="about-these-tutorials"></a>Informace o těchto kurzů

Této sérii kurzů je úvodem k tom, jak používat rozhraní ASP.NET Web Pages. Celkový počet v této úvodní kurz sadě jsou 9 kurzů. To je částí série kurzů sady, která vás od začátečníků po rozhraní ASP.NET Web Pages, k vytvoření skutečné, profesionální weby.

Tento první kurz nastavit koncentráty zobrazuje základní informace o tom, jak pracovat s webovými stránkami ASP.NET. Jakmile budete hotovi, můžete pracovat s další kurz sady, které vyzvednutí kde ukončení této a webové stránky, která prozkoumat podrobněji.

Záměrně přejdeme snadno na podrobnější vysvětlení. A pokaždé, když něco ukazujeme, pro tuto sérii kurzů jsme vždy vyberte způsob, jakým myslíme, že je nejjednodušší pochopit. Novější kurz sady přejít do větší hloubky a zobrazení efektivnější a flexibilnější přístupy (také další zábavný). Ale tyto kurzy vyžadují, abyste nejprve porozumět základům o.

Této sérii kurzů, které jste začali zahrnuje tyto funkce z webových stránek ASP.NET:

- Úvod a připravuje se vše nainstalované. (To je v tomto kurzu, který právě čtete.)
- Základy programování v rozhraní ASP.NET Web Pages.
- Vytvoření databáze.
- Vytvoření a zpracování formulář vstupu uživatele.
- Přidání, aktualizaci a odstraňování dat v databázi.

## <a name="what-will-you-create"></a>Co se vytvoří?

V tomto kurzu nastavte a následné ty točí kolem webu, kde můžete zobrazit seznam filmy, které vám vyhovuje. Budete moct zadat filmy, upravovat a jejich seznam. Tady je několik stránek, které vytvoříte v této sérii kurzů. První z nich ukazuje film zobrazení stránky, kterou vytvoříte:

![Dokončil filmová aplikace zobrazuje seznam video](getting-started/_static/image1.png)

A tady je stránka, která umožňuje přidat nový film informace na váš web:

![Dokončení filmová aplikace stránkou přidat video](getting-started/_static/image2.png)

Další kurz sady sestavení v tomto nastavení a přidávají další funkce, jako je nahrávání obrázků, umožňuje uživatelům přihlášení, odesílání e-mailů a integraci s sociálních médií.

## <a name="see-this-app-running-on-azure"></a>Zobrazit tuto aplikaci běžící v Azure

Chcete zobrazit dokončené web spuštěný jako živou webovou aplikaci? Kompletní verze aplikace můžete nasadit ke svému účtu Azure jednoduše kliknutím na následující tlačítko.

[![](http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/WebPagesMovies)

Potřebujete účet Azure k nasazení tohoto řešení do Azure. Pokud ještě nemáte účet, máte následující možnosti:

- [Zdarma otevřít účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) – budete dostávat kredity můžete použít k vyzkoušení placených služeb Azure a dokonce i po jejich použití až můžete účet ponechat a dál používat bezplatné služby Azure.
- [Aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) – vaše předplatné MSDN vám dává kredity každý měsíc, můžete použít k placení za služby Azure.

## <a name="installing-everything"></a>Všechno, co instalace

Všechno, co můžete nainstalovat pomocí instalačního programu webové platformy společnosti Microsoft. V důsledku toho nainstalovat Instalační program a potom ho použít k instalaci všechno ostatní.

Pomocí webových stránek, je nutné mít minimálně Windows XP s aktualizací SP3 nainstalovaná, nebo Windows Server 2008 nebo novější.

Na [webové stránky](../../../index.md) webu technologie ASP.NET, klikněte na tlačítko **nainstalovat**.

![Zobrazení webu rozhraní ASP.NET Web &quot;instalace služby WebMatrix&quot; tlačítko](getting-started/_static/image3.png)

Zobrazí se výzva k přijetí licenční podmínky a prohlášení o ochraně před instalací služby WebMatrix.

![Přijměte termín k zahájení instalace](getting-started/_static/image4.png)

Klikněte na tlačítko **spustit** spusťte instalaci. (Pokud budete chtít uložte instalační program, klikněte na tlačítko **Uložit** a poté spusťte instalační program ze složky, kam jste jej uložili.)

![](getting-started/_static/image5.png)

Instalace webové platformy se zobrazí, připravena k instalaci služby WebMatrix. Klikněte na tlačítko **nainstalovat**.

![](getting-started/_static/image6.png)

Proces instalace přijde na to, co bylo potřeba nainstalovat na počítače a zahájí proces. V závislosti na tom, co přesně musí být nainstalovaná proces může trvat od několika okamžiků několik minut. Vyberte **souhlasím** k potvrzení licenčních podmínek.

## <a name="hello-webmatrix"></a>Dobrý den, služba WebMatrix

Po dokončení procesu instalace můžete spustit služby WebMatrix. Pokud ne, ve Windows, od **Start** nabídky, spuštění **Microsoft WebMatrix**.

Při prvním spuštění služby WebMatrix, budete mít příležitost dobře se přihlaste se k Microsoft Azure se svým účtem Microsoft. Po přihlášení, zobrazí se 10 bezplatných webových aplikací prostřednictvím Azure. Tato bezplatná webová aplikace poskytují pohodlný způsob pro testování vašich aplikací. Pokud ještě nemáte účet Azure, ale máte předplatné MSDN, můžete si [aktivovat výhody předplatného MSDN](https://www.windowsazure.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604). V opačném případě můžete vytvořit bezplatného zkušebního účtu stačí pár minut. Podrobnosti najdete v tématu [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/free/?WT.mc_id=A443DD604).

Není potřeba Přihlaste se hned teď a pokračujte v tomto kurzu. Pokud není přihlásíte teď, budete stále mít možnost se přihlásit později. Poslední [tématu](publishing.md) řady v tomto kurzu dozvíte, jak nasadit svůj web do Azure, proto budete muset přihlásit k dokončení tohoto tématu.

V tomto okamžiku buď Přihlaste se pomocí svého účtu Microsoft nebo vyberte **teď ne** v pravém dolním rohu.

![Přihlásit se](getting-started/_static/image7.png)

Pokud chcete začít, bude vytvoření prázdného webu a přidat na stránku. V pozdějších kurzech v této sadě bude přehrávat s některou ze šablon integrované webu.

V okně start klikněte na tlačítko **nový**.

![Služba WebMatrix úvodní obrazovky](getting-started/_static/image8.png)

Šablony jsou předem připravených souborů a stránky pro různé druhy webů. Pokud chcete zobrazit všechny šablony, které jsou k dispozici ve výchozím nastavení, vyberte možnost galerie šablon.

![Vyberte šablonu Galerie](getting-started/_static/image9.png)

V **rychlý Start** okně **prázdný web** z **ASP.NET** skupinu a pojmenujte nový web "WebPagesMovies".

![Rychlý Start pro službu WebMatrix okna máte zvolenou šablonu prázdný web](getting-started/_static/image10.png)

Klikněte na tlačítko **Další**.

Pokud jste se přihlásili ke svému účtu Microsoft, budete mít příležitost k vytvoření webu v Azure. Na základě názvu vašeho webu, výchozí název **WebPagesMovies.azurewebsites.net** navržený; však vykřičník označuje, že tento název není k dispozici na Windows Azure. Pro jednoduchost, vyberte **přeskočit** obejít vytvoření webu v Azure hned teď. Později v této sérii publikujeme webu do Azure.

![Vytvoření serveru azure](getting-started/_static/image11.png)

Služba WebMatrix se vytvoří a otevře web:

![Nový web WebPagesMovies otevřete v nástroji WebMatrix](getting-started/_static/image12.png)

V horní části je panel nástrojů Rychlý přístup a pás karet. Vlevo dole, zobrazí selektor pracovního prostoru kde přepínat mezi úlohami (**lokality**, **soubory**, **databází**, **sestavy**). Na pravé straně je obsahu podokna editoru a pro sestavy. A v dolní části se zobrazí čas od času oznamovací zprávy.

Dozvíte další informace o službě WebMatrix a jeho funkcí při procházení těmito kurzy.

## <a name="creating-a-web-page"></a>Vytvoření webové stránky

Abyste se seznámili s nástrojem WebMatrix a webových stránek ASP.NET, vytvoříte jednoduchou stránku.

V modulu pro výběr pracovního prostoru, vyberte **soubory** pracovního prostoru. Tento pracovní prostor vám umožní pracovat se soubory a složkami. V levém podokně zobrazí strukturu souborů vašeho webu. Změny pásu karet zobrazit úkoly související se soubory.

![Soubor pracovního prostoru v nástroji WebMatrix](getting-started/_static/image13.png)

Na pásu karet klikněte na šipku v rámci **nový** a potom klikněte na tlačítko **nový soubor**.

![Použití &quot;nový&quot; v pásu karet a vytvořte nový soubor](getting-started/_static/image14.png)

Služba WebMatrix zobrazí seznam typů souborů. Vyberte **CSHTML**a **název** zadejte "HelloWorld". Na stránce CSHTML je na stránce rozhraní ASP.NET Web Pages.

![Vytváří se nová stránka CSHTML s názvem HelloWorld.cshtml](getting-started/_static/image15.png)

Klikněte na tlačítko **OK**.

Služba WebMatrix vytvoří stránky a otevře v editoru.

![Nová stránka HelloWorld v editoru WebMatrix](getting-started/_static/image16.png)

Jak vidíte, tato stránka obsahuje většinou běžné kódování HTML, s výjimkou bloku v horní části, který vypadá takto:

[!code-cshtml[Main](getting-started/samples/sample1.cshtml)]

To pro přidání kódu, jak brzy zjistíte.

Všimněte si, že různé části na stránce &mdash; názvy elementů, atributů a text a navíc bloku v horní části, jsou všechny na různé barvy. Tento postup se nazývá *zvýraznění syntaxe*, a usnadňuje mít všechno Vymazat. Je jednou z funkcí, které usnadňuje práci s webovými stránkami ve Webmatrixu.

Přidat obsah `<head>` a `<body>` prvky jako v následujícím příkladu. (Pokud chcete, můžete pouze zkopírujte následující blok a nahraďte tento kód celé stávající stránky.)

[!code-cshtml[Main](getting-started/samples/sample2.cshtml)]

V panelu nástrojů Rychlý přístup nebo v **souboru** nabídky, klikněte na tlačítko **Uložit**.

![Tlačítko Uložit ve službě WebMatrix panelu nástrojů Rychlý přístup](getting-started/_static/image17.png)

## <a name="testing-the-page"></a>Testování stránky

V **soubory** pracovního prostoru, klikněte pravým tlačítkem myši *HelloWorld.cshtml* stránce a potom klikněte na **spustit v prohlížeči**.

![Spuštění stránky pomocí tlačítka pro spuštění na pásu karet nástroje WebMatrix](getting-started/_static/image18.png)

Služba WebMatrix spustí integrovaný webový server (služba IIS Express), můžete použít k testování stránek ve vašem počítači. (Bez služby IIS Express v nástroji WebMatrix, je třeba publikovat na webový server stránky někde předtím, než je možné otestovat.) Na stránce se zobrazí ve vašem výchozím prohlížeči.

![&quot;Hello World&quot; stránku spuštění v prohlížeči](getting-started/_static/image19.png)

Všimněte si, že při testování stránku ve službě WebMatrix, adresu URL v prohlížeči je něco jako `http://localhost:33651/HelloWorld.cshtml.` název *localhost* odkazuje na místním serveru, což znamená, že na stránce obsluhuje webový server, který je ve vašem počítači. Jak jsme uvedli, služba WebMatrix nabízí program webového serveru s názvem služby IIS Express, která se spouští při spuštění na stránce.

Počet po *localhost* (například *localhost:33651*) odkazuje *číslo portu* ve vašem počítači. Toto je počet "kanál" využívající službu IIS Express pro tento konkrétní web. Číslo portu je z rozsahu 1024 až 65536 vybraného náhodně, když vytvoříte web, a se liší pro každý web, který vytvoříte. (Při testování svůj vlastní web, číslo portu téměř jistě budou jiné číslo než 33561.) Když použijete jiný port pro každý web, službu IIS Express můžete ponechat přímo které z vašich lokalit se mluví k.

Později při publikování webu do veřejné webového serveru, už se nezobrazují *localhost* v adrese URL. Od tohoto okamžiku zobrazí více klasickou adresu URL jako `http://myhostingsite/mywebsite/HelloWorld.cshtml` nebo je bez ohledu na stránce. Můžete se dozvíte víc o publikování lokality dále v této sérii kurzů.

## <a name="adding-some-server-side-code"></a>Přidání kódu na straně serveru

Zavřete prohlížeč a přejděte zpět na stránku ve Webmatrixu.

Přidá řádek do bloku kódu tak, aby vypadal jako v následujícím kódu:

[!code-cshtml[Main](getting-started/samples/sample3.cshtml)]

To je něco kódu Razor. Je nejspíš jasné, že získá aktuální datum a čas a vloží tuto hodnotu do *proměnnou* s názvem `currentDateTime`. Nemusíte se věnovat čtení informace o syntaxi Razor v dalším kurzu.

V těle stránky až `<p>Hello World!</p>` prvku, přidejte následující:

[!code-html[Main](getting-started/samples/sample4.html)]

Tento kód získá hodnotu, kam si ukládáte do `currentDateTime` proměnné v horní části a vloží jej do kódu stránky. `@` Znak označuje rozhraní ASP.NET Web Pages kód na stránce.

Spusťte znovu (WebMatrix uloží změny můžete před spuštěním na stránce). Tentokrát zobrazí datum a čas ve stránce.

![&quot;Hello World&quot; spuštění v prohlížeči pomocí dynamicky generované času zobrazení stránky](getting-started/_static/image20.png)

Chvíli počkejte a pak aktualizujte stránku v prohlížeči. Zobrazení data a času se aktualizuje.

V prohlížeči podívejte se na zdroje stránky. Vypadá podobně jako následující kód:

[!code-html[Main](getting-started/samples/sample5.html)]

Všimněte si, že `@{ }` tam není bloku v horní části. Všimněte si také, že zobrazuje displej datum a čas vytvoření skutečného řetězce znaků (`1/18/2012 2:49:50 PM` nebo jakékoli), nikoli `@currentDateTime` jako došlo v *.cshtml* stránky. Co se stalo, zde je, že při spuštění stránky technologie ASP.NET zpracovány veškerý kód (velmi málo v tomto případě), která byla označena jako `@`. Kód vytvoří výstup, a tento výstup byl vložen do stránky.

## <a name="this-is-what-aspnet-web-pages-are-about"></a>Toto je ASP.NET Web Pages se týkají

Při čtení, rozhraní ASP.NET Web Pages vytváří dynamického webového obsahu, co už víte, zde je cílem. Stránka, kterou jste právě vytvořili obsahuje stejný kód HTML, který jste se seznámili před. Může také obsahovat kód, který může provádět všechny možné druhy úloh. V tomto příkladu stejně jednoduchá získat aktuální datum a čas. Jak už jste viděli, můžete proložit HTML výstup na stránce kódu. Když uživatel požádá *.cshtml* stránku v prohlížeči, ASP.NET zpracovává stránky, i když je stále v rámci webového serveru. ASP.NET vloží výstup kódu (pokud existuje) do stránky ve formátu HTML. Po dokončení zpracování kódu, technologie ASP.NET odešle výslednou stránku v prohlížeči. Všechno, co se někdy získá prohlížeče je ve formátu HTML. Zde je diagram:

![Koncepční tok způsobu, jakým technologie ASP.NET generuje HTML dynamicky](getting-started/_static/image21.png)

Cílem je jednoduché, ale kód můžete provádět mnoho zajímavých úlohy a existuje mnoho způsobů zajímavé, ve kterých můžete dynamicky přidat obsah ve formátu HTML na stránce. A ASP.NET *.cshtml* stránky, jako jsou jakékoli stránky HTML, může také obsahovat kód, který běží v prohlížeči (kód jazyka JavaScript a jQuery). Prozkoumáte všechny tyto věci v této sérii kurzů a dalších ty.

## <a name="coming-up-next"></a>Chystá se další

V dalším kurzu této série prozkoumejte trochu programování rozhraní ASP.NET Web Pages.

## <a name="additional-resources"></a>Další prostředky

[Vytvoření zcela nové webové stránky ASP.NET](https://www.microsoft.com/web/post/create-an-aspnet-website-from-scratch). Toto je kurz, který této volbě jde jenom o pomocí služby WebMatrix (ne webových stránek ASP.NET). Přejde do trochu více podrobností o některé další funkce služby WebMatrix, který jsme nebudeme se zabývat v této sérii kurzů.

> [!div class="step-by-step"]
> [Next](intro-to-web-pages-programming.md)
