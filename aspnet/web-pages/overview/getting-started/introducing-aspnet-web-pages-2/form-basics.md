---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/form-basics
title: Představení rozhraní ASP.NET Web Pages – základy formulářů HTML | Dokumentace Microsoftu
author: Rick-Anderson
description: V tomto kurzu se dozvíte základní informace o tom, jak vytvořit vstupní formulář a způsob zpracování vstupu uživatele při použití webových stránek ASP.NET (Razor). A teď...
ms.author: riande
ms.date: 05/28/2015
ms.assetid: 81ed82bf-b940-44f1-b94a-555d0cb7cc98
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/form-basics
msc.type: authoredcontent
ms.openlocfilehash: 28385ac2244ab0bfb38ee5fcbc64e6e11804612b
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021674"
---
<a name="introducing-aspnet-web-pages---html-form-basics"></a>Úvod do ASP.NET Web Pages – základy formulářů HTML
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> V tomto kurzu se dozvíte základní informace o tom, jak vytvořit vstupní formulář a způsob zpracování vstupu uživatele při použití webových stránek ASP.NET (Razor). A teď, když máte databázi, budete používat svoje dovednosti formuláře, umožníte uživatelům najít konkrétní videa v databázi. Předpokládá, že jste dokončili řady prostřednictvím [Úvod k zobrazení dat pomocí rozhraní ASP.NET Web Pages](/aspnet/web-pages/overview/getting-started/introducing-aspnet-web-pages-2/displaying-data).
> 
> Co se dozvíte:
> 
> - Jak vytvořit formulář s využitím standardních prvků HTML.
> - Čtení uživatele o vstup ve formuláři.
> - Jak vytvořit dotaz SQL, selektivně získá data pomocí hledání termín, který uživatel zadává.
> - Jak vám má pole na stránce "nezapomeňte" co uživatel zadal.
>   
> 
> Popsané funkce a technologie:
> 
> - `Request` Objektu.
> - SQL `Where` klauzuli.


## <a name="what-youll-build"></a>Co budete vytvářet

V předchozím kurzu vytvořili databázi, do ní přidat data a pak použít `WebGrid` pomocné rutiny pro zobrazení údajů. V tomto kurzu přidáte vyhledávacího pole, která vám umožní najít filmy konkrétní žánr nebo jejichž název obsahuje libovolné slovo, které zadáte. (Například budete moci vyhledat všechny filmy žánru je "Action" nebo jejichž název obsahuje "Harry" nebo "Adventure.")

Jakmile budete hotovi s tímto kurzem, budete mít na stránce podobný následujícímu:

![Stránka filmů s rozšířením podle tematických a název hledání](form-basics/_static/image1.png)

Výpis části stránky je stejná jako v posledním kurzu &mdash; mřížky. Bude záležet, mřížky se zobrazí pouze filmy, kterého jste hledali.

## <a name="about-html-forms"></a>O formulářích HTML

(Pokud máte zkušenosti s vytvářením formuláře HTML a rozdíl mezi `GET` a `POST`, můžete tuto část přeskočit.)

Formulář obsahuje elementy vstupu uživatele &mdash; textová pole, tlačítka, přepínací tlačítka, zaškrtávací políčka, rozevírací seznamy a tak dále. Uživatelé vyplňte tyto ovládací prvky nebo dle popisu a pak klepnutím na tlačítko odeslání formuláře.

Základní syntaxe kódu HTML formuláře je znázorněna v tomto příkladu:

[!code-html[Main](form-basics/samples/sample1.html)]

Spuštění na stránce tento kód vytvoří jednoduchý formulář, který vypadá jako na tomto obrázku:

![Základní formulář HTML jako vykreslený v prohlížeči](form-basics/_static/image2.png)

`<form>` Element vloží elementy HTML k odeslání. (Jednoduché chybou aby je přidání prvků na stránce, ale pak nezapomeňte znovu vytvořte z nich uvnitř `<form>` elementu. V takovém případě nic se odesílá.) `method` Atribut sděluje prohlížeči, jak odeslat vstup uživatele. Tuto možnost nastavíte `post` Pokud provádíte aktualizaci na server nebo do `get` Pokud jste právě načítání dat ze serveru.

<a id="GET,_POST,_and_HTTP_Verb_Safety"></a>

