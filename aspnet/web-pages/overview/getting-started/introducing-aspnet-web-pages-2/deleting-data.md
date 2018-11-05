---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/deleting-data
title: Představení rozhraní ASP.NET Web Pages – odstranění databázových dat | Dokumentace Microsoftu
author: Rick-Anderson
description: V tomto kurzu se dozvíte, jak odstranit položku jednotlivých databází. Předpokládá se, že jste dokončili řady prostřednictvím aktualizace dat z databáze v prostředí ASP.NET Pa...
ms.author: riande
ms.date: 01/02/2018
ms.assetid: 75b5c1cf-84bd-434f-8a86-85c568eb5b09
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/deleting-data
msc.type: authoredcontent
ms.openlocfilehash: b2ef8fcc8cc534bd31fea83bf0b085b85995f417
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020439"
---
<a name="introducing-aspnet-web-pages---deleting-database-data"></a>Úvod do webových stránek ASP.NET – odstranění databázových dat
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> V tomto kurzu se dozvíte, jak odstranit položku jednotlivých databází. Předpokládá, že jste dokončili řady prostřednictvím [aktualizaci dat z databáze v ASP.NET Web Pages](updating-data.md).
> 
> Co se dozvíte:
> 
> - Jak vybrat jednotlivý záznam v seznamu záznamů.
> - Postup odstranění jednoho záznamu z databáze.
> - Návod k ověření, že bylo na určité tlačítko kliknuto ve formuláři.
>   
> 
> Popsané funkce a technologie:
> 
> - `WebGrid` Pomocné rutiny.
> - SQL `Delete` příkazu.
> - `Database.Execute` Způsob spuštění SQL `Delete` příkazu.


## <a name="what-youll-build"></a>Co budete vytvářet

V předchozím kurzu jste zjistili, jak aktualizovat stávající záznam v databázi. Tento kurz je podobné, s tím rozdílem, že místo aktualizace záznamu, budete ho odstranit. Procesy, které jsou podobné, s tím rozdílem, že odstranění je jednodušší, takže v tomto kurzu bude krátký.

V *filmy* stránky, budete aktualizovat `WebGrid` pomocné rutiny, takže se zobrazí **odstranit** odkaz vedle každý film vyvíjený **upravit** propojení, které jste přidali dříve.

![Zobrazuje odkaz pro odstranění pro každý film stránky filmy](deleting-data/_static/image1.png)

Stejně jako u úpravy, když kliknete **odstranit** odkaz, tím přejdete na jinou stránku, kde je video informace již ve formě:

![Odstranit stránku film se video zobrazí](deleting-data/_static/image2.png)

Klikněte na tlačítko se trvale odstranit záznam.

## <a name="adding-a-delete-link-to-the-movie-listing"></a>Přidat odkaz pro odstranění výpis Movie

Začnete tak, že přidáte **odstranit** propojit `WebGrid` pomocné rutiny. Tento odkaz je podobný **upravit** propojení, které jste přidali v předchozím kurzu.

Otevřít *Movies.cshtml* souboru.

Změnit `WebGrid` kód v těle stránky tak, že přidáte sloupec. Tady je upravený kód:

[!code-html[Main](deleting-data/samples/sample1.html?highlight=9-10)]

Nový sloupec se tohle:

[!code-html[Main](deleting-data/samples/sample2.html)]

Tak, jak je nakonfigurovaný mřížky, **upravit** sloupec je úplně vlevo v mřížce a **odstranit** sloupec je úplně vpravo. (Není za čárka `Year` sloupec, v případě, že nebyl Všimněte si, že.) Není nic zvláštního kde tyto sloupce odkaz přejděte a by snadno mohlo vedle sebe. V tomto případě jsou samostatné znesnadnit Smíchat se jim.

![Označené filmy stránka s odkazy pro úpravy a podrobnosti zobrazíte, že nejsou vedle sebe](deleting-data/_static/image3.png)

Nový sloupec zobrazuje spojení (`<a>` element) jejichž text říká "Odstranit". Cíl odkazu (jeho `href` atribut) je kód, který se s podobným tuto adresu URL, takže v konečném důsledku přeloží `id` hodnotu pro každý film:

[!code-css[Main](deleting-data/samples/sample3.css)]

