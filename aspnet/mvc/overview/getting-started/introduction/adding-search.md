---
uid: mvc/overview/getting-started/introduction/adding-search
title: Hledání | Dokumentace Microsoftu
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 01/17/2019
ms.assetid: df001954-18bf-4550-b03d-43911a0ea186
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-search
msc.type: authoredcontent
ms.openlocfilehash: ada125c917656f3a83524ff39e53b4cfc041a497
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248378"
---
<a name="search"></a>Hledat
====================

[!INCLUDE [Tutorial Note](sample/code-location.md)]

## <a name="adding-a-search-method-and-search-view"></a>Přidání vyhledávací metody a zobrazení vyhledávání

V této části přidáte možnost vyhledávání `Index` metody akce, která umožňuje hledat filmy podle žánru nebo názvu.

## <a name="prerequisites"></a>Požadavky

Tak, aby odpovídala této části snímky obrazovky, musíte spustit aplikaci (F5) a přidejte následující videa do databáze.

| Název | Datum vydání | Genre | Cena |
| ----- | ------------ | ----- | ----- |
| Ghostbusters | 6/8/1984 | Komedie | 6.99 |
| Ghostbusters II | 6/16/1989 | Komedie | 6.99 |
| Globálním opice | 3/27/1986 | Akce | 5.99 |


## <a name="updating-the-index-form"></a>Aktualizuje se Index formuláře

Začněte tím, že aktualizace `Index` metody akce ke stávající `MoviesController` třídy. Zde je kód:

[!code-csharp[Main](adding-search/samples/sample1.cs?highlight=1,6-9)]

