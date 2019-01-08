---
uid: web-forms/overview/older-versions-getting-started/aspnet-ajax/understanding-partial-page-updates-with-asp-net-ajax
title: Principy částečných aktualizací stránek technologií ASP.NET AJAX | Dokumentace Microsoftu
author: scottcate
description: Pravděpodobně nejvíce viditelné funkce rozšíření ASP.NET AJAX je schopnost provést na stránce částečné nebo přírůstkové aktualizace bez provedení úplného zpětného odeslání na t...
ms.author: riande
ms.date: 03/28/2008
ms.assetid: 54d9df99-1161-4899-b4e8-2679c85915e7
msc.legacyurl: /web-forms/overview/older-versions-getting-started/aspnet-ajax/understanding-partial-page-updates-with-asp-net-ajax
msc.type: authoredcontent
ms.openlocfilehash: 4883046aa16d5e67b7f0c92e15c897ef1a933b67
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098932"
---
<a name="understanding-partial-page-updates-with-aspnet-ajax"></a>Principy částečných aktualizací stránek technologií ASP.NET AJAX
====================
podle [– Scott Cate](https://github.com/scottcate)

[Stáhnout PDF](http://download.microsoft.com/download/C/1/9/C19A3451-1D14-477C-B703-54EF22E197EE/AJAX_tutorial01_Partial_Page_Updates_cs.pdf)

> Možná nejviditelnější funkce rozšíření ASP.NET AJAX je schopnost provést na stránce částečné nebo přírůstkové aktualizace bez provedení úplného zpětného odeslání na server bez změn kódu a změny minimální značek. Výhody jsou rozsáhlé – stav ve vašich multimédiích (třeba Adobe Flash nebo Windows Media) je beze změny, je snížení nákladů na šířku pásma a klient nedochází k blikání obvykle spojené se zpětné volání.


## <a name="introduction"></a>Úvod

Technologie ASP.NET společnosti Microsoft přináší objektově orientované a založený na událostech programovací model a sjednotí s výhodami zkompilovaný kód. Svůj model zpracování na straně serveru má však několik nevýhody vyplývajících z technologie:

- Aktualizace stránky vyžadují zpátečního převodu na server, který vyžaduje aktualizace stránky.
- Doby odezvy nezůstanou zachována důsledky generovaný Javascript nebo jiné technologie na straně klienta (třeba Adobe Flash)
- Během zpětné volání prohlížečích než Microsoft Internet Explorer nepodporuje automatické obnovení na pozici posunutí. A dokonce i v aplikaci Internet Explorer se vám stále blikání obnovení stránky.
- Zpětného odeslání může zahrnovat vysoké množství šířky pásma, jako \_ \_může zvětšit pole Stav zobrazení formuláře, zejména při práci s ovládacími prvky, jako je například ovládací prvek GridView nebo opakovače.
- Neexistuje žádné jednotný model pro přístup k webovým službám prostřednictvím jazyka JavaScript nebo jiné technologie na straně klienta.

Zadejte rozšíření ASP.NET AJAX od Microsoftu. AJAX, což je zkratka pro **A** synchronní **J** avaScript **A** nd **X** ML, je integrovaná platforma pro poskytování přírůstkové stránky aktualizace prostřednictvím JavaScriptu pro různé platformy, se skládá z skládající se z aplikací Microsoft AJAX Framework a komponenty skript s názvem knihovně Microsoft AJAX skript kód na straně serveru. Rozšíření ASP.NET AJAX také poskytují podporu pro různé platformy pro přístup k webovým službám ASP.NET prostřednictvím JavaScriptu.

Tento dokument White Paper zkoumá částečná stránka aktualizace funkce rozšíření ASP.NET AJAX, který obsahuje součást ScriptManager, ovládací prvek UpdatePanel a ovládací prvek UpdateProgress a bere v úvahu scénáře, ve kterých by měl nebo neměl využívá.

Tento dokument White Paper vychází z verze beta verzi 2 sady Visual Studio 2008 a rozhraní .NET Framework 3.5, která integruje rozšíření ASP.NET AJAX v knihovně tříd Base (Pokud byla dříve doplněk komponenty, která je k dispozici pro technologii ASP.NET 2.0). Tento dokument White Paper také předpokládá, že používáte Visual Studio 2008 a ne Visual Web Developer Express Edition; Některé šablony projektů, které jsou odkazovány nemusí být dostupné pro uživatele Visual Web Developer Express.

## <a name="partial-page-updates"></a>Částečných aktualizací stránek

Možná nejviditelnější funkce rozšíření ASP.NET AJAX je schopnost provést na stránce částečné nebo přírůstkové aktualizace bez provedení úplného zpětného odeslání na server bez změn kódu a změny minimální značek. Výhody jsou rozsáhlé – stav ve vašich multimédiích (třeba Adobe Flash nebo Windows Media) je beze změny, je snížení nákladů na šířku pásma a klient nedochází k blikání obvykle spojené se zpětné volání.

Schopnost integrovat vykreslování části stránky je integrována do technologie ASP.NET s minimálními změnami do vašeho projektu.

## <a name="walkthrough-integrating-partial-rendering-into-an-existing-project"></a>Průvodce: Integrace částečného zobrazení do existujícího projektu


1. V systému Microsoft Visual Studio 2008, vytvořte nový projekt webu ASP.NET tak, že přejdete do <em>souboru</em>  <em>- &gt; nový</em>  <em>- &gt; webu</em> a vyberete webovou stránku ASP.NET z tohoto dialogového okna. Název můžete libovolně a můžete ji nainstalovat do systému souborů nebo do Internetové informační služby (IIS).
2. Zobrazí se prázdný výchozí stránka s základní kód technologie ASP.NET (formulář na straně serveru a `@Page` – direktiva). Přetáhněte popisek s názvem `Label1` a tlačítko s názvem `Button1` na stránku v rámci elementu formuláře. Libovolně může nastavit jejich vlastnosti text.
3. V návrhovém zobrazení, klikněte dvakrát na `Button1` pro vygenerování obslužné rutiny použití modelu code-behind. V rámci této obslužné rutiny události nastavit `Label1.Text` na kliknutí na tlačítko! .

**Výpis 1: Kód pro default.aspx před povolením částečného zobrazení**

[!code-aspx[Main](understanding-partial-page-updates-with-asp-net-ajax/samples/sample1.aspx)]

**Výpis 2: (Oříznut) v default.aspx.cs CodeBehind**

[!code-csharp[Main](understanding-partial-page-updates-with-asp-net-ajax/samples/sample2.cs)]

1. Stisknutím klávesy F5 spusťte webovou stránku. Visual Studio vás vyzve k přidání souboru web.config pro povolení ladění; Uděláte to tak. Když kliknete na tlačítko, Všimněte si, aktualizuje stránku ke změně textu v popisku a se vám stručný blikání překreslení stránky.
2. Po zavření okna prohlížeče, vrátí se sadou Visual Studio a na stránce značek. Přejděte dolů na panelu nástrojů sady Visual Studio a najít na kartě s popiskem rozšíření AJAX. (Pokud nemáte na této kartě vzhledem k tomu, že používáte starší verzi rozšíření AJAX nebo Atlas, najdete v návodu k registraci položky panelu nástrojů rozšíření AJAX dále v tomto dokumentu White Paper, nebo nainstalovat aktuální verzi s instalačním programem Windows ke stažení z webu).


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image2.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image1.png)

([Kliknutím ji zobrazíte obrázek v plné velikosti](understanding-partial-page-updates-with-asp-net-ajax/_static/image3.png))


1. <em>Známý problém:</em>Pokud nainstalujete do počítače, který už má nainstalovaný s příponami AJAX technologie ASP.NET 2.0 Visual Studio 2005 Visual Studio 2008, Visual Studio 2008 naimportuje položky panelu nástrojů rozšíření AJAX. Můžete zjistit, zda je tento případ prozkoumáním Popis součástí; slibují by měla verzi 3.5.0.0. Pokud, Řekněme, že verze 2.0.0.0, pak jste naimportovali vaše staré položky panelu nástrojů a bude nutné ručně naimportovat pomocí dialogového okna Výběr položek sady nástrojů v sadě Visual Studio. Nebude možné přidat ovládací prvky verze 2 pomocí návrháře.

2. Před `<asp:Label>` začíná značky, vytvoření řádku prázdné znaky a dvakrát klikněte na ovládací prvek UpdatePanel v panelu nástrojů. Všimněte si, že nový `@Register` direktiva je zahrnuté v horní části stránky, označující, že by měly být naimportovány ovládací prvky v rámci oboru názvů System.Web.UI pomocí `asp:` předponu.
3. Přetáhněte uzavírací `</asp:UpdatePanel>` označit za koncem element přepínače tak, aby prvek je ve správném formátu ovládací prvky popisku a tlačítko zabalena.
4. Po zahájení `<asp:UpdatePanel>` značku, začněte otevřením nové značky. Všimněte si, že technologie IntelliSense zobrazí dvě možnosti. V takovém případě vytvořte `<ContentTemplate>` značky. Ujistěte se, zda zabalit tato značka kolem popisek a tlačítko tak, aby kód je ve správném formátu.


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image5.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image4.png)

