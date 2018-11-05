---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/entering-data
title: Představení rozhraní ASP.NET Web Pages – zadávání dat do databáze pomocí formulářů | Dokumentace Microsoftu
author: Rick-Anderson
description: V tomto kurzu se dozvíte, jak vytvořit formulář pro zadávání a zadejte data, která získáte z formuláře do databázové tabulky pomocí rozhraní ASP.NET Web Pages (...)
ms.author: riande
ms.date: 05/28/2015
ms.assetid: d37c93fc-25fd-4e94-8671-0d437beef206
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/entering-data
msc.type: authoredcontent
ms.openlocfilehash: e40d2962ccac56eaaf4812819aa42168e69295bc
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021726"
---
<a name="introducing-aspnet-web-pages---entering-database-data-by-using-forms"></a>Představení rozhraní ASP.NET Web Pages – zadávání dat do databáze pomocí formulářů
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> V tomto kurzu se dozvíte, jak vytvořit formulář položku a zadejte data, která získáte z formuláře do tabulky databáze při použití webových stránek ASP.NET (Razor). Předpokládá, že jste dokončili řady prostřednictvím [základy z formuláře HTML na webových stránkách ASP.NET](https://go.microsoft.com/fwlink/?LinkId=251581).
> 
> Co se dozvíte:
> 
> - Další informace o tom, jak zpracovat formulářích pro zadávání.
> - Jak přidat data (Vložit) v databázi.
> - Jak zajistit, aby uživatelé zadali požadovaná hodnota ve formě (jak ověřit vstup uživatele).
> - Jak zobrazit chyby ověření.
> - Jak přejít na jinou stránku na aktuální stránce.
>   
> 
> Popsané funkce a technologie:
> 
> - `Database.Execute` Metody.
> - SQL `Insert Into` – příkaz
> - `Validation` Pomocné rutiny.
> - `Response.Redirect` Metody.


## <a name="what-youll-build"></a>Co budete vytvářet

V tomto kurzu dříve, který vám ukázali, jak vytvořit databázi, jste zadali dat z databáze tak, že upravíte přímo ve službě WebMatrix, práce databázi **databáze** pracovního prostoru. Ve většině aplikací, který není praktický způsob, jak ukládat data do databáze, i když. Proto v tomto kurzu vytvoříte webové rozhraní, ve kterém jste nebo všem uživatelům zadávat data a uložit do databáze.

Vytvoříte stránky, kde můžete zadat nové filmy. Stránka bude obsahovat formulář položka, která má pole (polí), ve kterém můžete zadat název filmu, rozšířením podle tematických a rok. Stránka bude vypadat na této stránce:

!["Přidat video" stránku v prohlížeči](entering-data/_static/image1.png)

Textová pole bude HTML `<input>` prvky, které bude vypadat jako tento kód:

`<input type="text" name="genre" value="" />`

## <a name="creating-the-basic-entry-form"></a>Vytvoření základní vstupu formuláře

Vytvoření stránky s názvem *AddMovie.cshtml*.

Nahraďte, co je v souboru následujícím kódem. Přepsat vše, co; přidáte blok kódu v horní části za chvíli.

[!code-cshtml[Main](entering-data/samples/sample1.cshtml)]

Tento příklad ukazuje typické HTML pro vytvoření formuláře. Používá `<input>` elementy pro textová pole a tlačítka Odeslat. Popisky pro textová pole jsou vytvořeny pomocí standardních `<label>` elementy. `<fieldset>` a `<legend>` prvky umístěte nice rámečkem okolo položky formuláře.

Všimněte si, že na této stránce `<form>` prvek používá `post` hodnotu `method` atribut. V předchozím kurzu jste vytvořili formuláře, který se používá `get` metody. Bylo správné, protože i když se odešle formulář hodnoty na server, žádost nebyly provedeny žádné změny. Všechno, co kdyby byl načtení dat různými způsoby. Ale na této stránce můžete *bude* proveďte změny – se chystáte přidat nové záznamy v databázi. Proto měli používat tento formulář `post` metody. (Další informace o rozdílech mezi `GET` a `POST` operací, najdete v článku[GET, POST a HTTP příkaz bezpečnosti](https://go.microsoft.com/fwlink/?LinkId=251581#GET,_POST,_and_HTTP_Verb_Safety) boční panel v předchozím kurzu.)

Všimněte si, že každé pole má `name` – element (`title`, `genre`, `year`). Jak už jste viděli v předchozím kurzu, tyto názvy jsou důležité, protože tyto názvy musí mít, abyste se mohli uživatelovo zadání později. Můžete použít názvy. Je vhodné použijte smysluplné názvy, které vám pomůžou zapamatovat jaká data se kterou pracujete.

`value` Atribut jednotlivých `<input>` prvek obsahuje hodně kódu Razor (například `Request.Form["title"]`). Verzi této možností jste zjistili v předchozím kurzu, chcete-li zachovat hodnoty zadané do textového pole (pokud existuje) po odeslání formuláře.

## <a name="getting-the-form-values"></a>Načtení hodnot formuláře

V dalším kroku přidáte kód, který zpracovává formuláře. V přehledu budete postupujte takto:

1. Zkontrolujte, zda se odeslání stránky (bylo odesláno). Chcete kód spustit pouze v případě, že uživatelé kliknuli na tlačítko Ne už při prvním spuštění stránky.
2. Získá hodnoty, které uživatel zadat do textového pole. V takovém případě vzhledem k tomu, že pomocí formuláře `POST` sloveso, získání hodnot z formulářů `Request.Form` kolekce.
3. Vložení hodnoty jako nového záznamu v *filmy* databázové tabulky.

Na začátek souboru přidejte následující kód:

[!code-cshtml[Main](entering-data/samples/sample2.cshtml)]

Několik prvních řádků vytváření proměnných (`title`, `genre`, a `year`) pro uchování hodnoty z textových polí. Na řádku `if(IsPost)` zajišťuje, že proměnné se nastaví *pouze* když uživatelé kliknou **přidat video** tlačítko – to znamená, když formulář byl odeslán.

Jak už jste viděli v předchozí kurzu, získáte hodnotu textové pole vlastnosti autorefresh pomocí výrazu jako `Request.Form["name"]`, kde *název* je název `<input>` elementu.

Názvy proměnných (`title`, `genre`, a `year`) jsou libovolného. Názvy, které přiřadíte, jako jsou `<input>` prvky, můžete je zavolat jakkoli chcete. (Názvy proměnných nemusí odpovídat názvu atributy `<input>` prvky ve formuláři.) Ale stejně jako u `<input>` prvků, je vhodné použít názvy proměnných, které zahrnují data, která obsahují. Při psaní kódu, konzistentní názvy usnadňují si zapamatovat, jaká data se kterou pracujete.

## <a name="adding-data-to-the-database"></a>Přidání dat do databáze

V kódu je právě přidanou, pouze blokovat *uvnitř* pravou složenou závorku ( `}` ) z `if` blokovat (ne jenom uvnitř bloku kódu), přidejte následující kód:

[!code-csharp[Main](entering-data/samples/sample3.cs)]

Tento příklad je podobný kód, který jste použili v předchozím kurzu k načtení a zobrazení data. Řádek, který začíná `db =` otevře databáze, stejně jako předtím a na další řádek definuje příkaz jazyka SQL, znovu jako viděli dříve. Ale tentokrát ho definuje SQL `Insert Into` příkazu. Následující příklad ukazuje Obecná syntaxe nástroje `Insert Into` – příkaz:

`INSERT INTO table (column1, column2, column3, ...) VALUES (value1, value2, value3, ...)`

Jinými slovy zadejte v tabulce pro vložení do, pak sloupce, které chcete vložit do seznamu a potom zobrazí seznam hodnot pro vložení. (Poznamenali dříve SQL není velká a malá písmena, ale někteří lidé velké první písmeno klíčová slova, aby bylo snazší přečíst příkazu.)

Sloupce, které jste vložili do už jsou uvedeny v příkazu – `(Title, Genre, Year)`. Zajímavé části je, jak získat hodnoty z textových polí do `VALUES` část příkazu. Místo skutečných hodnot, uvidíte `@0`, `@1`, a `@2`, které jsou samozřejmě zástupné symboly. Při spuštění příkazu (na `db.Execute` řádek), můžete předat hodnoty, které jste získali z textových polí.

**Důležité!** Mějte na paměti, že jediný způsob, kdy by měl obsahovat data zadaná uživatelem v příkazu SQL online se použít zástupné znaky, jako je tady uvidíte (`VALUES(@0, @1, @2)`). Pokud zřetězit uživatelský vstup do příkazu SQL můžete otevřít sami injektáže SQL, jak je vysvětleno v [základy formulářů v ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=251581) (předchozí kurz o službě).

Stále uvnitř `if` blokovat, přidejte následující řádek po `db.Execute` řádku:

[!code-css[Main](entering-data/samples/sample4.css)]

Jakmile tento nový film byla vložena do databáze, tento přeskakování vám (přesměrování) *filmy* stránce, abyste si mohli zobrazit videa, kterou jste právě zadali. `~` Operátor znamená "kořenový adresář webu." ( `~` Operátor funguje pouze na stránkách ASP.NET, není ve formátu HTML obecně.)

Blok kompletní kód bude vypadat jako v tomto příkladu:

[!code-cshtml[Main](entering-data/samples/sample5.cshtml)]

## <a name="testing-the-insert-command-so-far"></a>Testování příkazu Insert (zatím)

Ještě neskončili, ale teď je vhodná doba k testování.

Ve stromovém zobrazení souborů v nástroji WebMatrix, klikněte pravým tlačítkem myši *AddMovie.cshtml* stránce a potom klikněte na tlačítko **spustit v prohlížeči**.

!["Přidat video" stránku v prohlížeči](entering-data/_static/image2.png)

(Pokud skončíte se na jinou stránku v prohlížeči, ujistěte se, že je adresa URL `http://localhost:nnnnn/AddMovie`), kde *nnnnn* je číslo portu, který používáte.)

Obdrželi jste chybovou stránku? Pokud ano, pečlivě si přečtěte a ujistěte se, že kód bude vypadat přesně co byl uveden výše.

Zadejte ve formátu filmu &mdash; použít například "Občany Kratochvílová", "Drama" a "1941". (Nebo cokoli jiného.) Pak klikněte na tlačítko **přidat video**.

Pokud všechno proběhne správně, budete přesměrováni na *filmy* stránky. Ujistěte se, že je uvedena nová videa.

![Stránka filmy zobrazující nově přidána movie](entering-data/_static/image3.png)

## <a name="validating-user-input"></a>Ověřování uživatelského vstupu

Přejděte zpět *AddMovie* stránce a potom ho spusťte znovu. Zadejte jiný film, ale tentokrát, zadejte pouze název &mdash; zadejte například "Přihlášení ' v the déšť". Pak klikněte na tlačítko **přidat video**.

Budete přesměrováni na *filmy* stránku znovu nezobrazovat. Můžete najít tento nový film, ale je neúplný.

![Stránka zobrazující nové video, které chybí některé hodnoty filmy](entering-data/_static/image4.png)

Při vytváření *filmy* tabulky, můžete explicitně ale nutné dodat, že žádné z polí může mít hodnotu null. Zde máte formuláře pro nové filmy a jste se tak rozhodli pole prázdné. To je chyba.

V takovém případě databáze nebyla ve skutečnosti vyvolat (nebo *throw*) k chybě. Jste nezadali jste žánr nebo rok, takže kód *AddMovie* stránky považován za tyto hodnoty takzvané *prázdné řetězce*. Když SQL `Insert Into` byl spuštěn příkaz, pole žánr a roku neměli užitečná data v nich, ale jejich nebyly hodnotu null.

Samozřejmě které nechcete umožní uživatelům zadat polovině prázdný film informace do databáze. Řešením je ověření vstupu uživatele. Na začátku ověření jednoduše zajistit, aby uživatel zadal hodnotu pro všechna pole (to znamená, že žádný z nich obsahuje prázdný řetězec).

> [!TIP]
> 
> **Řetězce Null a prázdné**
> 
> Při programování, je rozdíl mezi různé pojmy "žádná hodnota." Obecně platí, je hodnota *null* Pokud nikdy byla nastavena nebo inicializován žádným způsobem. Naproti tomu proměnná, která očekává, že znaková data (řetězce) může být nastaven na *prázdný řetězec*. V takovém případě hodnota není null. To je právě byla explicitně nastaveno na řetězec znaků, jehož délka je 0. Tyto dva příkazy Zobrazit rozdíl:
> 
> [!code-csharp[Main](entering-data/samples/sample6.cs)]
> 
> To je trochu složitější než, ale důležitý bod je, že `null` představuje druh neurčeném stavu.
> 
> A poté je důležité pochopit přesně Pokud hodnota je null a když se nachází pouze prázdný řetězec. V kódu *AddMovie* stránky, získáte hodnoty do textových polí pomocí `Request.Form["title"]` a tak dále. Při prvním spuštění stránky (před kliknutím na tlačítko), hodnota `Request.Form["title"]` má hodnotu null. Ale při odeslání formuláře, `Request.Form["title"]` získá hodnotu `title` textového pole. Není zřejmé, ale není null; prázdné textové pole v něm jenom má prázdný řetězec. Proto při spuštění kódu v reakci na panelu klikněte na tlačítko, `Request.Form["title"]` v sobě obsahuje prázdný řetězec.
> 
> Toto rozlišení je důležité Při vytváření *filmy* tabulky, můžete explicitně ale nutné dodat, že žádné z polí může mít hodnotu null. Ale zde máte formuláře pro nové filmy a jste se tak rozhodli pole prázdné. Očekáváte by přiměřeně databáze si stěžovat při pokusu o uložení nové filmy, které nebyly k dispozici hodnoty žánr nebo rok. Ale to je bod &mdash; i v případě, že tyto textová pole necháte prázdné, nejsou hodnoty null; jsou prázdné řetězce. Díky tomu budete moct uložit do databáze s těmito sloupci prázdný nové filmy &mdash; , ale není null! &mdash; hodnoty. Proto budete muset Ujistěte se, že uživatelé neodesílejte prázdný řetězec, což lze provést pomocí ověření vstupu uživatele.


### <a name="the-validation-helper"></a>Pomocná rutina pro ověření

Webové stránky ASP.NET obsahuje pomocný &mdash; `Validation` pomocné rutiny &mdash; můžete zajistit, aby uživatelé zadali data, která splňují vaše požadavky. `Validation` Pomocné rutiny je jedním z pomocných rutin, které je součástí do webových stránek ASP.NET, takže není nutné jej nainstalovat jako balíček pomocí NuGet, tak, jak jste nainstalovali Gravatar pomocné rutiny v předchozí kurzu.

Ověření vstupu uživatele, budete postupujte takto:

- Pomocí kódu můžete určit, že chcete, aby hodnoty v polích text na stránce.
- Vložte test do kódu tak, aby informace film se přidá do databáze pouze v případě, že je všechno správně ověří.
- Přidejte kód do kódu zobrazit chybové zprávy.

V bloku kódu v *AddMovie* stránce, doprava nahoru v horní části před deklarace proměnných, přidejte následující kód:

[!code-csharp[Main](entering-data/samples/sample7.cs)]

Volání `Validation.RequireField` s jednou registrací u každé pole (`<input>` element) místo vyžadují zadání vstupu. Můžete také přidat vlastní chybovou zprávu pro každé volání, jako je tady vidíte. (Budeme měnit zprávy pouze k zobrazení můžete umístit všechno, co vám vyhovuje existuje).

Pokud je nějaký problém, můžete chtít zabránit nových filmů nebude vložen do databáze. V `if(IsPost)` zablokuje, použít `&&` (logický operátor a) Chcete-li přidat další typ podmínek, který testuje `Validation.IsValid()`. Až budete mít, celý `if(IsPost)` bloku vypadá přibližně takto:

[!code-csharp[Main](entering-data/samples/sample8.cs)]

Pokud dojde k chybě ověření pomocí libovolné pole, které jste zaregistrovali pomocí `Validation` pomocné rutiny, `Validation.IsValid` metoda vrátí hodnotu false. A v takovém případě žádný kód v tomto bloku se spustí, tak žádné položky neplatný film bude vložena do databáze. A samozřejmě budete přesměrování na *filmy* stránky.

Kompletní kód bloku, včetně kód pro ověření, se teď vypadá jako v tomto příkladu:

[!code-cshtml[Main](entering-data/samples/sample9.cshtml?highlight=10)]

## <a name="displaying-validation-errors"></a>Zobrazení chyb ověřování

Posledním krokem je zobrazení chybové zprávy. Můžete zobrazit jednotlivé zprávy pro všechny chyby ověření, nebo můžete zobrazit souhrn, nebo obojí. Pro účely tohoto kurzu budete používat i tak, abyste viděli, jak to funguje.

Vedle každého `<input>` element, který jste ověřování, volání `Html.ValidationMessage` metoda a předejte mu název `<input>` element už Probíhá ověřování. Umístíte `Html.ValidationMessage` metoda doprava, kde chcete, zobrazí se chybová zpráva. Při spuštění stránky, `Html.ValidationMessage` metoda vykreslí `<span>` elementu, kde bude umístěno chybu ověření. (Pokud se nezobrazí žádná chyba, `<span>` prvek je vykreslovaný, ale neexistuje žádný text v něm.)

Změňte kód na stránce tak, že obsahují `Html.ValidationMessage` metodu pro každý ze tří `<input>` elementy na stránce, jako jsou v tomto příkladu:

[!code-cshtml[Main](entering-data/samples/sample10.cshtml?highlight=3,8,13)]

Jak funguje souhrn najdete také přidejte následující kód a kódu hned po `<h1>Add a Movie</h1>` elementu na stránce:

[!code-cshtml[Main](entering-data/samples/sample11.cshtml)]

Ve výchozím nastavení `Html.ValidationSummary` metoda zobrazí seznam všech ověřovacích zpráv ( `<ul>` element, který se nachází uvnitř `<div>` element). Stejně jako u `Html.ValidationMessage` metoda, je vždy vykreslí značku pro souhrn ověření, pokud nejsou žádné chyby, jsou vykreslovány žádné položky seznamu.

Souhrn může být alternativní způsob zobrazení ověřovacích zpráv místo pomocí `Html.ValidationMessage` metodu pro zobrazení jednotlivých chyb specifické pro pole. Nebo můžete použít souhrn a podrobnosti. Nebo můžete použít `Html.ValidationSummary` metodu pro zobrazení Obecná chyba a potom použijte jednotlivé `Html.ValidationMessage` volání zobrazíte podrobnosti.

Na stránce dokončení teď vypadá jako v tomto příkladu:

[!code-cshtml[Main](entering-data/samples/sample12.cshtml)]

Je to. Na stránce teď můžete otestovat přidáním videa, ale přitom jeden nebo více polí. Když použijete, se zobrazí následující chyba zobrazení:

![Přidat stránku film zobrazení chybové zprávy ověření](entering-data/_static/image5.png)

## <a name="styling-the-validation-error-messages"></a>Používání stylů pro chybové zprávy ověření

Uvidíte, že existují chybové zprávy, ale jejich není odlišit se opravdu velmi dobře. Snadný způsob, jak formátování chybové zprávy, ale není k dispozici.

K formátování jednotlivých chybové zprávy, která zobrazuje `Html.ValidationMessage`, vytvořte třídu šablony stylů CSS style s názvem `field-validation-error`. K definování vzhledu pro souhrn ověření, vytvořte třídu šablony stylů CSS style s názvem `validation-summary-errors`.

Chcete-li zjistit, jak tento postup funguje, přidejte `<style>` element v rámci `<head>` části stránky. Potom definujte třídy s názvem `field-validation-error` a `validation-summary-errors` , které obsahují následující pravidla:

[!code-cshtml[Main](entering-data/samples/sample13.cshtml?highlight=4-17)]

Obvykle by pravděpodobně vložit informace o stylu do samostatné *.css* souboru, ale pro zjednodušení můžete je umístit na stránce teď. (Dále v této sérii kurzů přesunete pravidel šablon stylů CSS do samostatné *.css* souboru.)

Pokud dojde k chybě ověřování `Html.ValidationMessage` metoda vykreslí `<span>` element, který zahrnuje `class="field-validation-error"`. Přidáte definici stylu pro danou třídu, můžete nakonfigurovat, bude zpráva vypadat podobně jako. Pokud nejsou chyby, `ValidationSummary` metoda podobně dynamicky vykreslí atribut `class="validation-summary-errors"`.

Spusťte znovu a záměrně vynechte několika polí. Chyby jsou teď zřetelnější. (Ve skutečnosti jste overdone, ale, který je prostě ukázat, co můžete dělat.)

![Přidat video stránky zobrazující chybám ověření, které mají ve stylu](entering-data/_static/image6.png)

## <a name="adding-a-link-to-the-movies-page"></a>Přidání odkazu na stránku filmy

Jeden posledním krokem je usnadňuje zobrazíte *AddMovie* stránku z původní výpis video.

Otevřít *filmy* stránku znovu nezobrazovat. Za uzavírací `</div>` značky, který následuje `WebGrid` pomocné rutiny, přidejte následující kód:

[!code-cshtml[Main](entering-data/samples/sample14.cshtml)]

Protože jste viděli dříve, interpretuje ASP.NET `~` operátor jako kořenový adresář webu. Není nutné použít `~` operátor; můžete použít značky `<a href="./AddMovie">Add a movie</a>` nebo nějakým způsobem definování cestu, která analyzuje HTML. Ale `~` operátor je dobrý obecný postup při vytváření odkazy pro stránky Razor, protože to ztěžuje webu flexibilnější – Pokud přesunete aktuální stránku do podsložky, odkaz bude stále umístěno *AddMovie* stránky. (Mějte na paměti, `~` operátor funguje jenom *.cshtml* stránky. ASP.NET se rozumí, ale není standardní HTML.)

Jakmile budete hotovi, spusťte *filmy* stránky. Tato stránka bude vypadat:

![Filmy stránku s odkazem na stránku "přidat filmy](entering-data/_static/image7.png)

Klikněte na tlačítko **přidat video** odkaz, abyste měli jistotu, že přejde na *AddMovie* stránky.

## <a name="coming-up-next"></a>Chystá se další

V dalším kurzu se dozvíte, jak umožnit uživatelům upravovat data, která je již v databázi.

## <a name="complete-listing-for-addmovie-page"></a>Úplný seznam AddMovie stránky

[!code-cshtml[Main](entering-data/samples/sample15.cshtml)]

## <a name="additional-resources"></a>Další prostředky

- [Úvod k programování v prostředí ASP.NET pomocí syntaxe Razor](https://go.microsoft.com/fwlink/?LinkID=202890)
- [Vložit do příkazu SQL](http://www.w3schools.com/sql/sql_insert.asp) na webu W3Schools
- [Ověřování uživatelského vstupu v rozhraní ASP.NET Web Pages lokality](https://go.microsoft.com/fwlink/?LinkId=253002). Další informace o práci s `Validation` pomocné rutiny.

> [!div class="step-by-step"]
> [Předchozí](form-basics.md)
> [další](updating-data.md)
