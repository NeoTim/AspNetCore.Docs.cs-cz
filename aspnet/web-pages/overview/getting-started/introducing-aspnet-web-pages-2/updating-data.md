---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/updating-data
title: Představení rozhraní ASP.NET Web Pages – aktualizace databázových dat | Dokumentace Microsoftu
author: Rick-Anderson
description: V tomto kurzu se dozvíte, jak aktualizovat záznam (změnit) existující databáze při použití webových stránek ASP.NET (Razor). Předpokládá, že jste dokončili řady th...
ms.author: riande
ms.date: 01/02/2018
ms.assetid: ac86ec9c-6b69-485b-b9e0-8b9127b13e6b
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/updating-data
msc.type: authoredcontent
ms.openlocfilehash: 206d7e209857aceb3eb92c2405bb73f7ff7dbaeb
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021427"
---
<a name="introducing-aspnet-web-pages---updating-database-data"></a>Úvod do webových stránek ASP.NET – aktualizace databázových dat
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> V tomto kurzu se dozvíte, jak aktualizovat záznam (změnit) existující databáze při použití webových stránek ASP.NET (Razor). Předpokládá, že jste dokončili řady prostřednictvím [vstupní Data podle používání Forms pomocí rozhraní ASP.NET Web Pages](entering-data.md).
> 
> Co se dozvíte:
> 
> - Jak vybrat jednotlivý záznam v `WebGrid` pomocné rutiny.
> - Jak si jeden záznam z databáze.
> - Postup předběžné načtení formuláře s hodnotami ze záznamu v databázi.
> - Jak aktualizovat stávající záznam v databázi.
> - Jak ukládat informace na stránce bez zobrazení.
> - Jak používat skrytého pole k ukládání informací.
>   
> 
> Popsané funkce a technologie:
> 
> - `WebGrid` Pomocné rutiny.
> - SQL `Update` příkazu.
> - `Database.Execute` Metody.
> - Skryté pole (`<input type="hidden">`).


## <a name="what-youll-build"></a>Co budete vytvářet

V předchozím kurzu jste zjistili, jak přidat záznam do databáze. Tady se dozvíte, jak zobrazit záznam pro úpravy. V *filmy* stránky, budete aktualizovat `WebGrid` pomocné rutiny, takže se zobrazí **upravit** odkaz vedle každý film:

![Zobrazit WebGrid včetně odkaz pro "Úpravy" pro každý film](updating-data/_static/image1.png)

Když kliknete **upravit** odkaz, tím přejdete na jinou stránku, kde je video informace již ve formě:

![Upravit video stránky zobrazující film bude upravován](updating-data/_static/image2.png)

Můžete změnit libovolné hodnoty. Když odešlete změny kódu na stránce aktualizuje databázi a přejde zpět do seznamu video.

Tato část procesu funguje skoro stejně jako *AddMovie.cshtml* stránky, kterou jste vytvořili v předchozím kurzu, tak velkou část tohoto kurzu bude známé.

Je možné implementovat způsob, jak upravit jednotlivé film několika způsoby. Tento přístup je znázorněno byla vybrána, protože je snadno pochopitelný a snadno se implementuje.

## <a name="adding-an-edit-link-to-the-movie-listing"></a>Přidání odkaz pro úpravy seznamu Movie

Pokud chcete začít, budete aktualizovat *filmy* stránce tak, že každý film výpis také obsahuje **upravit** odkaz.

Otevřít *Movies.cshtml* souboru.

V těle stránky, změňte `WebGrid` kód tak, že přidáte sloupec. Tady je upravený kód:

[!code-html[Main](updating-data/samples/sample1.html?highlight=6)]

Nový sloupec se tohle:

[!code-html[Main](updating-data/samples/sample2.html)]

Bod v tomto sloupci se má zobrazit odkaz (`<a>` element) jejichž text říká "Upravit". Co jsme po je vytvořit odkaz, který vypadá podobně jako následující při spuštění stránky s `id` hodnotu pro každý film:

[!code-css[Main](updating-data/samples/sample3.css)]