([Kliknutím ji zobrazíte obrázek v plné velikosti](understanding-partial-page-updates-with-asp-net-ajax/_static/image6.png))


1. Kdekoli v rámci `<form>` prvek, ovládací prvek ScriptManager zahrnout dvojitým kliknutím na `ScriptManager` položky na panelu nástrojů.
2. Upravit `<asp:ScriptManager>` označit tak, že obsahují atribut `EnablePartialRendering= true`.

**Výpis 3: Kód pro default.aspx s povoleným vykreslováním na částečné**

[!code-aspx[Main](understanding-partial-page-updates-with-asp-net-ajax/samples/sample3.aspx)]

1. Otevřete soubor web.config. Všimněte si, že Visual Studio automaticky přidá odkaz na System.Web.Extensions.dll kompilace.

1. Co je nového v sadě Visual Studio 2008: Soubor web.config, která se dodává s webu technologie ASP.NET šablony projektu automaticky obsahuje všechny potřebné odkazy na rozšíření ASP.NET AJAX a obsahuje opatřený komentáři oddíly, které mohou být zrušení komentářem povolit další informace o konfiguraci funkce. Visual Studio 2005 měli podobné šablony, když byly nainstalované rozšíření AJAX technologie ASP.NET 2.0. Ale v sadě Visual Studio 2008, jsou rozšíření AJAX odhlásit ve výchozím nastavení (to znamená, že se odkazuje ve výchozím nastavení, ale je možné odebrat jako odkazy).


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image8.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image7.png)

