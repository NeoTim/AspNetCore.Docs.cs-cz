---
uid: web-api/overview/web-api-routing-and-actions/create-a-rest-api-with-attribute-routing
title: Vytvořit rozhraní REST API se směrováním atributů v rozhraní ASP.NET Web API 2 | Dokumentace Microsoftu
author: MikeWasson
description: ''
ms.author: riande
ms.date: 06/26/2013
ms.assetid: 23fc77da-2725-4434-99a0-ff872d96336b
msc.legacyurl: /web-api/overview/web-api-routing-and-actions/create-a-rest-api-with-attribute-routing
msc.type: authoredcontent
ms.openlocfilehash: 18a44c280e6df1603837938d24d7d639d8c87cc2
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021414"
---
<a name="create-a-rest-api-with-attribute-routing-in-aspnet-web-api-2"></a>Vytvořit rozhraní REST API se směrováním atributů ve rozhraní ASP.NET Web API 2
====================
podle [Mike Wasson](https://github.com/MikeWasson)

Webové rozhraní API 2 podporuje nový typ směrování, nazývá *směrováním atributů*. Obecný přehled směrování atributů, naleznete v tématu [směrováním atributů ve webovém rozhraní API 2](attribute-routing-in-web-api-2.md). V tomto kurzu použijete směrováním atributů k vytvoření rozhraní REST API pro kolekce knihy. Rozhraní API bude podporovat tyto akce:

| Akce | Příklad identifikátoru URI |
| --- | --- |
| Získání seznamu všech knih. | / api/knihy |
| Získat knihu podle ID. | /API/Books/1 |
| Získání podrobností o knize. | /API/Books/1/details |
| Získání seznamu knih podle žánru. | /API/Books/fantasy |
| Získání seznamu sad knihy datu publikování. | /API/Books/Date/2013-02-16 /api/books/date/2013/02/16 (alternativní formuláře) |
| Získání seznamu sad knihy konkrétním autorem. | /API/authors/1/Books |

Všechny metody jsou jen pro čtení (požadavky HTTP GET).

Datová vrstva použijeme Entity Framework. Záznamy o knihách bude mít tahle pole:

- ID
- Název
- Genre
- Datum publikování
- Cena
- Popis
- AuthorID (cizí klíč do tabulky autoři)

Pro většinu požadavků, ale rozhraní API vrátí podmnožinu těchto dat (názvu, autora a rozšířením podle tematických). Chcete-li získat úplný záznam klienta požadavky `/api/books/{id}/details`.

## <a name="prerequisites"></a>Požadavky

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) Community, Professional nebo Enterprise edition.

## <a name="create-the-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

Začněte tím, že spustíte Visual Studio. Z **souboru** nabídce vyberte možnost **nový** a pak vyberte **projektu**.

Rozbalte **nainstalováno** > **Visual C#** kategorie. V části **Visual C#** vyberte **webové**. V seznamu šablon projektu vyberte **webová aplikace ASP.NET (.NET Framework)**. Pojmenujte projekt &quot;BooksAPI&quot;.

![](create-a-rest-api-with-attribute-routing/_static/image1.png)

V **nová webová aplikace ASP.NET** dialogového okna, vyberte **prázdný** šablony. V části "Přidat složky a základní odkazy pro" vyberte **webového rozhraní API** zaškrtávací políčko. Klikněte na tlačítko **OK**.

![](create-a-rest-api-with-attribute-routing/_static/image2.png)

Tím se vytvoří kostru projektu, který je nakonfigurovaný pro webové rozhraní API funkce.

### <a name="domain-models"></a>Doménových modelů

Dále přidejte třídy pro doménových modelů. V Průzkumníku řešení klikněte pravým tlačítkem na složku modely. Vyberte **přidat**a pak vyberte **třídy**. Název třídy `Author`.

![](create-a-rest-api-with-attribute-routing/_static/image3.png)

Nahraďte kód v Author.cs následujícími způsoby:

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample1.cs)]

Nyní přidejte další třídu s názvem `Book`.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample2.cs)]

### <a name="add-a-web-api-controller"></a>Přidat kontroler rozhraní Web API

V tomto kroku přidáme kontroler Web API, která používá Entity Framework jako datová vrstva.

Sestavte projekt stisknutím kombinace kláves CTRL+SHIFT+B. Entity Framework používá reflexi ke zjišťování vlastností modely, takže vyžaduje zkompilovaného sestavení k vytvoření schématu databáze.

V Průzkumníku řešení klikněte pravým tlačítkem myši na složku řadiče. Vyberte **přidat**a pak vyberte **řadič**.

![](create-a-rest-api-with-attribute-routing/_static/image4.png)

V **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **Kontroleru webového rozhraní API 2 s akcemi používající nástroj Entity Framework**.

