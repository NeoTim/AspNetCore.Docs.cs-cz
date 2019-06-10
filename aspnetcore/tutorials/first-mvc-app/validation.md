---
title: Přidání ověřování do aplikace ASP.NET Core MVC
author: rick-anderson
description: Postup přidání ověřování do aplikace ASP.NET Core.
ms.author: riande
ms.date: 04/13/2017
uid: tutorials/first-mvc-app/validation
ms.openlocfilehash: 6c59d0188f67872c7dd5599967551d7d390bfdcf
ms.sourcegitcommit: e7e04a45195d4e0527af6f7cf1807defb56dc3c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2019
ms.locfileid: "65875047"
---
# <a name="add-validation-to-an-aspnet-core-mvc-app"></a>Přidání ověřování do aplikace ASP.NET Core MVC

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části:

* Přidání logiku ověřování k `Movie` modelu.
* Zajistíte tím, že ověřovacích pravidel se vynucují kdykoli uživatel vytvoří nebo upraví videa.

## <a name="keeping-things-dry"></a>Udržování věci zkušební

Jedním z principů návrhu MVC je [suchého](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("není opakujte sami"). ASP.NET Core MVC umožňuje zadat funkci nebo chování pouze jednou a pak jste ji všude, kde se projeví v aplikaci. To snižuje množství kódu, které potřebujete k zápisu a provede kód, který napíšete méně chyby náchylné k chybám, usnadňuje testování a jednodušší správu.

Podpora ověřování poskytované MVC a Entity Framework Core Code First je dobrým příkladem zásada SUCHÝCH v akci. Ověřovací pravidla můžete zadat pomocí deklarace na jednom místě (ve třídě modelu) a pravidel se vynucují kdekoli v aplikaci.

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

## <a name="validation-error-ui"></a>Chyba ověření uživatelského rozhraní

Spusťte aplikaci a přejděte na filmy kontroleru.

Klepněte **vytvořit nový** odkaz na přidání nového videa. Vyplňte formulář pomocí některé neplatné hodnoty. Jakmile jQuery ověřování na straně klienta zjistí chyby, zobrazí chybovou zprávu.

![Video zobrazit formulář s více chyby ověření na straně klienta jQuery](~/tutorials/first-mvc-app/validation/_static/val.png)

[!INCLUDE[](~/includes/currency.md)]

Všimněte si, jak formulář automaticky vykreslí příslušné ověřovací chybovou zprávu do každého pole, který obsahuje neplatnou hodnotu. Chyby se vynucují straně klienta (pomocí jazyků JavaScript a jQuery) a na straně serveru (v případě má zakázaný JavaScript).

Významné výhodou je, že nemusíte změnit jediný řádek kódu v `MoviesController` třídy nebo *Create.cshtml* zobrazení, chcete-li povolit toto ověření uživatelského rozhraní. Kontroler a zobrazení, které jste vytvořili dříve v tomto kurzu automaticky vybere nahoru ověřovací pravidla, které jste zadali pomocí atributů ověření na vlastnosti `Movie` třída modelu. Test ověření pomocí `Edit` je použít metody akce a stejné ověřování.

Data formuláře neodešle na server, dokud nedojde k žádným chybám ověření na straně klienta. Můžete to ověřit tak, že vložíte přerušení `HTTP Post` metodu, pomocí [nástroj Fiddler](http://www.telerik.com/fiddler) , nebo [nástroje pro vývojáře F12](/microsoft-edge/devtools-guide).

## <a name="how-validation-works"></a>Jak funguje ověřování

Může vás zajímat, jak byl vygenerován ověření uživatelského rozhraní bez jakýchkoli aktualizací kódu v kontroleru nebo zobrazení. Následující kód ukazuje dva `Create` metody.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

První (HTTP GET) `Create` zobrazí úvodní formulář vytvořit metody akce. Druhý (`[HttpPost]`) verze zpracovává odeslaného formuláře. Druhá `Create` – metoda ( `[HttpPost]` verze) volání `ModelState.IsValid` ke kontrole, jestli má tento film všechny chyby ověření. Voláním této metody vyhodnotí všechny atributy ověření, které se použily k objektu. Pokud objekt má chyby ověření, `Create` metoda znovu zobrazí formulář. Pokud nejsou žádné chyby, metoda tento nový film uloží v databázi. V našem příkladu film formuláři není odeslána na server po ověření chyby zjištěné na straně klienta; Druhá `Create` metoda se nikdy nevolá, když jsou chyby ověření na straně klienta. Pokud zakážete jazyka JavaScript v prohlížeči, je zakázáno ověřování na straně klienta a budete moci otestovat HTTP POST `Create` metoda `ModelState.IsValid` detekce všech chyb ověření.

Lze nastavit bod přerušení v `[HttpPost] Create` metoda a ověřit se nikdy nevolá metodu, ověřování na straně klienta nebude odeslat data formuláře, když se zjistí chyby ověření. Pokud zakážete jazyka JavaScript v prohlížeči a pak odesláním formuláře s chybami, se setkají bodu přerušení. Vy i přesto úplného ověření bez jazyka JavaScript. 

Následující obrázek ukazuje, jak zakázat jazyka JavaScript v prohlížeči FireFox.

![Firefox: Na kartě obsah z možností zrušte zaškrtnutí políčka Povolit Javascript.](~/tutorials/first-mvc-app/validation/_static/ff.png)

Následující obrázek ukazuje, jak zakázat jazyka JavaScript v prohlížeči Chrome.

![Google Chrome: V části Nastavení obsahu Javascript neumožňují vybrat libovolnou lokalitu spouštět JavaScript.](~/tutorials/first-mvc-app/validation/_static/chrome.png)

Po zakázání JavaScript odeslat neplatná data a projít ladicí program.

![Při ladění na příspěvek neplatných dat, technologie Intellisense v ModelState.IsValid ukazuje, že je hodnota false.](~/tutorials/first-mvc-app/validation/_static/ms.png)

Část *Create.cshtml* zobrazení šablony se zobrazí v následujícím kódu:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/CreateRatingBrevity.html)]