([Kliknutím ji zobrazíte obrázek v plné velikosti](understanding-partial-page-updates-with-asp-net-ajax/_static/image9.png))


1. Stisknutím klávesy F5 spusťte svůj web. Všimněte si, jak byly nezbytné pro podporu částečného zobrazení bez změny zdrojového kódu – pouze kód byl změněn.

Když spustíte svůj web, byste měli vidět, že částečného zobrazení teď povolené, protože po kliknutí na tlačítko existuje bez blikání bude ani bude k dispozici žádné změny v pozici posunutí stránky (Tento příklad neukazuje, který). Pokud byste se podívat na zdroji vykreslené stránky po kliknutí na tlačítko, potvrdí, že ve skutečnosti nedošlo k po back – původní text popisku je stále součástí zdrojový kód a popisek má změnit prostřednictvím JavaScriptu.

Visual Studio 2008 zřejmě není součástí předem definované šablony pro webový server s podporou technologie ASP.NET AJAX. Tyto šablony se však k dispozici v rámci sady Visual Studio 2005 byly nainstalované Visual Studio 2005 a rozšíření AJAX technologie ASP.NET 2.0. V důsledku toho konfigurace webového serveru a od verze šablony webu enabled budou pravděpodobně ještě jednodušší, jako šablona by měla obsahovat soubor web.config plně nakonfiguroval (podporují všechny rozšíření ASP.NET AJAX, včetně přístupu k webovým službám a serializaci JSON - JavaScript Object Notation) a zahrnuje UpdatePanel a ContentTemplate služby v rámci hlavní stránky webových formulářů ve výchozím nastavení. Povolení částečného zobrazení se tato stránka výchozí je stejně jednoduché, jako byste nepřešli znovu na 10 kroku tohoto návodu a vkládání ovládacích prvků na stránku.

## <a name="the-scriptmanager-control"></a>Ovládací prvek správce skriptů

## <a name="scriptmanager-control-reference"></a>Odkaz na prvek správce skriptů

Povolené značky vlastnosti:

| **Název vlastnosti** | **Typ** | **Popis** |
| --- | --- | --- |
| AllowCustomErrors-Redirect | BOOL | Určuje, jestli chcete používat vlastní chybová část souboru web.config pro zpracování chyb. |
| AsyncPostBackError – zpráva | String | Získá nebo nastaví chybovou zprávu odeslat klientovi, pokud dojde k chybě. |
| AsyncPostBack-Timeout | Int32 | Získá nebo nastaví výchozí dobu dobu, po kterou musí klient čekat na asynchronní požadavek na dokončení. |
| EnableScript globalizace | BOOL | Získá nebo nastaví, zda je povoleno skriptu globalization. |
| Lokalizace EnableScript | BOOL | Získá nebo nastaví, jestli má skript lokalizace. |
| ScriptLoadTimeout | Int32 | Určuje počet sekund, které jsou povolené pro načítání skriptů do klienta |
| ScriptMode | Výčet (Auto, ladění, vydání, dědí) | Získá nebo nastaví, jestli se má vykreslit verze skriptů |
| ScriptPath | String | Získá nebo nastaví kořenovou cesta k umístění souborů skriptů k odeslání do klienta. |

Vlastnosti pouze pro kód:

| **Název vlastnosti** | **Typ** | **Popis** |
| --- | --- | --- |
| AuthenticationService | AuthenticationService správce | Načte podrobnosti o proxy služby ověřování ASP.NET, které se pošlou do klienta. |
| IsDebuggingEnabled | BOOL | Získá, zda skript a ladění kódu je povolena. |
| IsInAsyncPostback | BOOL | Zjistí, zda je stránka momentálně ve asynchronního požadavku post back. |
| ProfileService | ProfileService správce | Načte podrobnosti o proxy služby profilace ASP.NET, které se pošlou do klienta. |
| Scripts | Kolekce&lt;odkaz na skript&gt; | Získá kolekci skriptových odkazů, které se odešlou do klienta. |
| Služby | Kolekce&lt;odkaz na službu&gt; | Získá kolekci odkazy na proxy webové služby, které se odešlou do klienta. |
| SupportsPartialRendering | BOOL | Zjistí, zda aktuální klient podporuje částečného zobrazení. Pokud tato vlastnost vrátí **false**, všechny požadavky na stránce budou standardní zpětného odeslání. |

Veřejné kód metody:

| **Název metody** | **Typ** | **Popis** |
| --- | --- | --- |
| SetFocus(string) | Typ void | Nastaví zaměření pro klienta pro konkrétní ovládací prvek při dokončení požadavku. |

Descendants značky:

| **Značka** | **Popis** |
| --- | --- |
| &lt;AuthenticationService&gt; | Obsahuje podrobné informace o proxy serveru k ověřovací službě ASP.NET. |
| &lt;ProfileService&gt; | Poskytuje podrobnosti o proxy serveru pro profilování služby technologie ASP.NET. |
| &lt;Skripty&gt; | Poskytuje odkazy na další skript. |
| &lt;ASP: ScriptReference&gt; | Označuje odkaz na konkrétní skriptu. |
| &lt;Service&gt; | Poskytuje další odkazy na webové služby, které se mají generované třídy proxy serveru. |
| &lt;asp:ServiceReference&gt; | Označuje konkrétní odkaz webové služby. |

Ovládací prvek ScriptManager se základní jádro pro rozšíření ASP.NET AJAX. Poskytuje přístup ke knihovně skriptu (včetně systém typů rozsáhlé skript na straně klienta), podporuje částečné vykreslování a poskytuje rozsáhlou podporu pro další služby technologie ASP.NET (jako je například ověřování a profilování, ale také ostatní webové služby). Ovládací prvek ScriptManager také poskytuje podporu globalizace a lokalizace pro skripty klienta.

## <a name="providing-alternative-and-supplemental-scripts"></a>Poskytuje alternativní a další skripty

AJAX rozšíření společnosti Microsoft ASP.NET 2.0 zahrnout kód celý skript v obou ladění a verze vydání jako prostředky v odkazovaných sestaveních, vývojáři libovolně udělovat ScriptManager přesměrovat na vlastní skript soubory, jakož i registraci Další nutných skriptů.

Pokud chcete přepsat výchozí vazby pro typicky zahrnuty skripty (například ty, které podporují Sys.WebForms obor názvů a vlastní psaní system), můžete zaregistrovat `ResolveScriptReference` událost třídy ovládacímu prvku ScriptManager. Když tato metoda je volána, obslužná rutina události má příležitost změnit cestu k souboru skriptu dotyčný; Správce skriptů pak odešle kopii různých nebo vlastní skripty do klienta.

Kromě toho odkazy na skripty (reprezentované `ScriptReference` třídy) může být součástí programově nebo prostřednictvím kódu. Uděláte to tak, buď programově změnit `ScriptManager.Scripts` kolekce, nebo zahrnout `<asp:ScriptReference>` značek v rámci `<Scripts>` značky, který je první úrovně podřízeného ovládacího prvku ScriptManager.

## <a name="custom-error-handling-for-updatepanels"></a>Vlastní zpracování chyb pro komponenty UpdatePanel

I když aktualizace jsou zpracovány pomocí aktivačních událostí určené ovládacích prvků UpdatePanel, podporu pro zpracování chyb a vlastní chybové zprávy zařizuje služba instance ovládacího prvku ScriptManager na stránce. Uděláte to pomocí vystavení události, `AsyncPostBackError`, na stránku, která lze poté poskytnout vlastní logiku zpracování výjimek.

Prostřednictvím události AsyncPostBackError, můžete zadat `AsyncPostBackErrorMessage` vlastnost, která poté způsobí, že výstrahy pole vyvolání po dokončení zpětného volání.

Vlastní nastavení na straně klienta je také možné, namísto použití pole výstrah výchozí. Například můžete chtít zobrazit přizpůsobené `<div>` prvek spíše než výchozí prohlížeč modální dialogové okno. V takovém případě mohou zpracovávat chyby ve skriptu klienta:

**Výpis 5: Zobrazit vlastní chyby skriptu na straně klienta**

[!code-html[Main](understanding-partial-page-updates-with-asp-net-ajax/samples/sample4.html)]

Skript výše velmi jednoduše řečeno, zaregistruje se modul runtime jazyka AJAX na straně klienta pro zpětné volání, po dokončení asynchronního požadavku. Poté zkontroluje, zda byla nahlášena chyba a pokud ano, zpracovává podrobnosti, nakonec označující modulu runtime, že byla chyba zpracována v vlastních skriptů.

## <a name="globalization-and-localization-support"></a>Globalizace a lokalizace podpory

Ovládací prvek ScriptManager poskytuje rozsáhlou podporu pro lokalizaci řetězců skriptu a komponenty uživatelského rozhraní; Toto téma však je mimo rozsah tohoto dokumentu. Další informace najdete v dokumentu White Paper, podpora globalizace v rozšíření ASP.NET AJAX.

## <a name="the-updatepanel-control"></a>Ovládací prvek UpdatePanel

## <a name="updatepanel-control-reference"></a>Odkaz prvku UpdatePanel

Povolené značky vlastnosti:

| **Název vlastnosti** | **Typ** | **Popis** |
| --- | --- | --- |
| Vlastnost ChildrenAsTriggers | bool | Určuje, zda podřízené ovládací prvky automaticky vyvolávají aktualizace zpětného odeslání. |
| RenderMode | výčet (blok, vložené) | Určuje, že vizuálně zobrazí tak, jak obsah. |
| Vlastnost UpdateMode nastavena | výčet (vždy, podmíněné) | Určuje, zda je během částečné vykreslení vždy aktualizovat prvku UpdatePanel nebo pokud je pouze aktualizován, když je spuštění aktivační události. |

Vlastnosti pouze pro kód:

| **Název vlastnosti** | **Typ** | **Popis** |
| --- | --- | --- |
| IsInPartialRendering | bool | Zjistí, zda prvku UpdatePanel podporuje částečného zobrazení pro aktuální požadavek. |
| Objekt ContentTemplate | ITemplate | Získá šablonu kód pro požadavek na aktualizaci. |
| ContentTemplateContainer | Ovládací prvek | Získá programový šablonu pro žádost o aktualizaci. |
| Aktivační procedury | Prvek UpdatePanel - TriggerCollection | Získá seznam aktivačních událostí, které jsou přidružené k aktuální prvek UpdatePanel. |

Veřejné kód metody:

| **Název metody** | **Typ** | **Popis** |
| --- | --- | --- |
| Update() | Typ void | Aktualizuje Zadaný prvek UpdatePanel prostřednictvím kódu programu. Umožňuje serveru požadavek na aktivaci částečné vykreslení jinak untriggered UpdatePanel. |

Descendants značky:

| **Značka** | **Popis** |
| --- | --- |
| &lt;Objekt ContentTemplate&gt; | Určuje kód, který se má použít k vykreslení částečného zobrazení výsledků. Podřízený &lt;asp: UpdatePanel&gt;. |
| &lt;Triggery&gt; | Určuje kolekci *n* ovládací prvky přidružené k aktualizaci tohoto ovládacího prvku UpdatePanel. Podřízený &lt;asp: UpdatePanel&gt;. |
| &lt;asp:AsyncPostBackTrigger&gt; | Určuje, která volá vykreslování části stránky pro daný objekt UpdatePanel. To může nebo nemusí být ovládací prvek jako následníka dotyčný UpdatePanel. Detailní název události. Podřízený &lt;triggery&gt;. |
| &lt;asp:PostBackTrigger&gt; | Určuje ovládací prvek, který způsobí, že celou stránku aktualizovat. To může nebo nemusí být ovládací prvek jako následníka dotyčný UpdatePanel. Detailní objektu. Podřízený &lt;triggery&gt;. |

`UpdatePanel` Ovládací prvek je ovládací prvek, který odděluje citaci obsah na straně serveru, který se účastní částečného zobrazení funkce rozšíření AJAX. Neexistuje žádné omezení počtu ovládací prvky UpdatePanel, které mohou být na stránce a mohou být vnořené. Každý prvek UpdatePanel je izolovaný, tak, aby každý může pracovat nezávisle (může mít dvě komponenty UpdatePanel spuštěné ve stejnou dobu vykreslování nezávisle na stránce postbacku různé části na stránce).