[![](create-a-rest-api-with-attribute-routing/_static/image6.png)](create-a-rest-api-with-attribute-routing/_static/image5.png)

V **přidat kontroler** dialogovém okně pro **názvu Kontroleru**, zadejte &quot;BooksController&quot;. Vyberte &quot;použít asynchronní akce kontroleru&quot; zaškrtávací políčko. Pro **třída modelu**vyberte &quot;knihy&quot;. (Pokud se nezobrazí `Book` třídy uvedené v rozevíracím seznamu, ujistěte se, že jste sestavili projekt.) Klikněte na tlačítko "+".

![](create-a-rest-api-with-attribute-routing/_static/image7.png)

Klikněte na tlačítko **přidat** v **nový kontext dat** dialogového okna.

![](create-a-rest-api-with-attribute-routing/_static/image8.png)

Klikněte na tlačítko **přidat** v **přidat kontroler** dialogového okna. Základní kostry aplikace přidá třídu s názvem `BooksController` , který definuje kontroleru rozhraní API. Také přidá třídu s názvem `BooksAPIContext` ve složce modelů, která definuje kontext dat pro Entity Framework.

![](create-a-rest-api-with-attribute-routing/_static/image9.png)

### <a name="seed-the-database"></a>Přidání dat do databáze

V nabídce Nástroje vyberte **Správce balíčků NuGet**a pak vyberte **Konzola správce balíčků**.

V okně konzoly Správce balíčků zadejte následující příkaz:

[!code-powershell[Main](create-a-rest-api-with-attribute-routing/samples/sample3.ps1)]

Tento příkaz vytvoří složku migrace a přidá nový soubor kódu s názvem Configuration.cs. Otevřete tento soubor a přidejte následující kód, který `Configuration.Seed` metody.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample4.cs)]

V okně konzoly Správce balíčků zadejte následující příkazy.

[!code-powershell[Main](create-a-rest-api-with-attribute-routing/samples/sample5.ps1)]

Tyto příkazy vytvoří místní databáze a vyvolat metodu počáteční hodnoty k naplnění databáze.

![](create-a-rest-api-with-attribute-routing/_static/image10.png)

## <a name="add-dto-classes"></a>Přidat objekt DTO třídy

Pokud aplikaci nyní spustit a odeslat požadavek GET na /api/books/1 odpověď vypadá podobně jako následující. (Po přidání odsazení pro lepší čitelnost).

[!code-json[Main](create-a-rest-api-with-attribute-routing/samples/sample6.json)]

Místo toho chci, aby tento požadavek vrátí podmnožinu polí. Také jsem má vrátí jméno autora, spíše než ID autora. K tomu upravíme metody kontroleru se vraťte *objekt pro přenos dat* (DTO) namísto EF modelu. Objekt DTO je objekt, který je určena pouze pro přenášejí data.

V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte **přidat** | **novou složku**. Název složky &quot;DTO&quot;. Přidejte třídu pojmenovanou `BookDto` ke složce DTO s následující definice:

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample7.cs)]

Přidejte další třídu s názvem `BookDetailDto`.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample8.cs)]

Dále, aktualizujte `BooksController` třídy pro vracení `BookDto` instancí. Použijeme [Queryable.Select](https://msdn.microsoft.com/library/system.linq.queryable.select.aspx) metoda do projektu `Book` instance na `BookDto` instancí. Tady je aktualizovaný kód pro třídu kontroleru.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample9.cs)]

> [!NOTE]
> Odstranil jsem `PutBook`, `PostBook`, a `DeleteBook` metody, protože nejsou potřeba pro účely tohoto kurzu.


Teď Pokud spuštění aplikace a požádat o /api/books/1 tělo odpovědi by měl vypadat takto:

[!code-json[Main](create-a-rest-api-with-attribute-routing/samples/sample10.json)]

## <a name="add-route-attributes"></a>Přidat atributy trasy

V dalším kroku se nám budete převést řadiče na použití směrování atributů. Nejprve přidejte **RoutePrefix** atributů kontroleru. Tento atribut definuje počáteční identifikátor URI segmenty pro všechny metody v tomto kontroleru.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample11.cs?highlight=1)]

Pak přidejte **[trasy]** atributy na akce kontroleru, následujícím způsobem:

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample12.cs?highlight=1,7)]

Šablona trasy pro jednotlivé metody kontroleru je předpona plus řetězec zadaný v **trasy** atribut. Pro `GetBook` parametrizovaný řetězec obsahuje metodu, šablona trasy &quot;{id: int}&quot;, který odpovídá, pokud segment identifikátoru URI obsahuje celočíselnou hodnotu.

| Metoda | Šablona trasy | Příklad identifikátoru URI |
| --- | --- | --- |
| `GetBooks` | "api/knihy" | `http://localhost/api/books` |
| `GetBook` | "api/books / {id: int}" | `http://localhost/api/books/5` |