Tento odkaz se vyvolat stránku s názvem *EditMovie*, a předá řetězec dotazu `?id=7` na tuto stránku.

Syntaxe pro nového sloupce, který může vypadat trochu složité, ale pouze důvodem umístí společně několik elementů je. Každý jednotlivý prvek je jednoduché. Pokud se můžete soustředit na jenom `<a>` elementu, zobrazí tento kód:

[!code-html[Main](updating-data/samples/sample4.html)]

Základní informace o tom, jak funguje mřížky: v mřížce zobrazené řádky, jeden pro každý záznam v databázi, a zobrazuje sloupce pro každé pole v záznamu v databázi. Zatímco každý řádek mřížky se vytváří, `item` záznamu v databázi (položky) pro tento řádek obsahuje objekt. Toto uspořádání poskytuje způsob, jak získat data pro tento řádek kódu. Který se zobrazí zde: výraz `item.ID` je stále hodnotu ID aktuální položky databáze. Vám může získat některé z hodnot v databázi (title, rozšířením podle tematických nebo rok) stejným způsobem jako pomocí `item.Title`, `item.Genre`, nebo `item.Year`.

Výraz `"~/EditMovie?id=@item.ID` kombinuje pevně zakódované část cílové adrese URL (`~/EditMovie?id=`) s číslem ID této dynamicky odvozené. (Jste viděli `~` operátor v předchozím kurzu; je operátor technologie ASP.NET, který představuje aktuální kořenové složky webu.)

Výsledkem je, že tuto část kódu ve sloupci jednoduše vytvoří něco jako následující kód za běhu:

[!code-xml[Main](updating-data/samples/sample5.xml)]

Přirozeně, skutečné hodnoty `id` se bude lišit pro každý řádek.

## <a name="creating-a-custom-display-for-a-grid-column"></a>Vytváření vlastních zobrazení pro sloupec mřížky

Vraťme se k sloupci mřížky. Tři sloupce jste původně měli v mřížce se zobrazí pouze hodnoty dat (název, rozšířením podle tematických a roku). Toto zobrazení určený název sloupce databáze &mdash; například `grid.Column("Title")`.

Tato nová **upravit** sloupec propojení se liší. Místo určení názvu sloupce, které předávání `format` parametru. Tento parametr umožňuje definovat kód, který `WebGrid` pomocné rutiny vykreslí spolu s `item` hodnota pro zobrazení dat sloupců jako tučný nebo zelenou nebo ve formátu, který má být. Například pokud byste chtěli název se zobrazí tučně, můžete například vytvořit sloupec jako v tomto příkladu:

[!code-html[Main](updating-data/samples/sample6.html)]

(Různé `@` znaků, které se zobrazí v `format` vlastnost označit přechod mezi značky a hodnoty kódu.)

Jakmile budete vědět o `format` vlastnost, je lze snáze pochopit jak nové **upravit** odkaz sloupce dohromady:

[!code-html[Main](updating-data/samples/sample7.html)]

Sloupec obsahuje *pouze* z kódu, který vykreslí na odkaz, a navíc několik informací (ID), které jsou extrahovány ze záznamu v databázi pro řádek.