Tento odkaz se vyvolat stránku s názvem *DeleteMovie* a předejte jí ID videa, které jste vybrali.

V tomto kurzu nezkoumá podrobnosti o tom, jak je vytvořen tento odkaz, protože je téměř stejný jako **upravit** odkaz z předchozí kurz o službě ([aktualizaci dat z databáze v ASP.NET Web Pages](updating-data.md)).

## <a name="creating-the-delete-page"></a>Vytvoření stránky Delete

Nyní můžete vytvořit stránku, která budou cílem pro **odstranit** odkaz v mřížce.

> [!NOTE] 
> 
> **Důležité** technika nejprve výběru záznam odstranit a pak používá samostatné stránky a tlačítko potvrďte procesu je velmi důležité pro zabezpečení. Protože jste si přečetli v předchozích kurzech, což *všechny* druh změn na váš web by měl *vždy* udělat pomocí formuláře &mdash; použití operace HTTP POST. Pokud jste provedli možné změnit webu jenom klepnutím na odkaz (tj. pomocí operace GET), lidí může provádět jednoduché požadavky vašeho webu a odstraňovat data. Dokonce i vyhledávací web prohledávací modul, který je indexování webu může omylem odstraníte data, stačí získat prostřednictvím následujících odkazů.
> 
> Aplikace umožňuje uživatelům změnit záznam, máte k dispozici záznam uživatele pro úpravy přesto. Ale můžete mít tendenci Přeskočit tento krok pro odstranění záznamu. Tento krok, není ale přeskočit. (Je také užitečné, uživatelé si můžou zobrazit záznam a potvrďte, že se při odstraňování záznamu, která jsou určena.)
> 
> V následující sérii kurzů uvidíte, jak přidat funkce přihlášení, aby uživatel musel přihlášení před odstraněním záznamu.


Vytvoření stránky s názvem *DeleteMovie.cshtml* a nahradit, co je v souboru následujícím kódem:

[!code-cshtml[Main](deleting-data/samples/sample4.cshtml)]

Tento kód je jako *EditMovie* stránky, s výjimkou, že místo použití polí (`<input type="text">`), obsahuje kód `<span>` elementy. Není nutné nic tady upravit. Všechno, co musíte udělat, je zobrazit podrobnosti o filmu mohou uživatelé provádět jistotu, že se při odstraňování správné videa.

Značky již obsahuje odkaz, který umožňuje uživateli vrátit na stránku výpis video.

Stejně jako *EditMovie* stránky, ID vybraného videa je uložená ve skrytém poli. (Je předána do stránky na prvním místě jako hodnotu řetězce dotazu.) Je `Html.ValidationSummary` volání, které se zobrazí chyby ověření. V takovém případě chyba může být, že žádné ID film byla předána na stránku nebo že film ID je neplatné. Tato situace může nastat, pokud někdo běžel na této stránce bez první video ve výběru *filmy* stránky.

Popisek tlačítka je **video odstranit**, a jeho název atributu je nastavena na `buttonDelete`. `name` Atributu se použije v kódu k identifikaci tlačítko odeslání formuláře.

Budete muset psát kód 1) přečíst podrobnosti o filmu při prvním zobrazení stránky a (2) ve skutečnosti odstranit videa, když uživatel klikne na tlačítko.

## <a name="adding-code-to-read-a-single-movie"></a>Přidání kódu pro čtení jednoho videa

V horní části *DeleteMovie.cshtml* stránce, přidejte následující blok kódu:

[!code-cshtml[Main](deleting-data/samples/sample5.cshtml)]

Tento kód je stejné jako odpovídající kód v *EditMovie* stránky. Získá ID film mimo řetězec dotazu a používá ID číst záznam z databáze. Tento kód obsahuje ověřovací test (`IsInt()` a `row != null`) a ujistěte, že ID film předávaný do stránky je platný.

Mějte na paměti, že tento kód by měl spustit pouze při prvním spuštění stránky. Chcete znovu načíst video záznam z databáze, když uživatel klikne **video odstranit** tlačítko. Proto kód ke čtení, video se nachází uvnitř test, který říká `if(!IsPost)` &mdash; tedy *Pokud žádost není operace post (odeslání formuláře)*.

## <a name="adding-code-to-delete-the-selected-movie"></a>Přidání kódu k odstranění vybraného videa