První řádek `Index` metoda vytvoří následující [LINQ](https://msdn.microsoft.com/library/bb397926.aspx) dotaz pro výběr videa:

[!code-csharp[Main](adding-search/samples/sample2.cs)]

Dotaz je definován v tomto okamžiku, ale nebyl dosud spuštěn na databázi.

Pokud `searchString` parametr obsahuje řetězec, dotaz filmy je upravit tak, aby filtrování na základě hodnoty hledaný řetězec, pomocí následujícího kódu:

[!code-csharp[Main](adding-search/samples/sample3.cs)]

`s => s.Title` Je výše uvedený kód [výraz Lambda](https://msdn.microsoft.com/library/bb397687.aspx). Výrazy lambda se používají v založených na volání metody [LINQ](https://msdn.microsoft.com/library/bb397926.aspx) dotazuje jako argumenty pro standardní metody operátoru dotazu, jako [kde](https://msdn.microsoft.com/library/system.linq.enumerable.where.aspx) metodu používanou ve výše uvedeném kódu. Dotazy LINQ nejsou provedeny, když jsou definovány, nebo při jejich změně voláním metody `Where` nebo `OrderBy`. Místo toho provádění dotazu je odloženo, což znamená, že vyhodnocení výrazu je odloženo jeho očekávané hodnoty ve skutečnosti procházen nebo [ `ToList` ](https://msdn.microsoft.com/library/bb342261.aspx) metoda je volána. V `Search` vzorku a spuštění dotazu v *Index.cshtml* zobrazení. Další informace o odložený dotaz, naleznete v tématu [provádění dotazu](https://msdn.microsoft.com/library/bb738633.aspx).

> [!NOTE]
> [Obsahuje](https://msdn.microsoft.com/library/bb155125.aspx) metodu spustíte v databázi, není kód jazyka c# výše. V databázi [obsahuje](https://msdn.microsoft.com/library/bb155125.aspx) mapuje [SQL LIKE](https://msdn.microsoft.com/library/ms179859.aspx), což je malá a velká písmena.

Nyní můžete aktualizovat `Index` zobrazení, které se uživateli zobrazí formulář.

Spusťte aplikaci a přejděte do */filmy/Index*. Připojte řetězec dotazu jako `?searchString=ghost` na adresu URL. Zobrazují se filtrované filmy.

![SearchQryStr](adding-search/_static/image1.png)

Pokud se změní podpis `Index` metoda může mít parametr s názvem `id`, `id` parametr budou odpovídat `{id}` zástupný symbol pro výchozí trasy sady v *aplikace\_Start\ RouteConfig.cs* souboru.

[!code-json[Main](adding-search/samples/sample4.json)]

Původní `Index` metoda vypadá takto:

[!code-csharp[Main](adding-search/samples/sample5.cs)]

Upravené `Index` metoda vypadá takto:

[!code-csharp[Main](adding-search/samples/sample6.cs?highlight=1,3)]

Nyní lze předat název vyhledávání jako data trasy (segment adresy URL) místo jako hodnotu řetězce dotazu.

![](adding-search/_static/image2.png)

Ale nemůžete očekávat, že uživatelům změnit adresu URL pokaždé, když chtějí hledat videa. Takže teď přidáte uživatelského rozhraní, aby to pomohl ostatním filtrovat videa. Pokud jste změnili podpis `Index` metody testování jak předat parametru ID vázané na trasy, změňte ho tak, aby vaše `Index` metoda použije parametr řetězce s názvem `searchString`:

[!code-csharp[Main](adding-search/samples/sample7.cs)]

Otevřít *Views\Movies\Index.cshtml* souboru a jenom po `@Html.ActionLink("Create New", "Create")`, přidejte značku formuláře, jejichž přehled najdete níže:

[!code-cshtml[Main](adding-search/samples/sample8.cshtml?highlight=12-15)]

`Html.BeginForm` Pomocné rutiny vytvoří počáteční `<form>` značky. `Html.BeginForm` Pomocné rutiny způsobí, že formulář ke zveřejnění na sebe sama, jakmile uživatel formulář odešle kliknutím **filtr** tlačítko.

Visual Studio 2013 obsahuje nice zlepšování při zobrazení a úpravy souborů zobrazení. Při spuštění aplikace se zobrazit soubor otevřít, Visual Studio 2013 vyvolá metoda akce kontroleru správné zobrazení.

![](adding-search/_static/image3.png)

Když je zobrazení indexu otevřete v sadě Visual Studio (jak je znázorněno na obrázku výše), klepněte na pev.cenu F5 nebo F5 spusťte aplikaci a pak zkuste vyhledat videa.

![](adding-search/_static/image4.png)

Neexistuje žádná `HttpPost` přetížení `Index` metody. Není nutné, protože metoda není změněn stav aplikace, stačí filtrování dat.

Můžete přidat následující `HttpPost Index` metody. V takovém případě by odpovídala původce volání akce `HttpPost Index` metody a `HttpPost Index` spustili byste metodu, jak je znázorněno na následujícím obrázku.

[!code-csharp[Main](adding-search/samples/sample9.cs)]

![SearchPostGhost](adding-search/_static/image5.png)

Ale i v případě, že přidáte to `HttpPost` verzi `Index` metoda, existuje omezení v tom, jak to vše implementován. Představte si, že chcete konkrétní hledání (záložky) nebo chcete poslat odkaz s přáteli, mohou kliknout, chcete-li zobrazit stejné filtrovaný seznam videa. Všimněte si, že adresa URL pro odeslání požadavku HTTP POST je stejný jako adresu URL pro požadavek na získání (localhost:xxxxx/filmy/Index) – není k dispozici žádné informace o vyhledávání v adrese URL samotného. Pravé teď vyhledávací řetězec informace jsou odeslány na server jako hodnotu pole formuláře. To znamená, že nelze zachytit tyto informace vyhledávání (záložky) nebo odeslat přátel v adrese URL.

Řešením je použití přetížení `BeginForm` , která určuje, že požadavek POST by měla přidat informace o hledání na adresu URL a, že by měl směrovat na `HttpGet` verzi `Index` metody. Nahraďte existující konstruktor bez parametrů `BeginForm` metoda následujícím kódem:

[!code-cshtml[Main](adding-search/samples/sample10.cshtml)]

![BeginFormPost_SM](adding-search/_static/image6.png)

Nyní když odešlete vyhledávání, adresa URL obsahuje hledaný řetězec dotazu. Hledání budou také moct `HttpGet Index` metodě akce, i když máte `HttpPost Index` metody.

![IndexWithGetURL](adding-search/_static/image7.png)

## <a name="adding-search-by-genre"></a>Přidání vyhledávání podle žánru

Pokud jste přidali `HttpPost` verzi `Index` metoda, odstraňte ji.

V dalším kroku přidáte funkci, která umožňují uživatelům vyhledat filmy podle žánru. Nahradit `Index` metodu s následujícím kódem:

[!code-csharp[Main](adding-search/samples/sample11.cs)]

Tato verze `Index` metoda přijímá další parametr, a to `movieGenre`. Vytvoření první několika řádků kódu `List` objekt pro uložení žánry video z databáze.

Následující kód je dotaz LINQ, který načte všechny žánry z databáze.

[!code-csharp[Main](adding-search/samples/sample12.cs)]

Tento kód použije `AddRange` metoda Obecné `List` kolekce pro přidání různých žánry do seznamu. (Bez `Distinct` modifikátor, byly přidány duplicitní žánry – například byly přidány komedie dvakrát v naší ukázce). Pak uloží seznam žánry v kódu `ViewBag.MovieGenre` objektu. Ukládání kategorie dat (takové film žánry) jako [SelectList](https://msdn.microsoft.cus/library/system.web.mvc.selectlist(v=vs.108).aspx) objekt `ViewBag`, pak je obvyklý postup pro aplikace MVC se přístup k datům kategorie v rozevíracím seznamu.

Následující kód ukazuje, jak zkontrolovat `movieGenre` parametru. Pokud není prázdná, omezí kód dál filmy dotaz omezit vybrané videa k zadaným rozšířením podle tematických.

[!code-csharp[Main](adding-search/samples/sample13.cs)]

Jak bylo uvedeno dříve, dotaz se nespouští na databázi, dokud není procházen seznamu video (který se stane v zobrazení po `Index` metoda akce vrací).

## <a name="adding-markup-to-the-index-view-to-support-search-by-genre"></a>Přidání značek do zobrazení Index pro podporu vyhledávání podle žánru

Přidat `Html.DropDownList` pomocná rutina pro *Views\Movies\Index.cshtml* souboru, těsně před `TextBox` pomocné rutiny. Dokončený kód je zobrazena níže:

[!code-cshtml[Main](adding-search/samples/sample14.cshtml?highlight=11)]

V následujícím kódu:

[!code-cshtml[Main](adding-search/samples/sample15.cshtml)]

Poskytuje klíč pro parametr "MovieGenre" `DropDownList` pomocná rutina pro vyhledání `IEnumerable<SelectListItem>` v `ViewBag`. `ViewBag` Používala v metodě akce:

[!code-csharp[Main](adding-search/samples/sample16.cs?highlight=10)]

Parametr "All" poskytuje popisku. Pokud tato volba si prohlédnout v prohlížeči, uvidíte, že jeho atribut "value" je prázdný. Protože kontroleru pouze filtruje `if` řetězec není `null` nebo je prázdná, prázdná hodnota pro odesílání `movieGenre` ukazuje všechny žánrů.

Můžete také nastavit možnost vybrat ve výchozím nastavení. Pokud byste chtěli "Komedie" jako výchozí možnost, chcete změnit kód v Kontroleru takto:

[!code-cshtml[Main](adding-search/samples/sample17.cshtml)]

Spusťte aplikaci a přejděte do */filmy/Index*. Zkuste hledat podle žánru, název filmu a obě kritéria.

![](adding-search/_static/image8.png)

V této části vytvoříte metody akce vyhledávání a zobrazení, které umožňují uživatelům vyhledat tak, že název filmu a rozšířením podle tematických. V další části, budete pohledu na tom, jak přidat vlastnost, která má `Movie` modelu a přidání inicializátoru, který se automaticky vytvoří testovací databáze.

> [!div class="step-by-step"]
> [Předchozí](examining-the-edit-methods-and-edit-view.md)
> [další](adding-a-new-field.md)
