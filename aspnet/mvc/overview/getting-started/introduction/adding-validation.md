---
uid: mvc/overview/getting-started/introduction/adding-validation
title: Přidání ověřování | Dokumentace Microsoftu
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 01/06/2019
ms.assetid: 9f35ca15-e216-4db6-9ebf-24380b0f31b4
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-validation
msc.type: authoredcontent
ms.openlocfilehash: 6831259ce19c3747c179d6fc1b7e2095051a603b
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099023"
---
<a name="adding-validation"></a>Přidání ověření
====================
Podle [Rick Anderson]((https://twitter.com/RickAndMSFT))

[!INCLUDE [Tutorial Note](sample/code-location.md)]

V této části přidáte logiku ověřování k `Movie` model a zajistíte, že ověřovacích pravidel se vynucují kdykoli se uživatel pokusí o vytvoření nebo úprava videa pomocí aplikace.

## <a name="keeping-things-dry"></a>Udržování suchého věcí

Jedním ze základních principů návrhu ASP.NET MVC je [suchého](http://en.wikipedia.org/wiki/Don't_repeat_yourself) (&quot;není opakujte sami&quot;). ASP.NET MVC umožňuje zadat funkci nebo chování pouze jednou a potom jste ho všude, kde se projeví v aplikaci. To snižuje množství kódu, které potřebujete k zápisu a provede kód, který napíšete náchylné k chybám a snadněji udržovat méně chyb.

Podpora ověřování poskytované rozhraní ASP.NET MVC a Entity Framework Code First je skvělé příkladem zásada SUCHÝCH v akci. Ověřovací pravidla můžete zadat pomocí deklarace na jednom místě (ve třídě modelu) a pravidel se vynucují kdekoli v aplikaci.

Podívejme se na tom, jak můžete využít této podpory ověřování v aplikace movie.

## <a name="adding-validation-rules-to-the-movie-model"></a>Přidání pravidel ověřování do modelu Movie

Zobrazí za přibližně tak, že přidáte některé logiku ověřování k `Movie` třídy.

Otevřít *Movie.cs* souboru. Všimněte si, že [ `System.ComponentModel.DataAnnotations` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) obor názvů neobsahuje `System.Web`. DataAnnotations poskytuje integrovanou sadu atributů ověření, které můžete provést pomocí deklarace tříd nebo vlastností. (Také obsahuje formátování atributů, jako je [datový typ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) , pomoct při formátování a neposkytují žádné ověřování.)

Teď aktualizovat `Movie` třídy, které chcete využít výhod integrovaného [ `Required` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute.aspx), [ `StringLength` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx), [regulární výraz](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx), a [ `Range` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.rangeattribute.aspx) atributů ověření. Nahraďte `Movie` třídy následujícím kódem:

[!code-csharp[Main](adding-validation/samples/sample1.cs?highlight=5,13-15,18-19,22-23)]

[ `StringLength` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx) Atribut Nastaví maximální délku řetězce a nastaví toto omezení pro databáze, proto se změní schéma databáze. Klikněte pravým tlačítkem na **filmy** tabulku v **Průzkumníka serveru** a klikněte na tlačítko **Otevřít definici tabulky**:

![](adding-validation/_static/image1.png)

Na obrázku výše, zobrazí se všechna pole řetězců jsou nastaveny na [NVARCHAR (maximální velikost)](https://technet.microsoft.com/library/ms186939.aspx). Migraci budeme používat k aktualizaci schématu. Sestavte řešení a pak otevřete **Konzola správce balíčků** okna a zadejte následující příkazy:

[!code-console[Main](adding-validation/samples/sample2.cmd)]

Po dokončení tohoto příkazu, Visual Studio otevře soubor třídy, která definuje nový `DbMIgration` odvozené třídy se zadaným názvem (`DataAnnotations`) a `Up` metoda vidíte kód, který aktualizuje schéma omezení:

[!code-csharp[Main](adding-validation/samples/sample3.cs)]

`Genre` Pole je už s možnou hodnotou Null (to znamená, je nutné zadat hodnotu). `Rating` Pole má maximální délku 5 a `Title` má maximální délku 60. Minimální délka 3 na `Title` a rozsah na `Price` nevytvořil změny schématu.

Zkontrolujte schéma filmu:

![](adding-validation/_static/image2.png)

Pole řetězců se zobrazí nová omezení délky a `Genre` již proběhne jako s možnou hodnotou Null.

Ověření atributy určují chování, které chcete vynutit na, které se použijí pro vlastnosti modelu. `Required` a `MinimumLength` atributy znamená, že vlastnost musí mít hodnotu, ale nic zabraňuje uživateli v zadávání prázdnými znaky až splňovat toto ověření. [Regulární výraz](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx) atribut se používá k omezení znaků, může být vstupní. Ve výše uvedeném kódu `Genre` a `Rating` musí používat jen písmena (prázdné znaky, číslice a speciální znaky nejsou povoleny). [ `Range` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.rangeattribute.aspx) Atribut omezuje hodnotu do zadaného rozsahu. `StringLength` Atribut umožňuje nastavit maximální délka vlastnosti typu string a volitelně jeho minimální délka. Typy hodnot (například `decimal, int, float, DateTime`) jsou ze své podstaty povinné a nemusíte `Required` atribut.

Kód nejprve zajistí, že se vynucují ověřovacích pravidel, které jste zadali na třídu modelu, předtím, než se aplikace uloží změny do databáze. Například kód uvedený níže vyvolá [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception(v=vs.103).aspx) výjimka při `SaveChanges` metoda je volána, protože některé požadované `Movie` hodnoty vlastností nebyly nalezeny:

[!code-csharp[Main](adding-validation/samples/sample4.cs)]

Výše uvedený kód vyvolala následující výjimku:

*Ověření se nezdařilo pro jednu nebo více entit. Viz vlastnost 'EntityValidationErrors' Další podrobnosti.*

S ověřovací pravidla automaticky vynucuje sada .NET Framework pomáhá vytvářet vaše aplikace odolnější. Také zajistí, že nelze zapomenete něco ověření a neúmyslně nechat chybná data do databáze.

## <a name="validation-error-ui-in-aspnet-mvc"></a>Chyba ověření uživatelského rozhraní v architektuře ASP.NET MVC

Spusťte aplikaci a přejděte */Movies* adresy URL.

Klikněte na tlačítko **vytvořit nový** odkaz na přidání nového videa. Vyplňte formulář pomocí některé neplatné hodnoty. Jakmile jQuery ověřování na straně klienta zjistí chyby, zobrazí chybovou zprávu.

![8_validationErrors](adding-validation/_static/image3.png)

> [!NOTE]
> pro podporu ověřování jQuery pro neanglická národní prostředí, které používají čárkou (",") pro desetinné čárky, je nutné zahrnout NuGet globalizace, jak je popsáno výše v tomto kurzu.


Všimněte si, jak formulář automaticky použila červené ohraničení zvýrazněte textová pole, které obsahují neplatná data a je vyzářeno chybovou zprávu ověření odpovídající vedle každé z nich. Chyby se vynucují straně klienta (pomocí jazyků JavaScript a jQuery) a na straně serveru (v případě má zakázaný JavaScript).

Skutečné výhodou je, že nemusíte změnit jediný řádek kódu v `MoviesController` třídy nebo *Create.cshtml* zobrazení, chcete-li povolit toto ověření uživatelského rozhraní. Kontroler a zobrazení, které jste vytvořili dříve v tomto kurzu automaticky vybere nahoru ověřovací pravidla, které jste zadali pomocí atributů ověření na vlastnosti `Movie` třída modelu. Test ověření pomocí `Edit` je použít metody akce a stejné ověřování.

Data formuláře neodešle na server, dokud nedojde k žádným chybám ověření na straně klienta. Můžete to ověřit tak, že vložíte přerušení metodu Post protokolu HTTP s použitím [nástroj fiddler](http://fiddler2.com/fiddler2/), nebo IE [vývojářské nástroje F12 pomáhají](https://msdn.microsoft.com/ie/aa740478).

## <a name="how-validation-occurs-in-the-create-view-and-create-action-method"></a>Jak probíhá ověřování vytvořením zobrazit a vytvořit metody akce

Může vás zajímat, jak byl vygenerován ověření uživatelského rozhraní bez jakýchkoli aktualizací kódu v kontroleru nebo zobrazení. Následující výpis ukazuje, co `Create` metody v `MovieController` vypadají třídy. Jsou to neliší od jak jste je vytvořili dříve v tomto kurzu.

[!code-csharp[Main](adding-validation/samples/sample5.cs)]

První (HTTP GET) `Create` zobrazí úvodní formulář vytvořit metody akce. Druhý (`[HttpPost]`) verze zpracovává odeslaného formuláře. Druhá `Create` – metoda ( `HttpPost` verze) kontroluje `ModelState.IsValid` zjistíte, zda má video všechny chyby ověření. Získání této vlastnosti vyhodnotí všechny atributy ověření, které se použily k objektu. Pokud objekt má chyby ověření, `Create` metoda znovu zobrazí formulář. Pokud nejsou žádné chyby, metoda tento nový film uloží v databázi. V našem příkladu film **formuláři není odeslána na server, pokud nejsou zjištěny na straně klienta; chyby ověřování druhého** `Create` **nikdy volána metoda**. Pokud zakážete jazyka JavaScript v prohlížeči, ověřování na straně klienta je zakázána a HTTP POST `Create` metoda získá `ModelState.IsValid` ke kontrole, jestli má tento film všechny chyby ověření.

Lze nastavit bod přerušení v `HttpPost Create` metoda a ověřit se nikdy nevolá metodu, ověřování na straně klienta nebude odeslat data formuláře, když se zjistí chyby ověření. Pokud zakážete jazyka JavaScript v prohlížeči a pak odesláním formuláře s chybami, se setkají bodu přerušení. Vy i přesto úplného ověření bez jazyka JavaScript. Následující obrázek ukazuje, jak zakázat jazyka JavaScript v aplikaci Internet Explorer.

![](adding-validation/_static/image5.png)

![](adding-validation/_static/image6.png)

Následující obrázek ukazuje, jak zakázat jazyka JavaScript v prohlížeči FireFox.

![](adding-validation/_static/image7.png)

Následující obrázek ukazuje, jak zakázat jazyka JavaScript v prohlížeči Chrome.

![](adding-validation/_static/image8.png)

Níže je uvedená *Create.cshtml* zobrazit šablonu, která automaticky dříve v tomto kurzu. Používá se pomocí metody akce, které jsou uvedené výše obě počáteční formulář pro zobrazení a znovu ho zobrazit v případě chyby.

[!code-cshtml[Main](adding-validation/samples/sample6.cshtml?highlight=16-17)]

Všimněte si, jak tento kód použije `Html.EditorFor` pomocné rutiny pro výstup `<input>` – element pro každé `Movie` vlastnost. Vedle tohoto pomocníka je volání `Html.ValidationMessageFor` Pomocná metoda. Tyto dvě metody helper pracovat s modelu objektu, který je předán adaptérem k zobrazení (v tomto případě `Movie` objekt). Zobrazují se automaticky pro ověření atributy určené v modelu a zobrazí chybové zprávy podle potřeby.

Co je hodně příjemné o tento přístup je, že žádná z kontroleru ani `Create` zobrazit šablonu nic ví o skutečné ověřovacích pravidel se vynucují nebo specifické chybové zprávy zobrazeny. Ověřovací pravidla a chybové řetězce se zadávají pouze v `Movie` třídy. Tyto stejné ověřovací pravidla se automaticky využije na `Edit` zobrazení a jakékoli další zobrazení šablony můžete vytvořit, upravit model.

Pokud chcete později změnit logiku ověřování, můžete k tomu v přesně jedno místo přidávání atributů ověření do modelu (v tomto příkladu `movie` třídy). Není nutné se starat o různé části aplikace, s jakým způsobem se vynucují pravidla nekonzistentní – veškerou logiku ověřování definované na jednom místě, který se použít kdekoli. To zajišťuje velmi čistým kódem a umožňuje snadno vyvíjet a udržovat. A to znamená, že je budete mít plně dodržením *suchého* zásadě.

## <a name="using-datatype-attributes"></a>Pomocí atributů datový typ

Otevřít *Movie.cs* souboru a zkoumat `Movie` třídy. [ `System.ComponentModel.DataAnnotations` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) Obor názvů obsahuje atributy formátování kromě integrovaná sada atributů ověření. Už jsme použili [ `DataType` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) hodnota výčtu datum vydání a na pole price. Následující kód ukazuje `ReleaseDate` a `Price` vlastnosti příslušnou [ `DataType` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) atribut.

[!code-csharp[Main](adding-validation/samples/sample7.cs)]

[Datový typ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atributy pouze poskytnout nápovědu pro modul zobrazení pro zobrazení dat (a zadat atributy například `<a>` pro adresy URL a `<a href="mailto:EmailAddress.com">` e-mailu. Můžete použít [regulární výraz](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx) atribut pro ověření formátu data. [Datový typ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atribut se používá k určení datový typ, který je specifičtější než vnitřní typ databáze, jsou ***není*** atributů ověření. V tomto případě chceme jenom udržovat přehled o datum, nejsou data a času. [Datový typ výčtu](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) poskytuje pro mnoho typů dat, například *datum, čas, telefonní číslo, Měna, EmailAddress* a provádění dalších akcí. `DataType` Atribut můžete také povolit aplikace a zajistit tak automaticky specifické pro typ funkce. Například `mailto:` propojení lze vytvořit pro [DataType.EmailAddress](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx), a selektor date lze zadat pro [DataType.Date](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) v prohlížečích podporujících [HTML5](http://html5.org/). [Datový typ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atributy vysílá HTML 5 [data -](http://ejohn.org/blog/html-5-data-attributes/) (vyslovováno *data dash*) atributy, které umožní pochopit prohlížeče HTML 5. [Datový typ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atributy se neposkytuje žádné ověřování.

`DataType.Date` neurčuje formátu, který se zobrazí datum. Ve výchozím nastavení, zobrazí se pole data podle výchozí formát založený na serveru [CultureInfo](https://msdn.microsoft.com/library/vstudio/system.globalization.cultureinfo(v=vs.110).aspx).

`DisplayFormat` Atribut se používá s ohledem na formát data:


[!code-csharp[Main](adding-validation/samples/sample8.cs)]


`ApplyFormatInEditMode` Nastavení určuje, že zadané formátování také bude použito při hodnota se zobrazí v textovém poli pro úpravu. (Pokud nechcete pro některá pole – například pro hodnoty měny, nemusí chcete symbol měny v textovém poli pro úpravu.)

Můžete použít [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) atribut samotný, ale to je obecně vhodné použít [datový typ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) také atribut. `DataType` Atribut přenáší *sémantiku* dat jako rozdíl od vykreslování na obrazovce a nabízí následující výhody, které vám s `DisplayFormat`:

- Funkce HTML5 (pro příklad, který znázorňuje ovládacího prvku kalendář, symbol měny odpovídající národní prostředí, odkazy na e-mailu, atd.) můžete povolit v prohlížeči.
- Ve výchozím prohlížeči bude vykreslovat data ve správném formátu, na základě vašich [národní prostředí](https://msdn.microsoft.com/library/vstudio/wyzd2bce.aspx).
- [Datový typ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atribut můžete povolit MVC zvolit šablonu pravé pole k poskytnutí dat ( [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) pokud používá sama používá šablony řetězce). Další informace najdete v tématu Brada Wilsona [šablony ASP.NET MVC 2](http://bradwilson.typepad.com/blog/2009/10/aspnet-mvc-2-templates-part-1-introduction.html). (I když napsané pro MVC 2, tento článek stále platí pro aktuální verzi technologie ASP.NET MVC.)

Pokud používáte `DataType` atribut pomocí pole pro datum, budete muset zadat `DisplayFormat` atribut také aby se správně vykresluje pole v prohlížečích Chrome. Další informace najdete v tématu [toto vlákno na StackOverflow](http://stackoverflow.com/questions/12633471/mvc4-datatype-date-editorfor-wont-display-date-value-in-chrome-fine-in-ie).

> [!NOTE]
> k ověřování jQuery nefunguje s [rozsah](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.rangeattribute.aspx) atribut a [data a času](https://msdn.microsoft.com/library/system.datetime.aspx). Například následující kód se vždy zobrazí chyba ověření na straně klienta, i v případě, že datum je v zadaném rozsahu:
> 
> [!code-csharp[Main](adding-validation/samples/sample9.cs)]
> 
> Je potřeba zakázat ověřování jQuery data použít [rozsah](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.rangeattribute.aspx) atributem [data a času](https://msdn.microsoft.com/library/system.datetime.aspx). Není obvykle vhodné pro kompilaci pevného data ve vašich modelů použít [rozsah](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.rangeattribute.aspx) atribut a [data a času](https://msdn.microsoft.com/library/system.datetime.aspx) se nedoporučuje.


Následující kód ukazuje kombinace atributů na jednom řádku:

[!code-csharp[Main](adding-validation/samples/sample10.cs?highlight=4,6,10,12)]

V další části série, přidáme zkontrolujte, zda aplikace a vylepšování některé automaticky generované `Details` a `Delete` metody.

> [!div class="step-by-step"]
> [Předchozí](adding-a-new-field.md)
> [další](examining-the-details-and-delete-methods.md)