## <a name="get-book-details"></a>Získat podrobnosti adresáře

Pokud chcete získat podrobnosti adresáře, klient odešle požadavek GET na `/api/books/{id}/details`, kde *{id}* je ID knihy.

Přidejte následující metodu do `BooksController` třídy.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample13.cs)]

Pokud jste požádali o `/api/books/1/details`, odpověď vypadá takto:

[!code-json[Main](create-a-rest-api-with-attribute-routing/samples/sample14.json)]

## <a name="get-books-by-genre"></a>Získat knihy podle žánru

K získání seznamu knih v konkrétní genre, klient odešle požadavek GET na `/api/books/genre`, kde *žánr* je název žánr. (Například `/api/books/fantasy`.)

Přidejte následující metodu do `BooksController`.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample15.cs)]

Tady jsme definovat trasu, která obsahuje parametr {žánr} v šablona identifikátoru URI. Všimněte si, že se k rozlišení těchto dvou identifikátory URI a směrovat na různé metody webového rozhraní API:

`/api/books/1`

`/api/books/fantasy`

Důvodem je, `GetBook` metoda obsahuje omezení, že segment "id" musí být celočíselná hodnota:

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample16.cs?highlight=1)]

Pokud jste požádali o /api/books/fantasy, odpověď vypadá takto:

`[ { "Title": "Midnight Rain", "Author": "Ralls, Kim", "Genre": "Fantasy" }, { "Title": "Maeve Ascendant", "Author": "Corets, Eva", "Genre": "Fantasy" }, { "Title": "The Sundered Grail", "Author": "Corets, Eva", "Genre": "Fantasy" } ]`

## <a name="get-books-by-author"></a>Získat knihy autorem

Pokud chcete získat seznam webu knihy pro konkrétní Autor, klient odešle požadavek GET na `/api/authors/id/books`, kde *id* je ID autora.

Přidejte následující metodu do `BooksController`.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample17.cs)]

V tomto příkladu je zajímavé protože &quot;knihy&quot; je zacházeno podřízený prostředek &quot;autoři&quot;. Tento model je celkem běžné v rozhraní RESTful API.

Předpona trasy v přepsání tilda (~) v šabloně trasy **RoutePrefix** atribut.

## <a name="get-books-by-publication-date"></a>Získat knihy datum publikace

K získání seznamu knih podle data zveřejnění, klient odešle požadavek GET na `/api/books/date/yyyy-mm-dd`, kde *rrrr mm-dd* datum.

Tady je jeden způsob, jak to udělat:

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample18.cs)]

`{pubdate:datetime}` Parametr je omezená tak, aby odpovídaly **data a času** hodnotu. Tento postup funguje, ale je ve skutečnosti mnohem mírnější, než jsme chtěli. Například tyto identifikátory URI se taky shodovat trasy:

`/api/books/date/Thu, 01 May 2008`

`/api/books/date/2000-12-16T00:00:00`

Není nic špatného s povolením tyto identifikátory URI. Můžete však omezit trasy na konkrétní formát přidáním omezení regulární výraz pro šablonu trasy:

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample19.cs?highlight=1)]

Nyní pouze data ve formě &quot;rrrr mm-dd&quot; bude odpovídat. Všimněte si, že nebudeme používat k ověření, že jsme získali skutečné datum regulární výraz. Při pokusí převést segment identifikátoru URI do webového rozhraní API, která je zpracována **data a času** instance. Neplatné datum jako je například "2012-47-99 se nepodařilo převést, a klient se zobrazí chyba 404.

Může také podporovat lomítko oddělovačem (`/api/books/date/yyyy/mm/dd`) tak, že přidáte další **[trasy]** atribut s jiný regulární výraz.

[!code-html[Main](create-a-rest-api-with-attribute-routing/samples/sample20.html)]

Je zde podrobností drobným ale důležité. Druhá šablona trasy obsahuje zástupný znak (\*) na začátku parametru {pubdate}:

[!code-json[Main](create-a-rest-api-with-attribute-routing/samples/sample21.json)]

Modul směrování říká, že {pubdate} by měl odpovídat zbývající části identifikátoru URI. Ve výchozím nastavení odpovídá parametru šablony jeden segment identifikátoru URI. V tomto případě chceme {pubdate} span několik segmentů identifikátor URI:

`/api/books/date/2013/06/17`

## <a name="controller-code"></a>Kódu kontroleru

Tady je kompletní kód pro třídu BooksController.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample22.cs)]

## <a name="summary"></a>Souhrn

Směrování atributů nabízí další ovládací prvek a větší flexibilitu při navrhování identifikátory URI pro vaše rozhraní API.