> [!TIP] 
> 
> **GET, POST a bezpečný přístup z více příkaz HTTP**
> 
> HTTP, protokolu, které prohlížeče a servery používají k výměně informací, je výjimečně jednoduché v její základní operace. Prohlížeče pomocí jenom pár operace provést žádosti o připojení k serverům. Při psaní kódu pro web, je dobré znát tyto příkazy a jak je používat prohlížečem a serverem. Nejčastěji používané příkazy nebo daleko jsou tyto:
> 
> - `GET`. Prohlížeč používá tento příkaz pro načtení něco ze serveru. Například když zadáte adresu URL do prohlížeče, prohlížeč provede `GET` operaci žádosti o stránku, kterou chcete. Pokud stránka obsahuje grafiky, prohlížeč provede další `GET` operace získání bitové kopie. Pokud `GET` operace má k předávání informací na server, informace je předána jako část adresy URL v řetězci dotazu.
> - `POST`. Prohlížeč odesílá `POST` požadavek, aby bylo možné odeslat data do přidáno nebo změněno na serveru. Například `POST` příkaz slouží k vytváření záznamů v databázi nebo měnit stávající. Prohlížeč provede většinu času, po vyplnění formuláře a kliknutí na tlačítko Odeslat, `POST` operace. V `POST` operace, data se předá serveru jsou v těle stránky.
> 
> K rozlišení mezi těchto příkazů je, že `GET` operace by neměl něco na serveru změnit – nebo vložit něco abstraktnějšího způsobem, `GET` operace nemá za následek změnu stavu na serveru. Můžete provádět `GET` operace na stejné prostředky jako tolikrát, kolikrát se vám líbí a neměnit tyto prostředky. (A `GET` operace se často říká, že jako "bezpečné", nebo použít technický termín je *idempotentní*.) Naproti tomu samozřejmě `POST` žádost o něco na serveru změní pokaždé, když provedete tuto operaci.
> 
> Pomůže dva příklady ilustrují tento rozdíl. Při hledání pomocí webu jako Bing nebo Google, vyplňte formulář, který se skládá z jedné textové pole a potom klikněte na tlačítko vyhledat. Prohlížeč provede `GET` operace s hodnotou zadanou do pole předána jako část adresy URL. Použití `GET` operace pro tento typ formuláře je v pořádku, protože operace hledání se nezmění žádné prostředky na server, stačí načte informace.
> 
> Teď se podíváme řazení něco online proces. Vyplňte podrobnosti objednávky a potom klikněte na tlačítko Odeslat. Tato operace bude `POST` žádosti, protože operace způsobí změn na serveru, jako je například nový záznam pořadí, ke změně informací o vašem účtu a možná mnoho dalších změn. Na rozdíl od `GET` operace, které se nemůže opakovat. vaše `POST` žádosti – Pokud jste provedli, pokaždé, když znovu odeslat požadavek, by se vytvořit novou objednávku na serveru. (V takových případech websites často upozornění, ne do více než jednou klikněte na tlačítko pro odeslání, nebo tak, aby vám není formulář znovu odešlete omylem zakáže tlačítko pro odeslání.)
> 
> V tomto kurzu budete používat i `GET` operace a `POST` operace pro práci s formuláře HTML. Vysvětlíme v každé případu důvod, proč je příkaz, který používáte příslušné předplatné.
> 
> (Další informace o příkazů HTTP, najdete v článku [definice metod](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) článku na webu W3C.)


Většina elementy vstupu uživatele jsou ve formátu HTML `<input>` elementy. Vypadají `<input type="type" name="name">,` kde *typ* označuje druh uživatele vstupní ovládací prvek. Ty běžné jsou tyto prvky:

- Textové pole: `<input type="text">`
- Zaškrtněte políčko: `<input type="check">`
- Přepínač: `<input type="radio">`
- Tlačítko: `<input type="button">`
- Tlačítko odešlete: `<input type="submit">`

