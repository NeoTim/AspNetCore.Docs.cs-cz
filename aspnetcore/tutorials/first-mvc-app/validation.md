---
title: Přidání ověření do aplikace core mvc ASP.NET
author: rick-anderson
description: Jak přidat ověření do aplikace ASP.NET Core.
ms.author: riande
ms.date: 04/13/2017
uid: tutorials/first-mvc-app/validation
ms.openlocfilehash: ecf3d011b38347eb32020df00e44d93ca789443a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80242533"
---
# <a name="add-validation-to-an-aspnet-core-mvc-app"></a>Přidání ověření do aplikace core mvc ASP.NET

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části:

* Logika ověření je `Movie` přidána do modelu.
* Zajistíte, aby ověřovací pravidla byla vynucena při každém vytvoření nebo úpravě filmu uživatelem.

## <a name="keeping-things-dry"></a>Udržování věcí v suchu

Jedním z konstrukčních principů MVC je [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("Neopakujte se"). ASP.NET Core MVC doporučuje zadat funkce nebo chování pouze jednou a pak se projeví všude v aplikaci. To snižuje množství kódu, který potřebujete k zápisu a umožňuje kód, který píšete méně náchylné k chybám, snadněji testovat a snadněji udržovat.

Podpora validace poskytovaná společností MVC a core code frameworkem First je dobrým příkladem principu DRY v akci. Můžete deklarativně zadat ověřovací pravidla na jednom místě (ve třídě modelu) a pravidla jsou vynucena všude v aplikaci.

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

## <a name="validation-error-ui"></a>Urozhraní s chybou ověření

Spusťte aplikaci a přejděte na ovladač Filmy.

Klepnutím na odkaz **Vytvořit nový** přidáte nový film. Vyplňte formulář s některými neplatnými hodnotami. Jakmile ověření na straně klienta jQuery zjistí chybu, zobrazí se chybová zpráva.

![Formulář zobrazení filmu s více chybami ověření na straně klienta jQuery](~/tutorials/first-mvc-app/validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Všimněte si, jak formulář automaticky vykreslil příslušnou chybovou zprávu ověření v každém poli obsahující neplatnou hodnotu. Chyby jsou vynuceny jak na straně klienta (pomocí JavaScriptu a jQuery), tak na straně serveru (v případě, že má uživatel JavaScript zakázán).

Významnou výhodou je, že jste nemuseli měnit jeden `MoviesController` řádek kódu ve třídě nebo v zobrazení *Create.cshtml,* abyste mohli povolit toto ověřovací ui. Řadič a zobrazení, které jste vytvořili dříve v tomto kurzu automaticky zvedl ověřovací pravidla, která jste zadali pomocí atributů ověření na vlastnosti třídy `Movie` modelu. Test ověření `Edit` pomocí metody akce a stejné ověření se použije.

Data formuláře nejsou odeslána na server, dokud neexistují žádné chyby ověření na straně klienta. Můžete to ověřit umístěním bodu `HTTP Post` přerušení metody pomocí [nástroje Fiddler](https://www.telerik.com/fiddler) nebo [nástrojů pro vývojáře F12](/microsoft-edge/devtools-guide).

## <a name="how-validation-works"></a>Jak ověření funguje

Možná se divíte, jak bylo generováno ověřovací umělá vláda bez jakýchkoli aktualizací kódu v kontroleru nebo zobrazeních. Následující kód ukazuje `Create` dvě metody.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

První metoda akce `Create` (HTTP GET) zobrazí počáteční formulář Create. Druhá verze`[HttpPost]`( ) zpracovává formulářový příspěvek. Druhá `Create` metoda (Verze) `[HttpPost]` `ModelState.IsValid` volá ke kontrole, zda film má nějaké chyby ověření. Volání této metody vyhodnotí všechny ověřovací atributy, které byly použity na objekt. Pokud má objekt chyby `Create` ověření, metoda znovu zobrazí formulář. Pokud nejsou žádné chyby, metoda uloží nový film v databázi. V našem příkladu filmu není formulář zaúčtován na server, pokud jsou na straně klienta zjištěny chyby ověření; druhá `Create` metoda je nikdy volána, pokud existují chyby ověření na straně klienta. Pokud v prohlížeči zakážete JavaScript, ověření klienta `Create` `ModelState.IsValid` je zakázáno a můžete otestovat metodu HTTP POST, která zjistí chyby ověření.

Můžete nastavit bod přerušení `[HttpPost] Create` v metodě a ověřit, že metoda není nikdy volána, ověření na straně klienta nebude odesílat data formuláře při zjištění chyb ověření. Pokud ve svém prohlížeči zakážete JavaScript a poté odešlete formulář s chybami, bude bod přerušení zasažen. Stále získáte úplné ověření bez JavaScriptu. 

Následující obrázek ukazuje, jak zakázat JavaScript v prohlížeči Firefox.

![Firefox: Na kartě Obsah možností zaškrtněte políčko Povolit Javascript.](~/tutorials/first-mvc-app/validation/_static/ff.png)

Následující obrázek ukazuje, jak zakázat JavaScript v prohlížeči Chrome.

![Google Chrome: V části Javascript v nastavení obsahu vyberte Nepovolit žádnému webu spouštět JavaScript.](~/tutorials/first-mvc-app/validation/_static/chrome.png)

Po zakázání Jazyka JavaScript zaúčtujete neplatná data a projděte ladicí program.

![Při ladění na post neplatných dat, Intellisense na ModelState.IsValid ukazuje, že hodnota je false.](~/tutorials/first-mvc-app/validation/_static/ms.png)

Část šablony zobrazení *Create.cshtml* je zobrazena v následujících značkách:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/CreateRatingBrevity.html)]

Předchozí značky se používají metody akce k zobrazení počátečního formuláře a k jeho opětovnému zobrazení v případě chyby.

Pomocník [vstupních značek](xref:mvc/views/working-with-forms) používá [atributy DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta. Pomocná [pomoc s ověřovacíznačkou](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zobrazuje chyby ověření. Další informace naleznete v [tématu Ověření.](xref:mvc/models/validation)

Co je opravdu pěkné o tomto přístupu je, že řadič ani šablona `Create` zobrazení neví nic o skutečné ověřovací pravidla jsou vynucena nebo o konkrétní chybové zprávy zobrazené. Ověřovací pravidla a chybové řetězce jsou `Movie` určeny pouze ve třídě. Tato stejná ověřovací pravidla `Edit` jsou automaticky použita pro zobrazení a všechny ostatní šablony zobrazení, které můžete vytvořit, které upravují váš model.

Když potřebujete změnit logiku ověření, můžete tak učinit přesně na jednom místě přidáním atributů ověření do modelu (v tomto příkladu `Movie` třída). Nebudete se muset starat o různé části aplikace, které nejsou v souladu s tím, jak jsou pravidla vynucena – veškerá logika ověření bude definována na jednom místě a použita všude. To udržuje kód velmi čistý a usnadňuje údržbu a vývoj. A to znamená, že budete plně ctít princip DRY.

## <a name="using-datatype-attributes"></a>Použití atributů datového typu

Otevřete soubor *Movie.cs* `Movie` a zkontrolujte třídu. Obor `System.ComponentModel.DataAnnotations` názvů poskytuje kromě předdefinované sady atributů ověření také atributy formátování. Již jsme použili `DataType` hodnotu výčtu pro datum vydání a pro pole cen. Následující kód ukazuje `ReleaseDate` `Price` vlastnosti a `DataType` s příslušným atributem.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Atributy `DataType` poskytují pouze rady pro modul zobrazení pro formátování dat (a poskytuje `<a>` prvky/atributy, například pro adresy URL a `<a href="mailto:EmailAddress.com">` e-mail. `RegularExpression` Atribut můžete použít k ověření formátu dat. Atribut `DataType` se používá k určení datového typu, který je konkrétnější než vnitřní typ databáze, nejsou ověřovací atributy. V tomto případě chceme pouze sledovat datum, ne čas. `DataType` Výčet poskytuje mnoho datových typů, jako je například datum, čas, telefonní číslo, měna, e-mailová adresa a další. Atribut `DataType` může také povolit aplikaci automaticky poskytovat funkce specifické pro daný typ. Lze například `mailto:` vytvořit odkaz `DataType.EmailAddress`pro aplikaci a `DataType.Date` v prohlížečích podporujících html5 lze zadat volič data. Atributy `DataType` vyzařují atributy HTML 5 `data-` (vyslovuje se pomlčka dat), kterým prohlížeče HTML 5 rozumějí. Atributy `DataType` **neposkytují** žádné ověření.

`DataType.Date`neurčuje formát zobrazeného data. Ve výchozím nastavení je datové pole zobrazeno podle výchozích formátů založených na souborech serveru `CultureInfo`.

Atribut `DisplayFormat` se používá k explicitnímu zadání formátu data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

Toto `ApplyFormatInEditMode` nastavení určuje, že formátování by mělo být použito také při zobrazení hodnoty v textovém poli pro úpravy. (To možná nechcete u některých polí , například u hodnot měny pravděpodobně nechcete, aby byl symbol měny v textovém poli pro úpravy určen.)

Atribut můžete `DisplayFormat` použít samostatně, ale je obecně vhodné použít `DataType` atribut. Atribut `DataType` vyjadřuje sémantiku dat na rozdíl od způsobu jejich vykreslení na obrazovce a poskytuje následující výhody, které nezískáte s DisplayFormat:

* Prohlížeč může povolit funkce HTML5 (například pro zobrazení ovládacího prvku kalendáře, symbolměny odpovídající národnímu prostředí, odkazy na e-mailatd.)

* Ve výchozím nastavení prohlížeč vykreslí data ve správném formátu na základě národního prostředí.

* Atribut `DataType` může povolit MVC zvolit správnou šablonu pole `DisplayFormat` pro vykreslení dat (pokud používá sám používá šablonu řetězce).

> [!NOTE]
> jQuery ověření nefunguje s `Range` atributem a `DateTime`. Například následující kód vždy zobrazí chybu ověření na straně klienta, i když je datum v zadaném rozsahu:
>
> `[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]`

Chcete-li atribut použít s `Range` `DateTime`programem . Obecně není vhodné kompilovat tvrdá data ve vašich `Range` modelech, takže použití atributu a `DateTime` je odrazováno.

Následující kód ukazuje kombinování atributů na jednom řádku:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

V další části série, zkontrolujeme aplikaci a provést některá vylepšení automaticky generované `Details` a `Delete` metody.

## <a name="additional-resources"></a>Další zdroje

* [Práce s formuláři](xref:mvc/views/working-with-forms)
* [Globalizace a lokalizace](xref:fundamentals/localization)
* [Úvod do pomocné spoje značek](xref:mvc/views/tag-helpers/intro)
* [Pomocné s tagy autora](xref:mvc/views/tag-helpers/authoring)

> [!div class="step-by-step"]
> [Předchozí](new-field.md)
> [další](details.md)  