Prvku UpdatePanel řídit primárně obchody s aktivačními procedurami ovládacího prvku – ve výchozím nastavení, libovolný ovládací prvek obsažený v rámci ovládacího prvku UpdatePanel `ContentTemplate` , který vytvoří zpětné volání je zaregistrovaný jako trigger pro prvku UpdatePanel. To znamená, že prvku UpdatePanel můžete pracovat s ovládací prvky vázané na data výchozí (například prvku GridView), s uživatelskými ovládacími prvky a mohou být naprogramovány ve skriptu.

Ve výchozím nastavení při vykreslování části stránky se aktivuje, všechny ovládací prvky UpdatePanel na stránce se aktualizují, určuje, jestli ovládací prvky UpdatePanel definice aktivační události pro tyto akce. Například pokud jedna UpdatePanel definuje ovládací prvek Button a dojde ke kliknutí na ovládací prvek tlačítko, všechny ovládací prvky UpdatePanel na této stránce se budou aktualizovat ve výchozím nastavení. Důvodem je, že ve výchozím nastavení, `UpdateMode` prvku UpdatePanel je nastavena na `Always`. Alternativně může nastavit vlastnost UpdateMode na `Conditional`, což znamená, že prvku UpdatePanel se obnovit jen v případě dosažení konkrétní aktivační události.

## <a name="custom-control-notes"></a>Vlastní ovládací prvek poznámky

Ovládacího prvku UpdatePanel lze přidat uživatelský ovládací prvek nebo vlastního ovládacího prvku; však musí obsahovat stránku, na kterém jsou zahrnuty tyto ovládací prvky také ovládací prvek ScriptManager vlastnost EnablePartialRendering nastavena na **true**.

Jedním ze způsobů, ve kterém vám může počítat při použití vlastní webové ovládací prvky je přepsat chráněnou `CreateChildControls()` metodu `CompositeControl` třídy. Díky tomu můžete vložit ovládacího prvku UpdatePanel mezi podřízené položky ovládacího prvku a okolím, pokud zjistíte, že stránka podporuje částečného zobrazení; v opačném případě můžete jednoduše vrstvy podřízené ovládací prvky do kontejneru `Control` instance.

## <a name="updatepanel-considerations"></a>Důležité informace o prvku UpdatePanel

Prvku UpdatePanel funguje jako něco černé skříňky, obtékání postbacků technologie ASP.NET v rámci kontextu XMLHttpRequest jazyka JavaScript. Existují však důležité informace o výkonu pro berte v úvahu z hlediska chování a rychlost. Chcete-li pochopit, jak funguje prvku UpdatePanel, tak, aby co nejlépe můžete rozhodnout, kdy je vhodné jeho použití, byste měli zkontrolovat AJAX exchange. Následující příklad používá existující lokality a služby, Mozilla Firefox s příponou Firebug (Firebug zaznamená data o XMLHttpRequest).

Vezměte v úvahu formulář, který mimo jiné má poštovní směrovací číslo textové pole, která se má naplnit pole Město a stát na formulář nebo ovládací prvek. Tento formulář nakonec shromažďuje informace o členství, včetně názvu, adrese a kontaktní údaje uživatele. Existuje mnoho aspekty návrhu, abyste vezměte v úvahu, na základě požadavků na určitém projektu.


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image11.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image10.png)

([Kliknutím ji zobrazíte obrázek v plné velikosti](understanding-partial-page-updates-with-asp-net-ajax/_static/image12.png))


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image14.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image13.png)

([Kliknutím ji zobrazíte obrázek v plné velikosti](understanding-partial-page-updates-with-asp-net-ajax/_static/image15.png))


V původní iteraci této aplikace byla vytvořena ovládací prvek, který součástí rozsahu registrační data uživatele, včetně PSČ, Město a stát. Celý ovládací prvek byl zabalen do ovládacího prvku UpdatePanel a přetaženy webového formuláře. Když PSČ je zadané uživatelem, zjistí prvku UpdatePanel událostí (odpovídající událost TextChanged v back endu, tak, že zadáte aktivační události nebo s použitím vlastnost ChildrenAsTriggers vlastností nastavenou na hodnotu true). AJAX příspěvky všechna pole v rámci ovládacího prvku UpdatePanel, protože nezachytává FireBug (viz diagram na pravé straně).

Jak označuje snímek obrazovky se doručují hodnoty z každého ovládacího prvku v rámci prvku UpdatePanel (v tomto případě jsou všechny prázdné), a také pole ViewState. Všechny told víc než 9kb dat se odešle, když ve skutečnosti se jenom pět bajtů dat potřebné k tomu tento konkrétní požadavek. Odpověď je ještě více opakovaném: celkem, je 57kb odeslat klientovi, stačí k aktualizaci textové pole a pole rozevíracího seznamu.

