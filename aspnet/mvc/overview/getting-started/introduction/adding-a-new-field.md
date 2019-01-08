---
uid: mvc/overview/getting-started/introduction/adding-a-new-field
title: Přidání nového pole | Dokumentace Microsoftu
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 10/17/2013
ms.assetid: 4085de68-d243-4378-8a64-86236ea8d2da
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-a-new-field
msc.type: authoredcontent
ms.openlocfilehash: 950ae17ebd6b0f15520c2a4e9372703f5374dfbe
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098685"
---
<a name="adding-a-new-field"></a>Přidání nového pole
====================
Podle [Rick Anderson]((https://twitter.com/RickAndMSFT))

[!INCLUDE [Tutorial Note](sample/code-location.md)]

V této části použijete migrace Entity Framework Code First k migraci některých změn do tříd modelu, tak použití této změny do databáze.

Ve výchozím nastavení při použití platformy Entity Framework Code First automaticky vytvořit databázi, jako jste to udělali dříve v tomto kurzu Code First přidá tabulku do databáze pro sledování, zda je synchronizovaný s tříd modelu, které byly vygenerovány z schéma databáze. Pokud nejsou synchronizované, Entity Framework vyvolá chybu. Díky tomu je snadněji sledovat problémy při vývoji, který může jinak pouze pro vás (pomocí skrytého chyby) v době běhu.

## <a name="setting-up-code-first-migrations-for-model-changes"></a>Nastavení migrace Code First pro změny modelu

Přejděte do Průzkumníka řešení. Klikněte pravým tlačítkem na *Movies.mdf* a vyberte možnost **odstranit** odebrání databáze filmů. Pokud se nezobrazí *Movies.mdf* souboru, klikněte na **zobrazit všechny soubory** ikonu znázorněném na červené ohraničení.

![](adding-a-new-field/_static/image1.png)

Vytvoření aplikace, abyste měli jistotu, že nejsou žádné chyby.

Z **nástroje** nabídky, klikněte na tlačítko **Správce balíčků NuGet** a potom **Konzola správce balíčků**.

![Přidat balíček Man](adding-a-new-field/_static/image2.png)

V **Konzola správce balíčků** v okna `PM>` řádku zadejte

Povolení migrace - ContextTypeName MvcMovie.Models.MovieDBContext

![](adding-a-new-field/_static/image3.png)

**Povolení migrace** vytvoří příkaz (popsaný výš) *Configuration.cs* souboru v novém *migrace* složky.

![](adding-a-new-field/_static/image4.png)

Visual Studio otevře *Configuration.cs* souboru. Nahradit `Seed` metodu *Configuration.cs* souboru následujícím kódem:

[!code-csharp[Main](adding-a-new-field/samples/sample1.cs)]

Najeďte myší červená vlnovka čára pod `Movie` a klikněte na tlačítko `Show Potential Fixes` a potom klikněte na tlačítko **pomocí** **MvcMovie.Models;**

![](adding-a-new-field/_static/image5.png)

Tím se přidají následující příkaz using:

[!code-csharp[Main](adding-a-new-field/samples/sample2.cs)]

> [!NOTE]
> 
> Kód volá první migrace `Seed` za každou migraci (tedy volání **aktualizace databáze** v konzole Správce balíčků), a tato metoda aktualizuje řádky, které již byl vložen a vloží je, pokud jsou dosud neexistují.
> 
> [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) v následujícím kódu metoda provádí operaci "upsert":
> 
> [!code-csharp[Main](adding-a-new-field/samples/sample3.cs)]
> 
> Vzhledem k tomu, [počáteční hodnoty](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) metoda pracuje s každou migraci, data, nelze vložit stejně, protože se snažíte přidat řádky už budou tam po první migrace, který vytvoří databázi. "[Upsert](http://en.wikipedia.org/wiki/Upsert)" operaci brání chyby, které by mohlo dojít, pokud se pokusíte vložit řádek, který již existuje, ale přepíše všechny změny dat, který může mít provedené při testování aplikace. Pomocí testovacích dat v některých tabulkách nechcete provést: v některých případech při změně dat při testování chcete své změny zůstat po aktualizacích databáze. V takovém případě ji chcete vložit podmíněné operace: Vložit řádek jenom v případě, že ještě neexistuje.   
> 
> První parametr předána [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) metody určuje vlastnost, která má použít ke kontrole, jestli řádek již existuje. Pro data o filmech test, který tím `Title` vlastnost lze použít pro tento účel, protože každý název v seznamu je jedinečný:
> 
> [!code-csharp[Main](adding-a-new-field/samples/sample4.cs)]
> 
> Tento kód předpokládá, že jsou jedinečné názvy. Pokud chcete ručně přidat duplicitní název, zobrazí se následující výjimka při příštím provedení migrace.   
> 
>  *Posloupnost obsahuje více než jeden prvek.*  
> 
> Další informace o [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) metodu, najdete v článku [postará metodou AddOrUpdate 4.3 EF](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/)...


**Stisknutím klávesy CTRL-SHIFT-B a sestavte projekt.** (Následujících kroků selže, pokud není v tomto okamžiku sestavení.)

Dalším krokem je vytvoření `DbMigration` třídu pro počáteční migraci. Migrace vytvoří novou databázi, proto můžete odstranit *movie.mdf* souboru v předchozím kroku.

V **Konzola správce balíčků** okno, zadejte příkaz `add-migration Initial` vytvořit počáteční migraci. Název "Počáteční" libovolný a slouží k pojmenování souboru migrace vytvořili.

![](adding-a-new-field/_static/image6.png)

Migrace Code First vytvoří další soubor třídy v *migrace* složky (s názvem *{DateStamp}\_Initial.cs* ), a tato třída obsahuje kód, který vytvoří schéma databáze. Název souboru migraci je pevně předem s časovým razítkem Nápověda k řazení. Zkontrolujte *{DateStamp}\_Initial.cs* souboru, obsahuje pokyny pro vytvoření `Movies` tabulky pro video DB. Při aktualizaci databáze v pokynech níže to *{DateStamp}\_Initial.cs* souboru spustí a vytvořit schéma databáze. Pak bude **počáteční hodnoty** metoda spustí k naplnění databáze se testovací data.

V **Konzola správce balíčků**, zadejte příkaz `update-database` vytvořte databázi a spusťte `Seed` metody.

![](adding-a-new-field/_static/image7.png)

Pokud dojde k chybě, která určuje již existuje a nedá se vytvořit tabulku, je možné, že jste spustili aplikaci po odstranění databáze a předtím, než jste spustili `update-database`. V takovém případě odstranit *Movies.mdf* soubor znovu a zkuste to znovu `update-database` příkazu. Pokud stále dojde k chybě, odstraňte složku migrace a obsah, pak spusťte v pokynech v horní části této stránky (to je odstranit *Movies.mdf* souborů pak přejděte k povolení migrace). Pokud stále dojde k chybě, otevřete Průzkumník objektů systému SQL Server a odeberte databázi ze seznamu.

Spusťte aplikaci a přejděte */Movies* adresy URL. Počáteční data se zobrazí.

![](adding-a-new-field/_static/image8.png)

## <a name="adding-a-rating-property-to-the-movie-model"></a>Přidání vlastnosti do hodnocení filmů modelu

Začněte přidáním nového `Rating` vlastnost ke stávající `Movie` třídy. Otevřít *Models\Movie.cs* a přidejte `Rating` vlastnost podobný následujícímu:

[!code-csharp[Main](adding-a-new-field/samples/sample5.cs)]

Kompletní `Movie` třídy teď vypadá jako v následujícím kódu:

[!code-csharp[Main](adding-a-new-field/samples/sample6.cs?highlight=12)]

Vytvořte aplikaci (Ctrl + Shift + B).

Protože jsme přidali nové pole do `Movie` třídy, je také potřeba aktualizovat vazbu *seznamu povolených* tak tuto novou vlastnost budou zahrnuty. Aktualizace `bind` atributu `Create` a `Edit` metody akce, které chcete zahrnout `Rating` vlastnost:

[!code-csharp[Main](adding-a-new-field/samples/sample7.cs?highlight=1)]

Je také potřeba aktualizovat zobrazit šablony, aby bylo možné zobrazit, vytvořit a upravit novou `Rating` vlastností v okně prohlížeče.

Otevřít *\Views\Movies\Index.cshtml* a přidejte `<th>Rating</th>` hned za záhlaví sloupce **cena** sloupec. Pak přidejte `<td>` sloupec blíží ke konci šablonu k vykreslení `@item.Rating` hodnotu. Níže je co aktualizované *Index.cshtml* zobrazit šablonu vypadá jako:

[!code-cshtml[Main](adding-a-new-field/samples/sample8.cshtml?highlight=31-33,52-54)]

Dále otevřete *\Views\Movies\Create.cshtml* a přidejte `Rating` pole s následující highlighed značky. Tím zkopírujete textové pole tak, aby hodnocení můžete zadat, když se vytvoří nová videa.

[!code-cshtml[Main](adding-a-new-field/samples/sample9.cshtml?highlight=9-15)]

Teď když jste aktualizovali kód aplikace pro podporu nového `Rating` vlastnost.

Spusťte aplikaci a přejděte */Movies* adresy URL. Když toto provedete, ale zobrazí se vám některé z následujících chyb:

![](adding-a-new-field/_static/image9.png)  
  
Model zálohování kontextu 'MovieDBContext' byl změněn, protože byla vytvořena databáze. Zvažte použití migrace Code First k aktualizaci databáze (https://go.microsoft.com/fwlink/?LinkId=238269).

![](adding-a-new-field/_static/image10.png)

Tato chyba se zobrazuje, protože aktualizovaný `Movie` třídy modelu v aplikaci je nyní liší od schématu `Movie` tabulky existující databáze. (Neexistuje žádný `Rating` sloupec v tabulce databáze.)


Řešení chyby několika způsoby:

1. Máte rozhraní Entity Framework automaticky vyřadit a znovu vytvořit databázi založené na nové schéma třídy modelu. Tento přístup je velmi vhodné v rané fázi vývojového cyklu při provádění testu databáze; s aktivním vývojem umožňuje rychlý rozvoj schématu modelu a databáze společně. Nevýhodou, je však dojít ke ztrátě existujících dat v databázi, tak můžete *není* chcete použít tento postup u provozní databáze! Použití inicializátoru automaticky naplnit databázi daty testu je často produktivní způsob, jak vyvíjet aplikace. Další informace o databázi inicializátory Entity Framework naleznete v tématu [kurz ASP.NET MVC a Entity Framework](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).
2. Explicitně upravte schéma stávající databázi tak, aby odpovídalo tříd modelu. Výhodou tohoto přístupu je, že zachováte vaše data. Můžete tuto změnu provést buď ručně, nebo tak, že vytvoříte databázi změnit skript.
3. Pomocí migrace Code First aktualizovat schéma databáze.


Pro účely tohoto kurzu používáme migrace Code First.

Aktualizujte metodu počáteční hodnoty tak, aby poskytuje hodnoty pro nový sloupec. Otevřete soubor Migrations\Configuration.cs a přidat hodnocení pole do každé video.

[!code-csharp[Main](adding-a-new-field/samples/sample10.cs?highlight=6)]

Sestavte řešení a pak otevřete **Konzola správce balíčků** okna a zadejte následující příkaz:

`add-migration Rating`

`add-migration` Příkaz říká rozhraní framework migrace sloužící ke zkoumání aktuální model filmů s aktuální schéma databáze filmů a vytvářet kód potřebné k migraci databáze na nový model. Název *hodnocení* je volitelný a slouží k pojmenování souboru migrace. Je vhodné použít smysluplný název kroku migrace.

Po dokončení tohoto příkazu, Visual Studio otevře soubor třídy, která definuje nový `DbMigration` odvozené třídy a `Up` metoda se zobrazí kód, který vytvoří nový sloupec.

[!code-csharp[Main](adding-a-new-field/samples/sample11.cs)]

Sestavte řešení a pak zadejte `update-database` v příkaz **Konzola správce balíčků** okna.

Následující obrázek znázorňuje výstup v **Konzola správce balíčků** okno (datum razítko předřazení *hodnocení* se bude lišit.)

![](adding-a-new-field/_static/image11.png)

Znovu spusťte aplikaci a přejděte na adresu URL /Movies. Uvidíte nové pole hodnocení.

![](adding-a-new-field/_static/image12.png)

Klikněte na tlačítko **vytvořit nový** odkaz na přidání nového videa. Všimněte si, že můžete přidat hodnocení.

![7_CreateRioII](adding-a-new-field/_static/image13.png)

Klikněte na možnost **Vytvořit**. Tento nový film, včetně hodnocení, zobrazí se nově ve výpisu:

![7_ourNewMovie_SM](adding-a-new-field/_static/image14.png)

Teď, když projekt používá migrace, nebudete muset odpojení databáze při přidání nového pole nebo jinak aktualizovat schéma. V další části jsme budete provádět další změny schématu a použití migrace k aktualizaci databáze.

Měli byste také přidat `Rating` pole k zobrazení šablony upravit, podrobnosti a Delete.

Můžete například zadat příkaz "aktualizace databáze" v **Konzola správce balíčků** znovu okno a žádný kód migrace by spustit, protože schéma odpovídá schématu modelu. Však bude fungovat s "aktualizace databází" `Seed` metoda znovu, a pokud jste změnili počáteční hodnoty dat, změny budou ztraceny, protože `Seed` metoda upsertuje data. Další informace o `Seed` metoda v Tom Dykstra oblíbených [kurz ASP.NET MVC a Entity Framework](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).

V této části jste viděli, jak můžete upravit objekty modelu a udržovat synchronizované s změny databáze. Také jste se naučili způsob, jak naplnit nově vytvořenou databázi s ukázkovými daty, takže si můžete vyzkoušet scénáře. To je jenom rychlý úvod k Code First, naleznete v tématu [vytváření datového modelu Entity Framework pro aplikaci ASP.NET MVC](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) úplný kurz týkající se předmětu. V dalším kroku Podívejme se na jak můžete přidat bohatší logiku ověřování na třídy modelu a povolit některé obchodní pravidla, která budou vynucena.

> [!div class="step-by-step"]
> [Předchozí](adding-search.md)
> [další](adding-validation.md)