> [!TIP]
> 
> **Pojmenované parametry a poziční parametry pro metodu**
> 
> V mnoha případech když jste volali metodu a předat parametry, můžete jednoduše uvedli hodnoty parametrů oddělených čárkami. Tady je několik příkladů:
> 
> `db.Execute(insertCommand, title, genre, year)`
> 
> `Validation.RequireField("title", "You must enter a title")`
> 
> Pokud znáte tento kód, ale v obou případech se předávání parametrů k metodám v určitém pořadí jsme neměli zmiňovat problém &mdash; konkrétně, pořadí, ve kterém jsou parametry definované v této metodě. Pro `db.Execute` a `Validation.RequireFields`, pokud se promíchají pořadí hodnot předáte byste získali chybová zpráva při spuštění stránky nebo alespoň nějaké neobvyklé výsledky. Je zřejmé budete muset vědět předávat parametry v pořadí. (V nástroji WebMatrix, IntelliSense vám můžou pomoct při zjistit název, typ a pořadí parametrů.)
> 
> Jako alternativu k předávání hodnot v pořadí, můžete použít *pojmenované parametry*. (V pořadí předávání parametrů se označuje jako pomocí *poziční parametry*.) Pro pojmenované parametry explicitně zahrnete název parametru předává jeho hodnotu. Využili jste pojmenované parametry již s počtem opakování v těchto kurzech. Příklad:
> 
> [!code-csharp[Main](updating-data/samples/sample8.cs)]
> 
> and
> 
> [!code-css[Main](updating-data/samples/sample9.css)]
> 
> Pojmenované parametry jsou užitečné pro několik situací, zejména v případě, že metoda přijímá velký počet parametrů. Jeden je, když chcete předat pouze jeden nebo dva parametry, ale nejsou hodnoty, které chcete předat mezi první pozice v seznamu parametrů. Je jiné situace, kdy mají být váš kód lépe čitelný předáním parametry v pořadí, ve kterém je pro vás nejvhodnější.
> 
> Samozřejmě pokud chcete použít pojmenované parametry, budete muset znát názvy parametrů. Služba WebMatrix IntelliSense můžete *zobrazit* názvy, ale nemůžete vyplnit aktuálně je pro vás.


## <a name="creating-the-edit-page"></a>Vytvoření stránky pro úpravu

Nyní můžete vytvořit *EditMovie* stránky. Když uživatelé kliknou **upravit** odkaz, na této stránce se zobrazí skončit.

Vytvoření stránky s názvem *EditMovie.cshtml* a nahradit, co je v souboru následujícím kódem:

[!code-cshtml[Main](updating-data/samples/sample10.cshtml)]

Tento kód a kód se podobá mít *AddMovie* stránky. Je malý rozdíl v textu tlačítka pro odeslání. Stejně jako u *AddMovie* stránce, dojde `Html.ValidationSummary` volání, které se zobrazí chyby ověření, pokud tam nějaké jsou. Nyní jsme se přitom volání `Validation.Message`, protože chyby se zobrazí v souhrnu ověření. Jak je uvedeno v předchozím kurzu, můžete použít v různých kombinacích Souhrn ověření a jednotlivé chybové zprávy.