To může být také vás zajímá, zobrazíte jak aktualizace technologie ASP.NET AJAX v prezentaci. Část odpovědi žádosti o aktualizaci prvku UpdatePanel se zobrazí v zobrazení konzoly Firebug na levé straně; je speciálně formulovat kanálu oddělený řetězec, který je porušena klientského skriptu a pak na stránce sestaveny. Konkrétně technologie ASP.NET AJAX nastaví *innerHTML* vlastnost elementu HTML v klientovi, který představuje vaše UpdatePanel. Jako v prohlížeči se znovu generuje modelu DOM, je dojde k mírnému zpoždění, v závislosti na množství informací, které je potřeba zpracovat.

Opětovné vygenerování modelu DOM se aktivuje celou řadu dalších problémů:


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image17.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image16.png)

([Kliknutím ji zobrazíte obrázek v plné velikosti](understanding-partial-page-updates-with-asp-net-ajax/_static/image18.png))


- Pokud je element s fokusem HTML v prvku UpdatePanel, ztratí fokus. Tedy pro uživatele, kteří stisknutí klávesy Tab ukončíte PSČ textového pole, jejich umístěním by byl do textového pole Město. Ale po aktualizaci prvku UpdatePanel zobrazení, formuláře by už neměly mít fokus a stisknutím klávesy Tab by byly spuštěny zvýraznění prvky výběru (například odkazy).
- Pokud libovolný vlastní skript na straně klienta se používá, přístup k modelu DOM prvky, odkazy na trvalé pomocí funkcí může být nefunkční po částečné zpětného odeslání.

Komponenty UpdatePanel nemají být řešení pokrývající vše. Místo toho poskytují rychlé řešení pro určité situace, včetně vytváření prototypů, malé řízení aktualizací a poskytují známému rozhraní pro vývojáře využívající technologii ASP.NET, kteří mohou být obeznámeni s objektový model .NET, ale tak méně pomocí modelu DOM. Existuje mnoho možností, které může vést k vyšší výkon, v závislosti na scénáři aplikace:

- Zvažte použití PageMethods a JSON (JavaScript Object Notation) umožňuje vývojářům volat statické metody na stránce, jako kdyby byla vyvolání volání webové služby. Protože jsou statické metody, je nezbytné; žádný stav skript volajícímu poskytuje parametry a výsledek se vrátí asynchronně.
- Zvažte, pokud jeden ovládací prvek je potřeba použít na několika místech přes aplikaci pomocí webové služby a JSON. Toto opět vyžaduje velmi málo speciální práce a funguje asynchronně.

Začlenění funkcí přes webové služby nebo metody stránky má i nevýhody. Především vývojáře využívající technologii ASP.NET obvykle mají tendenci vytvářet malé součástí funkce do uživatelských ovládacích prvků (soubory .ascx). Metody stránky nemůže být hostovaná v těchto souborech; musí být hostovaný v rámci třídy stránky ASPX skutečný. Webové služby, musí být obdobně hostovaný v rámci třídy .asmx. V závislosti na aplikaci tato architektura porušují jedné zásadě odpovědnosti v této funkce pro jednotlivé součásti je nyní rozloženy nejmíň dva fyzické komponenty, které mohou mít žádné nebo téměř žádné získá na ucelenosti vazby.

Nakonec pokud aplikace vyžaduje, aby se používají komponenty UpdatePanel, podle následujících pokynů by měl pomoci v oblastech řešení potíží a údržby.

- **Vnoření komponenty UpdatePanel co je to možné, nejen v – jednotky, ale i napříč jednotky kódu.** Na stránce, která obaluje ovládací prvek, když tento ovládací prvek obsahuje také ovládacího prvku UpdatePanel, který obsahuje jiný ovládací prvek, který obsahuje ovládacího prvku UpdatePanel, s ovládacího prvku UpdatePanel je například různé jednotky vnoření. To pomáhá udržet vymazat prvky, které by měl být aktualizace a brání neočekávaný aktualizuje na podřízenou položku komponenty UpdatePanel.
- **Zachovat *vlastnost ChildrenAsTriggers* vlastnost nastavena na hodnotu false a explicitně nastavit, která aktivuje události.** Využívají `<Triggers>` kolekce je mnohem lepší způsob, jak zpracování událostí a může zabránit neočekávanému chování, pomáhá s úlohy údržby a vynucení pro vývojáře k vyjádření výslovného souhlasu pro událost.
- **Můžete dosáhnout funkcionality nejmenší jednotka je to možné.** Jak je uvedeno v diskuzi o službě PSČ, zabalení, že pouze minimální snižuje čas na serveru, celkem zpracování a nároky na klienta a serveru exchange, zvýšení výkonu.