Můžete také použít `<textarea>` prvku k vytvoření víceřádkového textového pole a `<select>` – element pro vytvoření rozevíracího seznamu nebo posuvný seznam. (Další informace o HTML formulář prvky, naleznete v tématu [formuláře HTML a vstup](http://www.w3schools.com/html/html_forms.asp) na webu W3Schools.)

`name` Atribut je velmi důležité, protože název je, jak získáte hodnota elementu později, jak brzy zjistíte.

Části zajímavé je, vývojář, k čemu se vstupem uživatele. Neexistuje žádné předdefinované chování spojené s těmito elementy. Místo toho budete muset získat hodnoty, které má uživatel zadat nebo vybrat a udělat něco s nimi. Je to, co se dozvíte v tomto kurzu.

> [!TIP] 
> 
> **HTML5 a vstupních formulářů**
> 
> Jak je možné, že, HTML je v přechodném stavu a nejnovější verzi (HTML5) zahrnuje podporu pro intuitivnější způsoby, jak uživatelé můžou zadat informace. Například HTML5 (Vývojář) můžete zjistit, na stránce, že chcete, aby uživatel zadal datum. Prohlížeči můžete pak automaticky zobrazí kalendáře spíše než by uživatel musel ručně zadejte datum. HTML5 je však nová a se zatím nepodporuje ve všech prohlížečích.
> 
> ASP.NET Web Pages podporuje HTML5 vstup v rozsahu, který nemá webového prohlížeče. Pro představu o nové atributy pro `<input>` naleznete HTML5 elementu [HTML &lt;vstupní&gt; zadejte atribut](http://www.w3schools.com/html/html_form_input_types.asp) W3Schools lokality.


## <a name="creating-the-form"></a>Vytvoření formuláře

V nástroji WebMatrix v **soubory** pracovního prostoru, otevřete *Movies.cshtml* stránky.

Za uzavírací `</h1>` značky a před otevírací `<div>` značku `grid.GetHtml` volání, přidejte následující kód:

[!code-html[Main](form-basics/samples/sample2.html)]

Tento kód vytvoří formulář obsahující textové pole s názvem `searchGenre` tlačítka a tlačítka Odeslat. Text pole a odeslání tlačítka jsou uzavřeny v `<form>` elementu jehož `method` atribut je nastaven na `get`. (Nezapomeňte, že pokud nemáte vložit do textového pole a tlačítko uvnitř odeslat `<form>` elementu, nic se po kliknutí na tlačítko Odeslat.) Můžete použít `GET` příkaz tady protože vytváříte formuláře, který provedeny žádné změny na serveru – právě výsledkem vyhledávání. (V předchozím kurzu jste použili `post` metodu, která je, jak odeslat změny na server. Uvidíte, že v dalším kurzu znovu.)

Spuštění stránky. I když nebyly definovány žádné chování pro daný formulář, najdete v článku bude vypadat takto:

![Stránka filmů s vyhledávací pole pro žánr](form-basics/_static/image3.png)

Zadejte hodnotu do textového pole, jako je "Komedie." Pak klikněte na tlačítko **hledání žánr**.

Poznamenejte si adresu URL stránky. Vzhledem k tomu, že nastavíte `<form>` elementu `method` atribut `get`, je zadaná hodnota je teď součástí řetězce dotazu v adrese URL, například takto:

`http://localhost:45661/Movies.cshtml?searchGenre=Comedy`

## <a name="reading-form-values"></a>Čtení hodnot formuláře

Stránka již obsahuje kód, který získá dat z databáze a zobrazí výsledky v mřížce. Teď budete muset přidat nějaký kód, který čte hodnoty do textového pole, abyste mohli spustit dotaz SQL, který obsahuje hledaný termín.

Vzhledem k tomu, že nastavíte formuláře metoda `get`, si můžete přečíst hodnotu, která byla zadán do textového pole pomocí kódu, jako je následující:

`var searchTerm = Request.QueryString["searchGenre"];`

`Request.QueryString` Objekt ( `QueryString` vlastnost `Request` objekt) obsahuje hodnoty prvků, které byly odeslány jako součást `GET` operace. `Request.QueryString` Obsahuje vlastnost *kolekce* (seznam) hodnot, které jsou odeslány ve formě. Pokud chcete získat všechny jednotlivé hodnoty, zadejte název elementu, který chcete. To je důvod, proč je třeba mít `name` atribut na `<input>` – element (`searchTerm`), který vytváří do textového pole. (Další informace o tom `Request` objektu, najdete v článku [postranního panelu](#BKMK_TheRequestObject) později.)

Je dostatečně jednoduchá načíst hodnotu v textovém poli. Ale pokud uživatel neměli zadávat nic vůbec v textovém poli, ale ke kliknutí na **hledání** i přesto, můžete ignorovat klikněte na položku, protože není nutné nic k vyhledání.

Následující kód představuje příklad, který ukazuje, jak implementovat tyto podmínky. (Není nutné přidat tento kód ještě, můžete to udělat za chvíli)

[!code-csharp[Main](form-basics/samples/sample3.cs)]

Test je rozdělí tímto způsobem:

- Získání hodnoty `Request.QueryString["searchGenre"]`, konkrétně hodnotu, která byla zadán do `<input>` element s názvem `searchGenre`.
- Zjistěte, jestli je prázdný pomocí `IsEmpty` metody. Tato metoda je standardní způsob, jak zjistit, zda něco (například element formuláře) obsahuje hodnotu. Ale ve skutečnosti, které jsou pro vás pouze v případě, že má *není* prázdné, proto...
- Přidat `!` operátor před `IsEmpty` testování. ( `!` Operátor znamená, že logický operátor NOT).

V běžném jazyce, celý `if` podmínka přeloží na následující: *Pokud formuláře searchGenre element není prázdný, pak...*

Tento blok nastaví fáze pro vytvoření dotazu, který používá hledaný termín. Můžete to udělat v další části.

<a id="BKMK_TheRequestObject"></a>

> [!TIP] 
> 
> **Objekt žádosti**
> 
> `Request` Objekt obsahuje všechny informace, které prohlížeč odesílá do vaší aplikace po požadované nebo odeslání stránky. Tento objekt obsahuje všechny informace, které uživatel zadá, jako je textové pole hodnoty nebo soubor k odeslání. Zahrnuje také všechny možné druhy Další informace, jako jsou soubory cookie, hodnoty v řetězci dotazu adresy URL (pokud existuje), cesta k souboru stránky, na kterém běží, typu prohlížeče, které uživatel používá, seznam jazyků, které jsou nastavené v prohlížeči a mnohem víc.
> 
> `Request` Je objekt *kolekce* (list) hodnot. Získat jednotlivé hodnoty z kolekce zadáním jeho názvu:
> 
> `var someValue = Request["name"];`
> 
> `Request` Skutečně zpřístupňuje několik podmnožin. Příklad:
> 
> - `Request.Form` obsahuje hodnoty z elementů v rámci odeslané `<form>` elementu, jestliže je žádost `POST` požadavku.
> - `Request.QueryString` umožňuje pouze hodnoty v řetězci dotazu adresy URL. (V adrese URL jako `http://mysite/myapp/page?searchGenre=action&page=2`, `?searchGenre=action&page=2` část adresy URL je řetězec dotazu.)
> - `Request.Cookies` kolekce poskytuje přístup k souborů cookie, které se odešle do prohlížeče.
> 
> Pro získání hodnoty, o kterém víte je v odeslané podobě, můžete použít `Request["name"]`. Alternativně můžete použít více konkrétních verzí `Request.Form["name"]` (pro `POST` požadavků) nebo `Request.QueryString["name"]` (pro `GET` požadavky). Samozřejmě *název* je název položky, která má získat.
> 
> Název položky, které chcete získat, musí být jedinečné v rámci kolekce, které používáte. To je důvod, proč `Request` objekt, který poskytuje podmnožiny, jako jsou `Request.Form` a `Request.QueryString`. Předpokládejme, že vaše stránka obsahuje element formuláře s názvem `userName` a *také* obsahuje soubor cookie s názvem `userName`. Pokud se zobrazí `Request["userName"]`, je nejednoznačný, zda se má hodnota formuláře nebo soubor cookie. Nicméně pokud se zobrazí `Request.Form["userName"]` nebo `Request.Cookie["userName"]`, už je explicitní v tom, jaká hodnota se má získat.
> 
> Je dobrým zvykem a buďte konkrétní podmnožinu `Request` , že máte zájem, jako je `Request.Form` nebo `Request.QueryString`. Pro jednoduché stránky, které vytváříte v tomto kurzu je pravděpodobně doesn't make skutečně žádný rozdíl. Ale při vytváření složitějších stránek, používá explicitní verzi `Request.Form` nebo `Request.QueryString` vám může pomoct vyhnout problémům, které mohou vzniknout, když se tato stránka obsahuje formulář (nebo více formulářů), soubory cookie, hodnoty řetězce dotazu a tak dále.


## <a name="creating-a-query-by-using-a-search-term"></a>Vytvoření dotazu pomocí hledaný termín.

Teď, když víte, jak získat hledaný termín, který uživatel zadal, můžete vytvořit dotaz, který ji používá. Mějte na paměti, že pokud chcete získat všechny položky video z databáze, používáte dotaz SQL, který bude vypadat jako tento příkaz:

`SELECT * FROM Movies`

Pokud chcete získat jenom určité filmy, budete muset použít dotaz, který zahrnuje `Where` klauzuli. Tato klauzule umožňuje nastavit podmínky, na kterém jsou řádky vrácené dotazem. Tady je příklad:

`SELECT * FROM Movies WHERE Genre = 'Action'`

Je základní formát `WHERE column = value`. Můžete použít různé operátory kromě právě `=`, třeba `>` (větší než) `<` (menší než), `<>` (nerovná se), `<=` (menší než nebo rovna hodnotě), dále, v závislosti na tom, co hledáte.

V případě, že přemýšlíte, příkazy SQL nejsou malá a velká písmena &mdash; `SELECT` je stejný jako `Select` (nebo dokonce `select`). Ale lidé často velké první písmeno klíčová slova v příkazu SQL, jako je třeba `SELECT` a `WHERE`, aby bylo snazší přečíst.

### <a name="passing-the-search-term-as-a-parameter"></a>Předání jako parametru hledaný termín

Vyhledávání pro konkrétní žánr je docela jednoduché (`WHERE Genre = 'Action'`), ale chcete se pokusit vyhledat rozšířením podle tematických, který uživatel zadá. K tomuto účelu vytvoříte jako dotaz SQL, který obsahuje zástupný symbol pro hodnotu k vyhledání. Bude vypadat jako tento příkaz:

`SELECT * FROM Movies WHERE Genre = @0`

Zástupný text je `@` znak následovaný žádným. Jak může odhad, dotaz může obsahovat několik zástupných symbolů a by se pojmenoval `@0`, `@1`, `@2`atd.

Nastavit dotaz a ve skutečnosti předat ji hodnotu, použijte kód podobný tomuto:

[!code-sql[Main](form-basics/samples/sample4.sql)]

Tento kód je podobný co jste už udělali pro zobrazení dat v mřížce. Pouze rozdíly jsou:

- Dotaz obsahuje zástupný symbol (`WHERE Genre = @0"`).
- Dotaz je umístěn do proměnné (`selectCommand`), než je předána dotaz přímo `db.Query` metoda.
- Při volání `db.Query` metodě předáte dotazu a hodnota, která má použít pro zástupný text. (Pokud dotaz obsahuje více zástupných symbolů, by se předat je jako samostatné hodnoty metody.)

Pokud jste v kostce řečeno všechny tyto prvky, získáte následující kód:

[!code-csharp[Main](form-basics/samples/sample5.cs)]

> [!NOTE] 
> 
> **Důležité!** Použití zástupných symbolů (jako je `@0`) k předání hodnot pro příkaz SQL je *velmi důležité* pro zabezpečení. Způsob, jak ho tady vidíte, se zástupnými symboly pro různá data, je jediný způsob, jak je potřeba vytvořit příkazy jazyka SQL.
> 
> Nikdy vytvořit příkaz SQL vložením společně (spojováním) textového literálu a hodnoty, který jste získali od uživatele. Zřetězení uživatelský vstup do příkazu SQL se otevře web *útok prostřednictvím injektáže SQL* kde uživatel se zlými úmysly odešle hodnoty na stránku, která hack vaší databáze. (Další informace v článku [útok prostřednictvím injektáže SQL](https://msdn.microsoft.com/library/ms161953.aspx) webu MSDN.)


## <a name="updating-the-movies-page-with-search-code"></a>Aktualizace na stránce videa pomocí vyhledávání kódu

Nyní můžete aktualizovat kód v *Movies.cshtml* souboru. Pokud chcete začít, nahraďte kód v bloku kódu v horní části stránky s tímto kódem:

[!code-csharp[Main](form-basics/samples/sample6.cs)]

Rozdíl spočívá v tom, že jsme připravili dotaz do `selectCommand` proměnnou, ve které budete předat `db.Query` později. Vložení příkaz jazyka SQL do proměnné můžete změnit příkaz, který je budete používat k vyhledání.

Rovněž jsme odstranili tyto dva řádky, které vám jako umístění vyberu zpět později:

[!code-csharp[Main](form-basics/samples/sample7.cs)]

Spusťte dotaz ještě nechcete (tedy volání `db.Query`) a nechcete, aby se inicializovat `WebGrid` pomocné rutiny, ale buď. Můžete udělat tyto věci po stanovíte příkazu SQL, který má spustit.

Po tomto přepsaný bloku můžete přidat nové logiku pro zpracování vyhledávání. Dokončený kód bude vypadat nějak takto. Aktualizace kódu na stránce tak, aby odpovídala takto:

[!code-cshtml[Main](form-basics/samples/sample8.cshtml)]

Na stránce teď funguje tímto způsobem. Pokaždé, když běží na stránce kódu se otevře databáze a `selectCommand` proměnná je nastavená na příkazu SQL, který získá všechny záznamy z `Movies` tabulky. Kód také inicializuje `searchTerm` proměnné.

Ale pokud obsahuje hodnotu pro aktuální požadavek `searchGenre` elementu, nastaví kód `selectCommand` na jiný dotaz – konkrétně na takový, který obsahuje `Where` klauzule hledání rozšířením podle tematických. Nastaví také `searchTerm` na cokoli, co byl předán pro vyhledávací pole (který může být nothing).

Bez ohledu na to, které SQL příkaz je v `selectCommand`, kód zavolá `db.Query` spusťte dotaz, předají se jí libovolné je v `searchTerm`. Pokud neobsahují nic `searchTerm`, nezáleží, protože v takovém případě zde není žádný parametr předat hodnotu `selectCommand` přesto.

Nakonec kód inicializuje `WebGrid` pomocné rutiny s využitím výsledky dotazu, stejně jako předtím.

Vidíte, že vložením příkazu jazyka SQL a hledaný termín do proměnné, jste přidali flexibilitu kódu. Jak uvidíte později v tomto kurzu, můžete použít tento základním rozhraním a pokračujte v přidávání logiku pro různé typy vyhledávání.

## <a name="testing-the-search-by-genre-feature"></a>Testování funkcí hledání podle žánru

V nástroji WebMatrix, spusťte *Movies.cshtml* stránky. Zobrazí se stránka s textovým polem pro žánr.

Zadejte genre, který jste zadali pro jeden záznamů testu a pak klikněte na **hledání**. Tentokrát se zobrazí seznam právě videa, které odpovídají této žánr:

![Filmy stránku se seznamem po hledání žánr Comedies](form-basics/_static/image4.png)

Zadejte jiný žánr a hledat znovu. Zkuste zadat žánr prostřednictvím všechna písmena malá písmena nebo všechna velká písmena, takže se zobrazí, hledání nerozlišuje velká a malá písmena.

## <a name="remembering-what-the-user-entered"></a>"Zapamatování" co uživatel zadal

Jste si možná všimli, který poté, co zadali rozšířením podle tematických a kliknutí na **hledání žánr**, jste viděli, výpis, pro nějž. Však byla prázdná pole Hledat text &mdash; jinými slovy, neměli pamatovat, co jste nezadali.

Je důležité pochopit, proč k tomuto chování dochází. Při odesílání stránky prohlížeč odešle požadavek na webový server. Při žádosti ASP.NET, vytvoří instanci zcela nové stránky, spustí kód, který v něm a pak znovu vykreslí stránku v prohlížeči. V důsledku toho však stránce neví, že jste právě pracovali s předchozí verzí sebe sama. Všechny že ví je, že byla přijata žádost, proběhly nějaké data formuláře v ní.

Pokaždé, když požádáte o stránku &mdash; , jestli se poprvé nebo jeho odesláním &mdash; získáváte novou stránku. Webový server nemá žádnou paměť vaší poslední žádosti. Ani nemá technologie ASP.NET a ani se do prohlížeče. Pouze připojení mezi tyto samostatné instance stránky se všechna data, která se přenášejí mezi nimi. Pokud odešlete stránku, například novou instanci stránku získat data formuláře, který odeslal předchozí instanci. (Další způsob, jak předávat data mezi stránkami je používat soubory cookie.)

Formální způsob, jak popisují tato situace je říct, že webové stránky jsou *bezstavové*. Webové servery a samotné stránky a prvky na stránce nemají žádné informace o stavu předchozí stránky. Web byl navržen tímto způsobem, protože zachování stavu pro jednotlivé požadavky by rychle vyčerpat prostředky webové servery, které často zpracovávají tisíce, možná i stovky tisíc požadavků za sekundu.

To je důvod, proč byla prázdná do textového pole. Po odeslání stránky technologie ASP.NET vytvoří novou instanci třídy stránky a spustili pomocí kódu a kódu. V tomto kódu, který má technologie ASP.NET k vložení hodnoty do textového pole nic došlo. ASP.NET tak neprovedli nic, a do textového pole byl vykreslen bez hodnoty v ní.

Není ve skutečnosti snadný způsob, jak tento problém jsme obešli. Genre, kterou jste zadali do textového pole *je* k dispozici v kódu &mdash; se `Request.QueryString["searchGenre"]`.

Aktualizujte kód pro textové pole tak, aby `value` atribut získá svou hodnotu z `searchTerm`, jako v tomto příkladu:

[!code-html[Main](form-basics/samples/sample9.html?highlight=1)]

Na této stránce můžete mít také nastavit `value` atribut `searchTerm` proměnné, protože tato proměnná obsahuje také žánr jste zadali. Ale pomocí `Request` nastavíte `value` atributu, jak je znázorněno zde je standardní způsob, jak tento úkol provést. (Za předpokladu, že jste ještě chcete provést &mdash; v některých případech může být vhodné k vykreslení stránky *bez* hodnoty v polích. Všechno závisí na co se děje s vaší aplikací.)

> [!NOTE]
> "Nepamatujete" hodnotu textového pole, který se používá pro hesla. Bylo by bezpečnostní riziko lidí nabídnout vyplnit pole pro heslo pomocí kódu.


Znovu spusťte stránky, zadejte rozšířením podle tematických a klikněte na **hledání žánr**. Tentokrát nejen zobrazí výsledky hledání, ale do textového pole si pamatuje zadáte čas poslední:

![Stránka zobrazující, že do textového pole má "uloží" předchozí záznam](form-basics/_static/image5.png)

## <a name="searching-for-any-word-in-the-title"></a>Vyhledat libovolné slovo v názvu

Můžete teď vyhledat všechny žánr, ale můžete také vyhledat název. Je těžké získat přesně správný název, při hledání, takže místo toho že můžete hledat slova, která se zobrazí kdekoliv v názvu. K tomu v SQL, můžete použít `LIKE` operátor a syntaxe, jako je následující:

`SELECT * FROM Movies WHERE Title LIKE '%adventure%'`

Tento příkaz načte všechny filmy, jejichž názvy obsahují "adventure". Při použití `LIKE` operátoru, zahrnují zástupný znak `%` jako součást hledaný termín. Hledání `LIKE 'adventure%'` znamená "od"adventure"". (Technicky vzato znamená "Řetězec následovaný nic adventure.") Obdobně hledaný termín `LIKE '%adventure'` znamená "cokoliv, za nímž následuje řetězec"adventure"", což je další způsob, jak Řekněme, že "končí"adventure"".

Hledaný termín `LIKE '%adventure%'` proto znamená "s"adventure"kdekoliv v názvu." (Technicky vzato "vše v názvu, za nímž následuje"adventure", za nímž následuje nic.")

Uvnitř `<form>` elementu, přidejte následující značky vpravo pod uzavírací `</div>` značky pro hledání žánru (těsně před uzavírací `</form>` element):

[!code-html[Main](form-basics/samples/sample10.html)]

Kód pro zpracování tohoto hledání je podobný kód pro hledání genre, s tím rozdílem, že budete muset sestavit `LIKE` vyhledávání. Uvnitř bloku kódu v horní části stránky, přidejte tuto `if` hned za block `if` bloku žánr hledání:

[!code-csharp[Main](form-basics/samples/sample11.cs)]

Tento kód používá stejnou logiku, která jste viděli dříve, s tím rozdílem, že používá hledání `LIKE` operátor a vloží kód "`%`" před a za hledaný termín.

Všimněte si, jak snadno tak člověk mohl přidat jiné hledání na stránku. Všechno, co jste museli dělat byl:

- Vytvoření `if` blok, který Pokud chcete zobrazit, zda pole Hledat relevantní měl hodnotu.
- Nastavte `selectCommand` proměnné pro nový příkaz SQL.
- Nastavte `searchTerm` proměnnou pro hodnotu pro předání do dotazu.

Tady je kompletní kód blok, který obsahuje nový logiku pro hledání názvu:

[!code-cshtml[Main](form-basics/samples/sample12.cshtml)]

Toto je souhrn toho, co dělá takto:

- Proměnné `searchTerm` a `selectCommand` jsou inicializovány v horní části. Budete k nastavení těchto proměnných na odpovídající hledaným termínem (pokud existuje) a odpovídající příkaz SQL podle uživatel nemá na stránce. Výchozí hledání je jednoduchý případ získat všechny filmy z databáze.
- V testech pro `searchGenre` a `searchTitle`, nastaví kód `searchTerm` na hodnotu, kterou chcete vyhledat. Tyto bloky kódu také nastavit `selectCommand` příslušný příkaz SQL pro toto hledání.
- `db.Query` Metoda vyvolána pouze jednou, pomocí každý příkaz SQL je v `selectedCommand` a libovolné hodnota `searchTerm`. Pokud není žádný hledaný termín (žádné žánr a žádný název word), hodnota `searchTerm` je prázdný řetězec. Nicméně, nezáleží, protože v takovém případě dotazu nevyžaduje parametr.

## <a name="testing-the-title-search-feature"></a>Testování funkcí hledání názvu

Nyní můžete otestovat stránky dokončené vyhledávání. Spustit *Movies.cshtml*.

Zadejte rozšířením podle tematických a klikněte na tlačítko **hledání žánr**. V mřížce zobrazené filmy genre, stejně jako před.

Zadejte slovo, název a klikněte na tlačítko **hledání názvu**. V mřížce zobrazené filmy, které mají v názvu toto slovo.

![Filmy stránku se seznamem po vyhledání "Na" v názvu](form-basics/_static/image6.png)

I textová pole ponechte prázdné a klikněte na tlačítko buď tlačítko. V mřížce zobrazené všechny filmy.

## <a name="combining-the-queries"></a>Kombinování dotazy

Můžete si všimnout, že se hledání, které můžete provádět vylučují. Hledání názvu a žánr ve stejnou dobu, i v případě, že oba vyhledávacích polí mají hodnoty v nich. Nelze například vyhledat všechny filmy akce, jejichž název obsahuje "Adventure". (Na stránce je nyní kódem, pokud zadáte hodnoty pro žánr a název, hledání názvu získá prioritu.) Pokud chcete vytvořit vyhledávání, které kombinuje podmínky, je nutné vytvořit dotaz SQL, který má následující syntaxi:

`SELECT * FROM Movies WHERE Genre = @0 AND Title LIKE @1`

A je třeba spustit dotaz s použitím příkaz podobný tomuto (zhruba řečeno):

`var selectedData = db.Query(selectCommand, searchGenre, searchTitle);`

Vytváření logiku, která umožní počet permutací kritéria vyhledávání můžete Zapojte se trochu, jak je vidět. Proto jsme vás přestaneme tady.

## <a name="coming-up-next"></a>Chystá se další

V dalším kurzu vytvoříte stránky, která používá formulář umožňuje uživatelům přidávat videa do databáze.

## <a name="complete-listing-for-movie-page-updated-with-search"></a>Úplný seznam pro stránku Movie (aktualizovat pomocí služby Search)

[!code-cshtml[Main](form-basics/samples/sample13.cshtml)]

## <a name="additional-resources"></a>Další prostředky

- [Úvod k programování v prostředí ASP.NET pomocí syntaxe Razor](https://go.microsoft.com/fwlink/?LinkID=202890)
- [Klauzule WHERE SQL](http://www.w3schools.com/sql/sql_where.asp) na webu W3Schools
- [Definice metod](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) článku na webu W3C

> [!div class="step-by-step"]
> [Předchozí](displaying-data.md)
> [další](entering-data.md)