Předchozí kód používá metody akce k počáteční formulář pro zobrazení a znovu ji v případě chyby.

[Pomocné rutiny značky vstup](xref:mvc/views/working-with-forms) používá [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) atributy a vytváří atributy HTML, které jsou potřeba pro architekturu jQuery ověření na straně klienta. [Pomocné rutiny značky ověření](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zobrazí chyby ověření. Zobrazit [ověření](xref:mvc/models/validation) Další informace.

Co je hodně příjemné o tento přístup je, že žádná z kontroleru ani `Create` zobrazit šablonu nic ví o skutečné ověřovacích pravidel se vynucují nebo specifické chybové zprávy zobrazeny. Ověřovací pravidla a chybové řetězce se zadávají pouze v `Movie` třídy. Tyto stejné ověřovací pravidla se automaticky využije na `Edit` zobrazení a jakékoli další zobrazení šablony můžete vytvořit, upravit model.

Pokud potřebujete změnit logiku ověřování, lze provést přesně pohromadě přidáním atributů ověření do modelu (v tomto příkladu `Movie` třídy). Není nutné se starat o různé části aplikace, s jakým způsobem se vynucují pravidla nekonzistentní – veškerou logiku ověřování definované na jednom místě, který se použít kdekoli. To zajišťuje velmi čistým kódem a umožňuje snadno vyvíjet a udržovat. A to znamená, že je budete mít plně dodržením zásada SUCHÝCH.

## <a name="using-datatype-attributes"></a>Pomocí atributů datový typ

Otevřít *Movie.cs* souboru a zkoumat `Movie` třídy. `System.ComponentModel.DataAnnotations` Obor názvů obsahuje atributy formátování kromě integrovaná sada atributů ověření. Už jsme použili `DataType` hodnota výčtu datum vydání a na pole price. Následující kód ukazuje `ReleaseDate` a `Price` vlastnosti příslušnou `DataType` atribut.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

`DataType` Atributy pouze poskytnout nápovědu pro modul zobrazení pro zobrazení dat (a poskytuje elementy nebo atributy, jako `<a>` pro adresy URL a `<a href="mailto:EmailAddress.com">` e-mailu. Můžete použít `RegularExpression` atribut pro ověření formátu data. `DataType` Atribut se používá k určení datový typ, který je specifičtější než vnitřní typ databáze, nejsou atributů ověření. V tomto případě chceme jenom udržovat přehled o datum bez času. `DataType` Výčet poskytuje pro mnoho typů dat, jako je například datum, čas, telefonní číslo, Měna, EmailAddress a další. `DataType` Atribut můžete také povolit aplikace a zajistit tak automaticky specifické pro typ funkce. Například `mailto:` propojení lze vytvořit pro `DataType.EmailAddress`, a selektor date lze zadat pro `DataType.Date` v prohlížečích podporujících HTML5. `DataType` Generování atributů HTML 5 `data-` (čteno data dash) atributy, které umožní pochopit prohlížeče HTML 5. `DataType` Proveďte atributy **není** žádné ověřování.

`DataType.Date` neurčuje formátu, který se zobrazí datum. Ve výchozím nastavení, zobrazí se pole data podle výchozí formát založený na serveru `CultureInfo`.

`DisplayFormat` Atribut se používá s ohledem na formát data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode` Nastavení určuje, že formátování také bude použito při hodnota se zobrazí v textovém poli pro úpravu. (Pokud nechcete pro některá pole – například pro hodnoty měny, pravděpodobně nechcete symbol měny v textovém poli pro úpravu.)

Můžete použít `DisplayFormat` atribut samotný, ale to je obecně vhodné použít `DataType` atribut. `DataType` Atribut přenáší sémantiku dat na rozdíl od vykreslování na obrazovce a nabízí následující výhody, které vám s DisplayFormat:

* Prohlížeči můžete povolit funkce HTML5 (pro příklad, který znázorňuje ovládacího prvku kalendář, symbol měny odpovídající národní prostředí, odkazy na e-mailu, atd.)

* Ve výchozím prohlížeči bude vykreslovat data ve správném formátu podle vašeho národního prostředí.

* `DataType` Atribut můžete povolit MVC zvolit šablonu pravé pole k poskytnutí dat ( `DisplayFormat` pokud používá sama používá šablony řetězce).

> [!NOTE]
> k ověřování jQuery nefunguje při využití `Range` atribut a `DateTime`. Například následující kód se vždy zobrazí chyba ověření na straně klienta, i v případě, že datum je v zadaném rozsahu:
>
> `[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]`

Je potřeba zakázat ověřování jQuery data použít `Range` atributem `DateTime`. Není obvykle vhodné pro kompilaci pevného data ve vašich modelů použít `Range` atribut a `DateTime` se nedoporučuje.

Následující kód ukazuje kombinace atributů na jednom řádku:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

V další části této série jsme zkontrolujte aplikaci a proveďte několik vylepšení pro automaticky generované `Details` a `Delete` metody.

## <a name="additional-resources"></a>Další zdroje

* [Práce s formuláři](xref:mvc/views/working-with-forms)
* [Globalizace a lokalizace](xref:fundamentals/localization)
* [Úvod do pomocné rutiny značek](xref:mvc/views/tag-helpers/intro)
* [Autor pomocných rutin značek](xref:mvc/views/tag-helpers/authoring)

> [!div class="step-by-step"]
> [Předchozí](new-field.md)
> [další](details.md)  