## <a name="the-updateprogress-control"></a>Ovládací prvek UpdateProgress

## <a name="updateprogress-control-reference"></a>Odkaz na prvek UpdateProgress

Povolené značky vlastnosti:

| **Název vlastnosti** | **Typ** | **Popis** |
| --- | --- | --- |
| AssociatedUpdate-PanelID | String | Určuje ID ovládacího prvku UpdatePanel, který by měl vykazovat tento prvek UpdateProgress. |
| Hodnotou DisplayAfter | Int | Určuje časový limit v milisekundách před zobrazením tohoto ovládacího prvku po zahájení Asynchronní požadavek. |
| DynamicLayout | bool | Určuje, zda průběh vykreslením dynamicky. |

Descendants značky:

| **Značka** | **Popis** |
| --- | --- |
| &lt;Objekt ProgressTemplate&gt; | Obsahuje nastavení pro obsah, který se zobrazí s tímto ovládacím prvkem šablonu ovládacího prvku. |

Ovládací prvek UpdateProgress poskytuje míru zpětné vazby zajistit zájmu vaši uživatelé v průběhu práce přenosu na server. To může pomoct uživatelům vědět, že provádíte něco, i když nemusí být zřejmé, zejména, protože většina uživatelů se používají na stránku pro aktualizaci a podívat se na stavovém řádku zvýraznit.

Jako poznámka může ovládacích prvků UpdateProgress objevit kdekoli v hierarchii stránek. Ale v případech, ve kterých částečné zpětného volání z podřízeným ovládacím prvkem UpdatePanel (kde ovládacího prvku UpdatePanel je vnořená v rámci jiného prvku UpdatePanel), zpětná vystavení triggerem podřízený prvek UpdatePanel způsobí UpdateProgress šablony, který se má zobrazit podřízené Prvek UpdatePanel, stejně jako nadřazeného ovládacího prvku UpdatePanel. Ale pokud se aktivační událost není přímé podřízeného člena nadřazeného ovládacího prvku UpdatePanel, pak se zobrazí pouze UpdateProgress šablon přidružené k nadřazené.

## <a name="summary"></a>Souhrn

Jsou Microsoft ASP.NET AJAX rozšíření sofistikovanými produkty určené pomáhají při zpřístupnění webového obsahu a poskytnout bohatší uživatelské prostředí pro vaše webové aplikace. Jako součást rozšíření ASP.NET AJAX, ovládací prvky vykreslování části stránky, včetně ovládacích prvků ScriptManager, UpdatePanel a UpdateProgress jsou některé z nejvíc viditelné součástí sady nástrojů.

Součást ScriptManager integruje zřizování klienta jazyka JavaScript pro rozšíření, jakož i umožňuje různé serveru a klientské komponenty pro práci s minimální vývoj investice.

Ovládací prvek UpdatePanel je zřejmý magic pole – Kód v rámci prvku UpdatePanel můžou mít Codebehind na straně serveru a ne aktivovat aktualizaci stránky. Ovládací prvky UpdatePanel mohou být vnořené a může být závislá na ovládací prvky v jiné komponenty UpdatePanel. Ve výchozím nastavení komponenty UpdatePanel zpracovat všechny postbacků vyvolání z jejich podřízených ovládacích prvků, i když tato funkce může být jemně vyladěné, deklarativně nebo prostřednictvím kódu programu.

Při použití ovládacího prvku UpdatePanel, vývojáři měli vědět, který může potenciálně dojít dopad na výkon. Potenciální mezi ně patří webové služby a metody stránky, i když by se měly zvažovat návrhu aplikace.

UpdateProgress, který ovládací prvek umožňuje uživateli vědět, že uživatel nebo mu není ignorována a, že požadavek na pozadí se děje při stránce jinak není teď zrovna nic nedělá reagovat na vstup uživatele. Zahrnuje také možnost přerušit částečného zobrazení výsledků.

Společně tyto nástroje pomáhají vytvářet bohaté a bezproblémové uživatelské prostředí tím, že server fungují méně zřejmé, uživatele a méně přerušení pracovního postupu.

## <a name="bio"></a>Životopis

Scott Cate má práce s Microsoft webových technologiích od roku 1997 a je prezident myKB.com ([www.myKB.com](http://www.myKB.com)) kde mu se specializuje na technologie ASP.NET psaní aplikací, zaměřuje na znalostní báze softwarová řešení založených na. Scott můžete kontaktovat prostřednictvím e-mailové adrese [ scott.cate@myKB.com ](mailto:scott.cate@myKB.com) nebo na svém blogu [ScottCate.com](http://ScottCate.com)

> [!div class="step-by-step"]
> [Next](understanding-asp-net-ajax-updatepanel-triggers.md)