Odstranit video, když uživatel klikne na tlačítko, přidejte následující kód pouze uvnitř složené závorce `@` blok:

[!code-csharp[Main](deleting-data/samples/sample6.cs)]

Tento kód je podobný kód pro aktualizaci existující záznam, ale jednodušší. Kód se spustí v podstatě SQL `Delete` příkazu.

 Stejně jako v *EditMovie* stránky, kód je v `if(IsPost)` bloku. Tentokrát `if()` je o něco složitější podmínky: 

[!code-csharp[Main](deleting-data/samples/sample7.cs)]

Existují dvě podmínky tady. První možností je, že na stránce se právě odesílá, jak už víte, před &mdash; `if(IsPost)`.

Druhá podmínka je `!Request["buttonDelete"].IsEmpty()`, což znamená, že žádost má objekt s názvem `buttonDelete`. Admittedly je to nepřímé způsob testování, které tlačítko odeslání formuláře. Pokud formulář obsahuje více tlačítka pro odeslání dat, zobrazí se pouze název tlačítka, které došlo ke kliknutí na v požadavku. Proto, logicky Pokud se zobrazí název konkrétního tlačítka v požadavku &mdash; nebo jak je uvedeno v kódu, pokud toto tlačítko není prázdný &mdash; , který je tlačítko, které odeslání formuláře.

`&&` Znamená, že operátor "a" (logický operátor a). Proto celý `if` podmínka je...

*Tento požadavek je příspěvek (ne první žádost)*  
  
 AND  
  
*`buttonDelete`Tlačítko* *byl tlačítko odeslání formuláře.*

Tento formulář (ve skutečnosti, na této stránce) obsahuje pouze jedno tlačítko, takže další test `buttonDelete` není technicky povinný. Stále Chystáte se provést operaci, která se trvale odeberou data. Proto má abyste měli jistotu, jako je to možné, že při provádění operace pouze v případě, že uživatel explicitně požaduje ho. Předpokládejme například, zvětšit později na této stránce a do ní přidat další tlačítka. Dokonce i pak, kód, který odstraní videa se spustí jenom v případě, `buttonDelete` došlo ke kliknutí na tlačítko.

Stejně jako *EditMovie* stránky, získejte ID z skryté pole a poté spusťte příkaz SQL. Syntaxe `Delete` příkaz je:

`DELETE FROM table WHERE ID = value`

Je důležité zahrnout `WHERE` klauzule a ID. Pokud vynecháte klauzule WHERE *se odstraní všechny záznamy v tabulce*. Protože jste viděli, předejte hodnotu ID příkazu SQL s použitím zástupného symbolu.

## <a name="testing-the-movie-delete-process"></a>Testování procesu odstranění Movie

Nyní můžete otestovat. Spustit *filmy* stránce a klikněte na tlačítko **odstranit** vedle videa. Když *DeleteMovie* stránky se zobrazí, klikněte na tlačítko **video odstranit**.

![Odstranit stránku film se zvýrazněným tlačítkem odstranit video](deleting-data/_static/image4.png)

Když kliknete na tlačítko, kód odstraní videa a vrátí na výpis video. Existuje vyhledejte odstraněné filmů a potvrďte, že byla odstraněna.

## <a name="coming-up-next"></a>Chystá se další

V dalším kurzu se dozvíte, jak poskytnout všechny stránky na webu běžné vzhledu a rozložení.

## <a name="complete-listing-for-movie-page-updated-with-delete-links"></a>Úplný seznam pro stránku Movie (aktualizován odstranění odkazů)

[!code-cshtml[Main](deleting-data/samples/sample8.cshtml)]

## <a name="complete-listing-for-deletemovie-page"></a>Úplný seznam DeleteMovie stránky

[!code-cshtml[Main](deleting-data/samples/sample9.cshtml)]

## <a name="additional-resources"></a>Další prostředky

- [Úvod k programování v prostředí ASP.NET pomocí syntaxe Razor](../introducing-razor-syntax-c.md)
- [Příkaz jazyka SQL odstranit](http://www.w3schools.com/sql/sql_delete.asp) na webu W3Schools

> [!div class="step-by-step"]
> [Předchozí](updating-data.md)
> [další](layouts.md)
