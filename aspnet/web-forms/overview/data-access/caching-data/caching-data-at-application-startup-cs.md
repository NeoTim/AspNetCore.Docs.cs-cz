---
uid: web-forms/overview/data-access/caching-data/caching-data-at-application-startup-cs
title: Ukládání do mezipaměti dat při spuštění aplikace (C#) | Dokumentace Microsoftu
author: rick-anderson
description: V jakékoli webové aplikaci se některá data často používat a některá data zřídka. Můžeme vylepšit výkon naší aplikace b ASP.NET...
ms.author: riande
ms.date: 05/30/2007
ms.assetid: 22ca8efa-7cd1-45a7-b9ce-ce6eb3b3ff95
msc.legacyurl: /web-forms/overview/data-access/caching-data/caching-data-at-application-startup-cs
msc.type: authoredcontent
ms.openlocfilehash: c97058e5fd54dfd0393ec5ad020ad957d9719784
ms.sourcegitcommit: f202864efca81a72ea7120c0692940c40d9d0630
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51635326"
---
<a name="caching-data-at-application-startup-c"></a>Ukládání dat do mezipaměti při spuštění aplikace (C#)
====================
podle [Scott Meisnerová](https://twitter.com/ScottOnWriting)

[Stáhnout PDF](caching-data-at-application-startup-cs/_static/datatutorial60cs1.pdf)

> V jakékoli webové aplikaci se některá data často používat a některá data zřídka. Načítání dat často používané techniky označované jako předem jsme lze vylepšit výkon naší aplikace technologie ASP.NET. Tento kurz ukazuje jeden ze způsobů proaktivní načítání, který je k načtení dat do mezipaměti při spuštění aplikace.


## <a name="introduction"></a>Úvod

Dva předchozích kurzech se podívali na ukládání dat v prezentaci a ukládání do mezipaměti vrstvy do mezipaměti. V [ukládání dat do mezipaměti ovládacím prvkem ObjectDataSource](caching-data-with-the-objectdatasource-cs.md), jsme se podívali na pomocí ukládání do mezipaměti funkce ObjectDataSource ukládání dat do mezipaměti v prezentační vrstvě. [Ukládání do mezipaměti dat v architektuře](caching-data-in-the-architecture-cs.md) prověřit, ukládání do mezipaměti v nové, samostatných vrstev ukládání do mezipaměti. Obě tyto kurzy používá *reaktivní načítání* při práci s datovou mezipaměť. S reaktivní načítání pokaždé, když data požádá, systém nejprve zkontroluje, jestli v mezipaměti. Pokud ne, získá data z původního zdroje, jako je například databáze a pak je uloží do mezipaměti. Hlavní výhodou reaktivní načítání je jeho snadné implementaci. Jedním z jeho nevýhody je jeho nerovnoměrné výkon napříč požadavky. Představte si stránku, která používá ukládání do mezipaměti vrstvy z předchozího kurzu zobrazíte informace o produktu. Když tuto stránku se navštíveny poprvé nebo navštíveny poprvé po data uložená v mezipaměti byl odstraněn z důvodu omezení paměti nebo zadané vypršení s bylo dosaženo, musí data načíst z databáze. Tyto požadavky uživatelů proto bude trvat déle než požadavky uživatelů, které je možné dodávat pomocí mezipaměti.

*Proaktivní načítání* poskytuje strategie správy mezipaměti alternativní této vyhlazuje výkon napříč požadavky načtením dat uložených v mezipaměti, než je potřeba. Proaktivní načítání obvykle používá nějaký proces, který pravidelně kontroluje nebo zasláno oznámení, když byla aktualizace na podkladová data. Tento proces pak aktualizuje mezipaměť zachovat aktuální. Proaktivní načítání je obzvláště užitečné, pokud podkladová data pochází z připojení pomalých databáze, webová služba nebo z jiného zdroje dat zejména pomalá. Ale tento přístup k proaktivní načítání je obtížné implementovat, jak vyžaduje vytváření, Správa a nasazování procesu a zkontrolujte změny v aktualizaci mezipaměti.

Jiné charakter a proaktivní načítání typu, který jsme bude konat v tomto kurzu se načítání dat do mezipaměti při spuštění aplikace. Tento přístup je zvláště užitečné pro ukládání do mezipaměti statická data, jako jsou záznamy v databázi vyhledávacími tabulkami.

> [!NOTE]
> Podrobnější pohled na rozdíly mezi proaktivní a reaktivní načítání, jakož i seznam v oblasti IT, nevýhody a doporučení pro implementaci, najdete [správu jeho obsahu mezipaměti](https://msdn.microsoft.com/library/ms978503.aspx) část [ Ukládání do mezipaměti Průvodce architekturou aplikací .NET Framework](https://msdn.microsoft.com/library/ms978498.aspx).


## <a name="step-1-determining-what-data-to-cache-at-application-startup"></a>Krok 1: Určení, jaká Data do mezipaměti při spuštění aplikace

Ukládání do mezipaměti příklady použití reaktivní načítání, jsme se zaměřili na předchozí dva kurzy práci dobře s daty, která může pravidelně měnit a nepřijímá exorbitantly dlouhé ke generování. Ale pokud se nikdy nemění data uložená v mezipaměti, vypršení platnosti používané reaktivní načítání je nadbytečný. Podobně pokud data do mezipaměti trvá generování mimořádně dlouho, pak tito uživatelé, jejichž požadavky najít prázdný mezipaměti bude mít k prosazování zdlouhavé počkejte podkladová data načte. Zvažte možnost ukládání do mezipaměti statická data a data, která trvá nezvykle dlouho generovat při spuštění aplikace.

Databáze mají mnoho dynamic, často mění hodnoty většina také mít množství statická data. Například prakticky všechny datové modely mít jeden nebo více sloupců, které obsahují konkrétní hodnoty z fixní sadu možností. A `Patients` tabulky databáze může mít `PrimaryLanguage` sloupec, jehož sadu hodnot může být angličtina, španělština, francouzština, ruština, japonština a tak dále. Tyto typy sloupců často, jsou implementovány pomocí *vyhledávacími tabulkami*. Místo uložení řetězce angličtině nebo ve francouzštině `Patients` tabulky, druhou tabulku je vytvořen, který obvykle obsahuje dva sloupce – jedinečný identifikátor a popis řetězce – záznam pro každou možnou hodnotu. `PrimaryLanguage` Sloupec `Patients` tabulka ukládá odpovídající jedinečný identifikátor ve vyhledávací tabulce. Na obrázku 1 pacienta John Doe primární jazyk je angličtina, zatímco Ed Johnsonem je ruština.


![Tabulky jazyky je vyhledávací tabulky, použité v tabulce pacientů](caching-data-at-application-startup-cs/_static/image1.png)

**Obrázek 1**: `Languages` je tabulka vyhledávací tabulka používá `Patients` tabulky


Uživatelské rozhraní pro úpravy nebo vytvoření nového pacienta bude zahrnovat rozevírací seznam povolených jazyků vyplněn záznamy v `Languages` tabulky. Bez ukládání do mezipaměti, navštívené pokaždé, když toto rozhraní je v systému se musí dotazovat `Languages` tabulky. Toto je plýtvání a zbytečné od hodnoty vyhledávací tabulky změnit velmi zřídka, pokud někdy.

Společnost Microsoft může ukládat do mezipaměti `Languages` dat s využitím stejné techniky reaktivní načtení zkontrolován z předchozích kurzů. Reaktivní načítání, ale používá čas vypršení, který není nutný pro data statické vyhledávací tabulky. Při ukládání do mezipaměti pomocí reaktivní načítání by bylo lepší než neexistující ukládání do mezipaměti vůbec, nejlepším řešením je aktivně načíst data tabulky vyhledávání do mezipaměti při spuštění aplikace.

V tomto kurzu se podíváme na tom, jak data v mezipaměti vyhledávací tabulky a dalších statických informací.

## <a name="step-2-examining-the-different-ways-to-cache-data"></a>Krok 2: Zkoumání různé způsoby, jak ukládat Data do mezipaměti

Informace můžete prostřednictvím kódu programu uložit do mezipaměti v aplikaci ASP.NET pomocí různých přístupů. Jsme ve už viděli, jak používat mezipaměť dat v předchozích kurzech. Alternativně objektů můžete prostřednictvím kódu programu uložit do mezipaměti pomocí *statické členy* nebo *stav aplikace*.

Při práci s třídou, obvykle třídu musíte nejprve vytvořit instanci před její členy jsou přístupné. Například pokud chcete vyvolat metodu z jedné ze tříd v našich vrstvy obchodní logiky, musí nejdřív vytvoříme instanci třídy:


[!code-csharp[Main](caching-data-at-application-startup-cs/samples/sample1.cs)]

Předtím, než jsme můžete vyvolat *SomeMethod* nebo pracovat s *SomeProperty*, jsme musíte nejprve vytvořit instanci třídy pomocí `new` – klíčové slovo. *SomeMethod* a *SomeProperty* jsou spojeny s konkrétní instancí. Doba života těchto členů je vázán na životnost jejich přidruženého objektu. *Statické členy*, na druhé straně jsou proměnné, vlastnosti a metody, které jsou odkazy sdíleny mezi *všechny* instancí třídy a v důsledku toho mají životnost co nejdelší třídy. Statické členy jsou rozlišeny pomocí klíčového slova `static`.

Kromě statické členy data můžete uložit do mezipaměti pomocí stav aplikace. Každá aplikace technologie ASP.NET udržuje kolekci název/hodnota, jež jsou sdílena mezi všemi uživateli a stránky aplikace. Tuto kolekci lze přistupovat pomocí [ `HttpContext` třídy](https://msdn.microsoft.com/library/system.web.httpcontext.aspx)společnosti [ `Application` vlastnost](https://msdn.microsoft.com/library/system.web.httpcontext.application.aspx)a použita z třídy stránky technologie ASP.NET použití modelu code-behind takto:


[!code-csharp[Main](caching-data-at-application-startup-cs/samples/sample2.cs)]

Mezipaměti dat poskytuje mnohem bohatší rozhraní API pro ukládání do mezipaměti dat poskytuje mechanismus pro expiries podle času a závislostí, priority položky mezipaměti a tak dále. Statické členy a stav aplikace je nutné přidat tyto funkce vývojářem ručně. Při ukládání dat při spuštění aplikace do mezipaměti po dobu životnosti aplikace, ale výhody mezipaměť dat jsou moot. V tomto kurzu podíváme na kód, který používá všechny tři metody pro ukládání statických dat do mezipaměti.

## <a name="step-3-caching-thesupplierstable-data"></a>Krok 3: Ukládání do mezipaměti`Suppliers`dat tabulky

Northwind databáze tabulky, můžeme implementovat na datum ve neobsahují žádné tradiční vyhledávacími tabulkami. Čtyři DataTables implementované v našich DAL všechny tabulky modelu, jejichž hodnoty jsou nestatická. Místo ztrácet čas přidat nový DataTable DAL nové třídy a metody BLL, pro účely tohoto kurzu teď právě předstírají, že, který `Suppliers` dat této tabulky je statická. Proto jsme mezipaměti tato data při spuštění aplikace.

Pokud chcete začít, vytvořte novou třídu s názvem `StaticCache.cs` v `CL` složky.


![Vytvořte třídu StaticCache.cs ve složce CL](caching-data-at-application-startup-cs/_static/image2.png)

**Obrázek 2**: vytvořit `StaticCache.cs` třídy v `CL` složky


Potřebujeme přidat metodu, která načte data při spuštění do úložiště příslušné mezipaměti, stejně jako metody, které vracejí data z této mezipaměti.


[!code-csharp[Main](caching-data-at-application-startup-cs/samples/sample3.cs)]

Výše uvedený kód používá statické členské proměnné `suppliers`, k ukládání výsledků z `SuppliersBLL` třídy `GetSuppliers()` metodu, která je volána z `LoadStaticCache()` metody. `LoadStaticCache()` Metoda je by neměl být volán při spuštění aplikace. Po načtení těchto dat při spuštění aplikace může volat jakékoli stránky, kterou je potřeba pracovat s daty dodavatele `StaticCache` třídy `GetSuppliers()` metody. Proto volání databáze zobrazíte dodavatelů situace nastane pouze jednou, při spuštění aplikace.

Místo použití statické členské proměnné jako mezipaměť, jsme mohli případně použít stav aplikace nebo data mezipaměti. Následující kód ukazuje třídu retooled použít stav aplikace:


[!code-csharp[Main](caching-data-at-application-startup-cs/samples/sample4.cs)]

V `LoadStaticCache()`, dodavatel informace se uloží do proměnné aplikace *klíč*. Se vrátí jako vhodný typ (`Northwind.SuppliersDataTable`) z `GetSuppliers()`. Když stav aplikace je přístupná ve třídách modelu code-behind stránky technologie ASP.NET s využitím `Application["key"]`, v architektuře, musíme použít `HttpContext.Current.Application["key"]` zajistí aktuální `HttpContext`.

Obdobně mezipaměť dat slouží jako úložiště mezipaměti, jak ukazuje následující kód:


[!code-csharp[Main](caching-data-at-application-startup-cs/samples/sample5.cs)]

Chcete-li přidat položku do mezipaměti dat bez podle času vypršení platnosti, použijte `System.Web.Caching.Cache.NoAbsoluteExpiration` a `System.Web.Caching.Cache.NoSlidingExpiration` hodnoty jako vstupní parametry. Tento konkrétní přetížení mezipaměť dat `Insert` metoda byl vybrán tak, že bychom mohli zadat *priority* položky mezipaměti. Priorita se používá k určení, jaké položky uklizeny z mezipaměti, když nedostatek dostupné paměti. Tady používáme prioritu `NotRemovable`, které zajišťuje, že tato položka mezipaměti vyhráli t úklid.

> [!NOTE]
> V tomto kurzu, stáhněte si implementuje `StaticCache` pomocí statické členské proměnné přístup. Kód pro techniky aplikace stav a data mezipaměti je k dispozici v komentářích v souboru třídy.


## <a name="step-4-executing-code-at-application-startup"></a>Krok 4: Spuštění kódu při spuštění aplikace

Chcete-li spustit kód při prvním spuštění webové aplikace, potřebujeme vytvořit zvláštní soubor s názvem `Global.asax`. Tento soubor může obsahovat obslužné rutiny událostí pro aplikace-, relace – a událostí na úrovni požadavku a je zde kde můžeme přidat kód, který se spustí při každém spuštění aplikace.

Přidat `Global.asax` souboru do kořenového adresáře webové aplikace tak, že kliknete pravým tlačítkem na název projektu webu v Průzkumníku řešení sady Visual Studio a zvolíte Přidat novou položku. Z dialogového okna Přidat novou položku vyberte typ položky Global Application Class a potom klikněte na tlačítko Přidat.

> [!NOTE]
> Pokud už máte `Global.asax` soubor v projektu, Global Application Class typ položky nebudou uvedené v dialogovém okně Přidat novou položku.


[![Přidat soubor Global.asax do kořenového adresáře webové aplikace](caching-data-at-application-startup-cs/_static/image4.png)](caching-data-at-application-startup-cs/_static/image3.png)

**Obrázek 3**: Přidejte `Global.asax` souboru do kořenového adresáře vaše webové aplikace ([kliknutím ji zobrazíte obrázek v plné velikosti](caching-data-at-application-startup-cs/_static/image5.png))


Výchozí hodnota `Global.asax` šablona souboru obsahuje pět metod v rámci na straně serveru `<script>` značky:

- **`Application_Start`** spustí při prvním spuštění webové aplikace
- **`Application_End`** spustí, když se aplikace vypíná
- **`Application_Error`** spustí vždy, když aplikace dosáhne neošetřená výjimka
- **`Session_Start`** provede, když je vytvořena nová relace
- **`Session_End`** spustí, když je relace vypršela nebo opuštění

`Application_Start` Obslužná rutina události je volána pouze jednou během životního cyklu aplikace. Aplikace se spustí při prvním prostředek ASP.NET vyžádaný z aplikace a běží nepřetržitě až do restartování aplikace, které může dojít, tak, že upravíte obsah `/Bin` složky, úprava `Global.asax`, změny obsah v `App_Code` složky nebo změny `Web.config` souboru, mezi další příčiny. Odkazovat na [Přehled životního cyklu aplikací ASP.NET](https://msdn.microsoft.com/library/ms178473.aspx) podrobnější informace o životním cyklu aplikace.

Pro tyto kurzy musíme pouze přidáním kódu `Application_Start` metody, takže teď můžete odebrat ostatní. V `Application_Start`, jednoduše zavolejte `StaticCache` třídy `LoadStaticCache()` metodu, která se načtou a mít informace dodavateli v mezipaměti:


[!code-aspx[Main](caching-data-at-application-startup-cs/samples/sample6.aspx)]

To je všechno je to! Při spuštění aplikace `LoadStaticCache()` metoda získejte informace o dodavateli z knihoven BLL a uložte ho statické členské proměnné (nebo libovolné mezipaměti můžete ukládat skončila pomocí `StaticCache` třídy). Pokud chcete ověřit toto chování, nastavte zarážku v `Application_Start` – metoda a spusťte aplikaci. Všimněte si, že je zarážka dosažena při spuštění aplikace. Další požadavky, ale nezpůsobí `Application_Start` metodu provést.


[![Použijte zarážku pro ověřte, zda obslužná rutina události Application_Start prováděný](caching-data-at-application-startup-cs/_static/image7.png)](caching-data-at-application-startup-cs/_static/image6.png)

**Obrázek 4**: použijte zarážku pro ověření, který `Application_Start` obslužná rutina události je spouštěna ([kliknutím ji zobrazíte obrázek v plné velikosti](caching-data-at-application-startup-cs/_static/image8.png))


> [!NOTE]
> Pokud není dosáhnete `Application_Start` zarážce při prvním spuštění ladění, je proto, že vaše aplikace je již spuštěna. Vynutit restartování úpravou aplikace vaše `Global.asax` nebo `Web.config` soubory a pak to zkuste znovu. Můžete jednoduše přidat (nebo odebrání) prázdný řádek na konci některý z těchto souborů rychlé restartování aplikace.


## <a name="step-5-displaying-the-cached-data"></a>Krok 5: Zobrazení Data uložená v mezipaměti

V tomto okamžiku `StaticCache` třída má verzi dodavatele data v mezipaměti při spuštění aplikace, který je přístupný prostřednictvím jeho `GetSuppliers()` metoda. Pro práci s těmito daty od prezentační vrstvy, můžeme použít prvku ObjectDataSource nebo programově volat `StaticCache` třídy `GetSuppliers()` metoda ze stránky technologie ASP.NET použití modelu code-behind třídy. Podívejme se na použití ObjectDataSource a GridView ovládacích prvků pro zobrazení informací v mezipaměti dodavatele.

Začněte otevřením `AtApplicationStartup.aspx` stránku `Caching` složky. Přetáhněte z panelu nástrojů do Návrháře nastavení GridView jeho `ID` vlastnost `Suppliers`. V dalším kroku v prvku GridView inteligentních značek zvolte k vytvoření nového prvku ObjectDataSource s názvem `SuppliersCachedDataSource`. Konfigurace ObjectDataSource používat `StaticCache` třídy `GetSuppliers()` metody.


[![Konfigurace ObjectDataSource pomocí třídy StaticCache](caching-data-at-application-startup-cs/_static/image10.png)](caching-data-at-application-startup-cs/_static/image9.png)

**Obrázek 5**: Konfigurace ObjectDataSource používat `StaticCache` třídy ([kliknutím ji zobrazíte obrázek v plné velikosti](caching-data-at-application-startup-cs/_static/image11.png))


[![Pomocí této metody GetSuppliers() načítat Data uložená v mezipaměti dodavatele](caching-data-at-application-startup-cs/_static/image13.png)](caching-data-at-application-startup-cs/_static/image12.png)

**Obrázek 6**: použijte `GetSuppliers()` metodu pro načtení dat do mezipaměti dodavatele ([kliknutím ji zobrazíte obrázek v plné velikosti](caching-data-at-application-startup-cs/_static/image14.png))


Po dokončení průvodce, Visual Studio automaticky přidá BoundFields pro každé pole data v `SuppliersDataTable`. GridView a ObjectDataSource deklarativní by měl vypadat nějak takto:


[!code-aspx[Main](caching-data-at-application-startup-cs/samples/sample7.aspx)]

Obrázek 7 znázorňuje stránky při prohlížení prostřednictvím prohlížeče. Výstup je stejný měli jsme načetli data z BLL `SuppliersBLL` třídy, ale používat `StaticCache` třídy vrací data dodavatele jako uložená v mezipaměti při spuštění aplikace. Můžete nastavit zarážky `StaticCache` třídy `GetSuppliers()` metodu k ověření tohoto chování.


[![Poskytovatel dat do mezipaměti se zobrazí v GridView](caching-data-at-application-startup-cs/_static/image16.png)](caching-data-at-application-startup-cs/_static/image15.png)

**Obrázek 7**: v mezipaměti Data dodavatele se zobrazí v GridView ([kliknutím ji zobrazíte obrázek v plné velikosti](caching-data-at-application-startup-cs/_static/image17.png))


## <a name="summary"></a>Souhrn

Většina každý datový model obsahuje množství statických dat, obvykle implementována ve formě vyhledávacími tabulkami. Protože tyto informace jsou statické, neexistuje žádný důvod k průběžně pokaždé, když přistupují k databázi, že tyto informace musí být zobrazena. Navíc díky jejich statické povaze při ukládání do mezipaměti tamní data je potřeba vypršení platnosti. V tomto kurzu jsme viděli, jak přijmout taková data a ukládat do mezipaměti se mezipaměť dat, stavu aplikace a prostředí statické členské proměnné. Tyto informace se uloží do mezipaměti při spuštění aplikace a zůstává v mezipaměti v průběhu životního cyklu aplikace.

V tomto kurzu a posledních dvou jsme ve podívali se na ukládání dat do mezipaměti po dobu trvání životnosti aplikace a jak používat expiries podle času. Při ukládání do mezipaměti dat z databáze, ale vypršení platnosti podle času může být menší než ideální. Místo pravidelně vyprazdňování mezipaměti, bylo by optimální pouze vyřazení položku z mezipaměti, když se změní podkladová data databáze. Je možné prostřednictvím použití závislostí mezipaměti SQL, které prozkoumáme v našem dalším kurzu této ideální.

Všechno nejlepší programování!

## <a name="about-the-author"></a>O autorovi

[Scott Meisnerová](http://www.4guysfromrolla.com/ScottMitchell.shtml), Autor sedm ASP/ASP.NET knih a Zakladatel [4GuysFromRolla.com](http://www.4guysfromrolla.com), má práce s Microsoft webových technologiích od roku 1998. Scott funguje jako nezávislý konzultant, trainer a zapisovače. Jeho nejnovější knihy [ *Edice nakladatelství Sams naučit sami ASP.NET 2.0 za 24 hodin*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Může být dosáhl v [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) nebo prostřednictvím jeho blogu, který lze nalézt v [ http://ScottOnWriting.NET ](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Speciální k

V této sérii kurzů byl recenzován uživatelem mnoho užitečných revidující. Vedoucí revidující pro účely tohoto kurzu byly Teresy Murphy a Zack Jones. Zajímat téma Moje nadcházejících článcích MSDN? Pokud ano, vyřaďte mě řádek na [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

> [!div class="step-by-step"]
> [Předchozí](caching-data-in-the-architecture-cs.md)
> [další](using-sql-cache-dependencies-cs.md)