Všimněte si, že znovu, který `method` atribut `<form>` prvek je nastaven na `post`. Stejně jako u *AddMovie.cshtml* stránky, tato stránka provádí změny v databázi. Proto by měl provádět tento formulář `POST` operace. (Další informace o rozdílech mezi `GET` a `POST` operací, najdete v článku [GET, POST a HTTP příkaz bezpečnosti](form-basics.md#GET,_POST,_and_HTTP_Verb_Safety) boční panel v tomto kurzu ve formulářích HTML.)

Jak už jste viděli v předchozí kurzu `value` textových polí se nastavují atributy s kódem Razor k jejich předběžné načtení. Tentokrát ale používáte proměnné jako `title` a `genre` pro tuto úlohu místo `Request.Form["title"]`:

`<input type="text" name="title" value="@title" />`

Jako dříve, tento kód bude předběžné načtení textové hodnoty pole s hodnotami video. Zobrazí se vám za chvíli důvody, proč je užitečný použít proměnné pro tuto chvíli namísto použití `Request` objektu.

K dispozici je také `<input type="hidden">` prvek na této stránce. Tento prvek ukládá ID film bez zviditelnění na stránce. ID původně předána na stránku pomocí hodnoty řetězce dotazu (`?id=7` nebo podobné v adrese URL). Vložením hodnoty ID do skryté pole, abyste měli jistotu, že je k dispozici při odeslání formuláře, i když už nebude mít přístup k původní adresu URL, které stránky se vyvolala s.

Na rozdíl od *AddMovie* stránce kód *EditMovie* stránka má dva různé funkce. První funkce je, že když stránka se zobrazí první (a *pouze* pak), kód získá ID video z řetězce dotazu. Kód pak používá ID na odpovídající videa z databáze pro čtení a zobrazení (přednačtení) ji do textových polí.

Druhá funkce je, že když uživatel klikne **odeslat změny** tlačítko, kód má číst hodnoty do textových polí a ověřit je. Kód má také k aktualizaci položky databáze s novými hodnotami. Tato technika je podobné jako přidání záznamu, protože jste viděli v *AddMovie*.

## <a name="adding-code-to-read-a-single-movie"></a>Přidání kódu pro čtení jednoho videa

K provedení první funkce, tento kód vložte do horní části stránky:

[!code-cshtml[Main](updating-data/samples/sample11.cshtml)]

Většina tento kód je uvnitř bloku, který se spustí `if(!IsPost)`. `!` Operátor znamená "not", takže znamená, že výraz *Pokud tento požadavek není odeslání příspěvku*, což je o tom, že to nepřímé způsob *Pokud tento požadavek je při prvním spuštění této stránce*. Jak je uvedeno výše, tento kód spustit *pouze* při prvním spuštění stránky. Pokud zadáte nepovedlo kód v `if(!IsPost)`, by spustit pokaždé, když se na stránce je vyvolána, zda poprvé nebo v reakci na tlačítku klikněte na tlačítko.

Všimněte si, že tento kód obsahuje `else` blokovat této doby. Jak jsme už uvedli, když jsme představili `if` bloky, někdy chcete spustit alternativní kódu, pokud neplatí stavu testování. Je to tento případ tady. Pokud bude podmínka splněna (tj. Pokud předané na stránku ID je v pořádku), čtení řádku z databáze. Nicméně, pokud podmínka není úspěšný, `else` bloku spuštěn a kód nastaví chybovou zprávu.

## <a name="validating-a-value-passed-to-the-page"></a>Ověřuje se hodnota předána na stránku

Tento kód použije `Request.QueryString["id"]` k získání ID, která je předána na stránku. Kód zajišťuje, že hodnotu byl předán ve skutečnosti identifikátor. Pokud byla předána žádná hodnota, nastaví kód chyby ověřování.

Tento kód ukazuje různé způsob, jak ověřit informace. V předchozím kurzu, kterou jste pracovali `Validation` pomocné rutiny. Jste zaregistrovali pole k ověření a ASP.NET automaticky nebyla ověření a zobrazí chyby pomocí `Html.ValidationMessage` a `Html.ValidationSummary`. V takovém případě však je už ověřování není ve skutečnosti uživatelského vstupu. Místo toho že ověřování hodnotu, která byla předána na stránku odjinud. `Validation` Pomocné rutiny, která neumí za vás.

Proto byste zkontrolovat hodnotu sami, testováním s `if(!Request.QueryString["ID"].IsEmpty()`). Pokud je nějaký problém, můžete zobrazit chyba s použitím `Html.ValidationSummary`, stejně jako `Validation` pomocné rutiny. Chcete-li to mohli udělat, zavolejte `Validation.AddFormError` a předejte jí zprávu zobrazit. `Validation.AddFormError` je integrovaná metoda, která umožňuje definovat vlastní zprávy, které jsou ověřování systému, kterou jste již obeznámeni s. (Dále v tomto kurzu budeme mluvit o tom, aby tento proces ověření trochu výkonnější.)

Jakmile ověříte, že je ID videa, kód čte databázi, hledá jenom položky izolované databáze. (Pravděpodobně jste si všimli obecný vzor databázových operací: Otevřete databázi, definujte příkaz SQL a spuštění příkazu.) Tentokrát SQL `Select` výpis obsahuje `WHERE ID = @0`. Protože ID je jedinečný, může být vrácen pouze jeden záznam.

Dotaz se provádí pomocí `db.QuerySingle` (není `db.Query`, jako jste použili pro výpis film), a vloží kód do výsledku `row` proměnné. Název `row` libovolný; můžete pojmenovat proměnné, jakkoli chcete. Proměnné inicializovány v horní části se pak naplní se podrobnosti o filmu tak, aby tyto hodnoty lze zobrazit v textových polích.

## <a name="testing-the-edit-page-so-far"></a>Testování stránky pro úpravu (zatím)

Pokud chcete otestovat stránku, spusťte *filmy* stránce teď a klikněte na tlačítko **upravit** odkaz vedle libovolné video. Zobrazí se vám *EditMovie* stránky s podrobnostmi o vyplňují za video, které jste vybrali:

![Upravit video stránky zobrazující film bude upravován](updating-data/_static/image3.png)

Všimněte si, že adresa URL stránky obsahuje něco jako `?id=10` (nebo jiné telefonní číslo). Zatím jste otestovali, který **upravit** odkazů v *film* stránce práce, že vaše stránka je čtení ID z řetězce dotazu a pracuje, že databáze dotaz pro získání film jeden záznam.

Můžete změnit informace film, ale nic se nestane po kliknutí na **odeslat změny**.

## <a name="adding-code-to-update-the-movie-with-the-users-changes"></a>Přidání kódu k aktualizaci videa pomocí změny uživatele

V *EditMovie.cshtml* implementovat druhou funkci (ukládají se změny), přidejte následující kód jenom složené závorce `@` bloku. (Pokud si nejste jistí, přesně, kde chcete změnit kód, můžete si prohlédnout [dokončení kódu pro stránku Upravit video](#Complete_Page_Listing_for_EditMovie) , který se zobrazí na konci tohoto kurzu.)

[!code-csharp[Main](updating-data/samples/sample12.cs)]

Znovu, je podobný kód v tomto značek a kódu *AddMovie*. Kód je v `if(IsPost)` blokovat, protože tento kód se spustí pouze v případě, že uživatel klikne **odeslat změny** tlačítko &mdash; to znamená, pokud (a pouze tehdy, když) byl odeslán formulář. V tomto případě, že nepoužíváte testu jako `if(IsPost && Validation.IsValid())`– to znamená, nejsou kombinování oba testy s použitím AND. Na této stránce, je nejprve určit, zda je odeslání formuláře (`if(IsPost)`) a teprve pak zaregistrujte pole pro ověřování. Pak můžete testovat výsledky ověření (`if(Validation.IsValid()`). Tok je mírně odlišná v porovnání s *AddMovie.cshtml* stránky, ale efekt je stejný.

Získat hodnoty do textových polí pomocí `Request.Form["title"]` a podobné pro druhý `<input>` elementy. Všimněte si, že tentokrát kód získá ID film mimo skryté pole (`<input type="hidden">`). Při provádění na stránce první, je kód teď ID mimo řetězec dotazu. Získat hodnotu z skryté pole, abyste měli jistotu, že se zobrazuje ID videa, která byla původně zobrazí v případě, že řetězec dotazu byla nějakým způsobem změněna od té doby.

Velmi důležitý rozdíl mezi *AddMovie* kódu a tento kód je, že v tomto kódu můžete použít SQL `Update` příkaz místo `Insert Into` příkazu. Následující příklad ukazuje syntaxi SQL `Update` – příkaz:

`UPDATE table SET col1="value", col2="value", col3="value" ... WHERE ID = value`

Všechny sloupce, můžete zadat v libovolném pořadí a není nutné nutně aktualizovat každý sloupec během `Update` operace. (ID, nelze aktualizovat, protože záznam, který by ve skutečnosti uložit jako nový záznam a, který není povolen pro `Update` operace.)

> [!NOTE] 
> 
> **Důležité** `Where` klauzule s ID je velmi důležité, protože to je, jak databáze pozná, kterou databázi záznamů, které chcete aktualizovat. Pokud jste minule přestali `Where` klauzule, aktualizovali byste databázi *každý* záznamů v databázi. Ve většině případů, které by byly havárii.


V kódu jsou předány aktualizaci hodnot pro příkaz jazyka SQL s použitím zástupných symbolů. Opakovat, co jsme řekli před: z bezpečnostních důvodů *pouze* použít zástupné znaky pro předání hodnot pro příkaz jazyka SQL.

Jakmile tento kód použije `db.Execute` ke spuštění `Update` příkaz, je přesměrován zpět na stránku seznam, kde můžete sledovat změny.

> [!TIP] 
> 
> **Různé SQL příkazy, různé metody**
> 
> Jste si možná všimli, že používáte mírně odlišné metody spouští různé příkazy SQL. Ke spuštění `Select` dotaz, který potenciálně vrací více záznamů, je použít `Query` metody. Ke spuštění `Select` dotaz, který vrátí pouze jednu položku databáze, je použít `QuerySingle` metody. Ke spuštění příkazů, provést změny, ale který nevracejte položky databáze, můžete použít `Execute` metody.
> 
> Je třeba mít různé metody, protože každá z nich vrací různé výsledky, jak jste už viděli v rozdílu mezi `Query` a `QuerySingle`. ( `Execute` Ve skutečnosti vrátí metoda hodnotu také &mdash; konkrétně, počet řádky databáze, které byly ovlivněny příkaz &mdash; ale můžete jste byla ignorována, který zatím.)
> 
> Samozřejmě `Query` metoda může vrátit pouze jeden řádek. Ale ASP.NET vždy zpracovává výsledky `Query` metoda jako kolekce. I v případě, že metoda vrátí pouze jeden řádek, budete muset extrahovat tento jeden řádek z kolekce. Proto v situacích, kde jste *vědět* získáte zpět pouze jeden řádek, je vhodnější použít trochu `QuerySingle`.
> 
> Existuje několik metod, které provádějí konkrétní typy databázových operací. Můžete najít seznam metod databáze [Stručná referenční rozhraní API technologie ASP.NET Web Pages](../../api-reference/asp-net-web-pages-api-reference.md#Data).


## <a name="making-validation-for-the-id-more-robust"></a>Aby ověřování pro ID více robustní

Při prvním spuštění stránky ID video z řetězce dotazu, které získáte můžete přejít z databáze získat tento film. Je ověřeno, že ve skutečnosti došlo hodnotu k vyhledání, které jste provedli pomocí tohoto kódu:

[!code-csharp[Main](updating-data/samples/sample13.cs)]

Ujistěte se, že pokud se uživatel dostane do pomocí tohoto kódu *EditMovies* stránky bez první video ve výběru *filmy* stránky, na stránce zobrazí popisnou chybovou zprávu. (V opačném případě uživatelé uvidí chybu, která je pravděpodobně právě zmást.)

Toto ověření se však nepříliš robustní. Na stránce může být vyvoláno také s těmito chybami:

- Identifikátor není číslo. Například může vyvolat na stránce s adresou URL jako `http://localhost:nnnnn/EditMovie?id=abc`.
- ID je číslo, ale odkazuje na film, který neexistuje (třeba `http://localhost:nnnnn/EditMovie?id=100934`).

Pokud jste zvědaví, pokud chcete zobrazit chyby, které vzniknou z těchto adres URL, spusťte *filmy* stránky. Vyberte video chcete upravit a potom změňte adresu URL *EditMovie* stránky na adresu URL, která obsahuje abecední ID nebo ID neexistující videa.

Tak co byste měli dělat? Abyste měli jistotu, že není pouze ID předána na stránku, ale, že ID je celé číslo je první opravu. Změňte kód `!IsPost` testu, aby vypadala jako v tomto příkladu:

[!code-csharp[Main](updating-data/samples/sample14.cs)]

Na druhou podmínku, která jste přidali `IsEmpty` testu, propojené s `&&` (logický operátor a):

[!code-csharp[Main](updating-data/samples/sample15.cs)]

Může nezapomeňte z [Úvod do programování webových stránek ASP.NET](../introducing-razor-syntax-c.md) kurz, který metody, jako jsou `AsBool` `AsInt` převést na jiný datový typ řetězec znaků. `IsInt` – Metoda (a jiné, jako například `IsBool` a `IsDateTime`) jsou podobné. Však jsou jen pro testování, zda jste *můžete* převést řetězec bez samotnému provedení převodu. Takže tady v podstatě říkáte *Pokud hodnotu řetězce dotazu lze převést na celé...* .

Možný problém hledá videa, která neexistuje. Kód slouží k získání filmu vypadá takto:

[!code-csharp[Main](updating-data/samples/sample16.cs)]

Pokud předáte `movieId` hodnota, která se `QuerySingle` metodě, která neodpovídá skutečné film, nevrátí a příkazy, které následují (například `title=row.Title`) dojít k chybám.

Znovu se to snadno napravit. Pokud `db.QuerySingle` metoda nebyly vráceny žádné výsledky `row` proměnná bude mít hodnotu null. Proto můžete zkontrolovat, zda `row` proměnná má hodnotu null, než se pokusíte získat hodnoty z něj. Následující kód přidává `if` bloku kolem příkazy, které získávají hodnoty z celkového počtu `row` objektu:

[!code-csharp[Main](updating-data/samples/sample17.cs)]

Na stránce se tyto dvě další ověřovací testy, stane odrážky testování. Kompletní kód `!IsPost` větev teď vypadá jako v tomto příkladu:

[!code-csharp[Main](updating-data/samples/sample18.cs)]

Jsme Všimněte ještě jednou, že tato úloha je vhodné využít pro `else` bloku. Pokud nejsou testy úspěšné, `else` bloky nastavení chybové zprávy.

## <a name="adding-a-link-to-return-to-the-movies-page"></a>Přidání propojení se vrátíte na stránku filmy

Konečné a užitečné podrobností je můžete přidat odkaz zpět *filmy* stránky. V běžném toku událostí bude uživatele začíná už na *filmy* stránky a klikněte na tlačítko **upravit** odkaz. Který přináší do *EditMovie* stránku, kde můžete upravit video a klikněte na tlačítko. Po kód zpracovává změny, je přesměrován zpět *filmy* stránky.

Ale:

- Uživatel se mohli rozhodnot něco změnit.
- Uživatel může jste získali na tuto stránku bez nejdříve kliknutím **upravit** odkaz v *filmy* stránky.

V obou případech budete chtít umožňují snadno vrátit na hlavní výpis. Jde to snadno napravit &mdash; přidejte následující kód bezprostředně po zavření `</form>` značky v kódu:

[!code-html[Main](updating-data/samples/sample19.html)]

Tento kód používá stejnou syntaxi pro `<a>` element, který jste se seznámili jinde. Adresa URL obsahuje `~` rozumí "kořenový adresář webu."

## <a name="testing-the-movie-update-process"></a>Testování procesu aktualizace Movie

Nyní můžete otestovat. Spustit *filmy* stránce a klikněte na tlačítko **upravit** vedle videa. Když *EditMovie* se zobrazí stránka, provést změny filmů a klikněte na tlačítko **odeslat změny**. Jakmile se zobrazí výpis film, ujistěte se, že změny jsou zobrazeny.

Chcete-li mít jistotu, že funguje ověřování, klikněte na tlačítko **upravit** pro jiné video. Když se zobrazí *EditMovie* zrušte **žánr** pole (nebo **rok** pole nebo obojí) a odeslat provedené změny. Zobrazí se vám chyby dle očekávání:

![Upravit video stránky zobrazující chyby ověření](updating-data/_static/image4.png)

Klikněte na tlačítko **vrátit seznam film** odkaz opustit změny a vraťte se do *filmy* stránky.

## <a name="coming-up-next"></a>Chystá se další

V dalším kurzu zobrazí se vám odstranění záznamu video.

## <a name="complete-listing-for-movie-page-updated-with-edit-links"></a>Úplný seznam pro stránku Movie (aktualizován odkazů pro úpravy)

[!code-cshtml[Main](updating-data/samples/sample20.cshtml)]

<a id="Complete_Page_Listing_for_EditMovie"></a>
## <a name="complete-page-listing-for-edit-movie-page"></a>Úplný výpis stránky pro stránku Upravit video

[!code-cshtml[Main](updating-data/samples/sample21.cshtml)]

## <a name="additional-resources"></a>Další prostředky

- [Úvod k programování v prostředí ASP.NET pomocí syntaxe Razor](../../getting-started/introducing-razor-syntax-c.md)
- [Prohlášení aktualizace SQL](http://www.w3schools.com/sql/sql_update.asp) na webu W3Schools

> [!div class="step-by-step"]
> [Předchozí](entering-data.md)
> [další](deleting-data.md)
