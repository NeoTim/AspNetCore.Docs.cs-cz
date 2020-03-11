---
title: Přidání ověřování do ASP.NET Core aplikace MVC
author: rick-anderson
description: Postup přidání ověřování do aplikace ASP.NET Core.
ms.author: riande
ms.date: 04/13/2017
uid: tutorials/first-mvc-app/validation
ms.openlocfilehash: 2bb4ed173d154e3b7457ce3f8009f0f9406e36c4
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661964"
---
# <a name="add-validation-to-an-aspnet-core-mvc-app"></a>Přidání ověřování do ASP.NET Core aplikace MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části:

* Logika ověřování se přidá do modelu `Movie`.
* Ujistěte se, že se ověřovací pravidla vynutila pokaždé, když uživatel vytvoří nebo upraví film.

## <a name="keeping-things-dry"></a>Udržování věcí v SUŠINě

Jedna z principy návrhu MVC je [suchá](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("princip"Neopakuj se""). ASP.NET Core MVC vám doporučuje zadat funkce nebo chování jenom jednou a pak je nechat odrážet všude v aplikaci. Tím se sníží množství kódu, který musíte napsat, a kód, který zapíšete méně náchylnou k chybám, bude snazší ho testovat a snáze udržovat.

Podpora ověřování poskytovaná MVC a Entity Framework Core Code First je dobrým příkladem SUCHÉho principu v akci. Můžete deklarativně zadat pravidla ověřování na jednom místě (ve třídě modelu) a pravidla se vynutila všude v aplikaci.

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

## <a name="validation-error-ui"></a>Uživatelské rozhraní chyby ověřování

Spusťte aplikaci a přejděte na kontroler filmů.

Klepnutím na odkaz **vytvořit nový** přidejte nový film. Vyplňte formulář s některými neplatnými hodnotami. Jakmile při ověřování na straně klienta jQuery dojde k chybě, zobrazí se chybová zpráva.

![Formulář zobrazení videa s několika chybami ověřování na straně klienta jQuery](~/tutorials/first-mvc-app/validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Všimněte si, jak formulář automaticky vygeneroval příslušnou chybovou zprávu ověřování v každém poli, které obsahuje neplatnou hodnotu. Chyby se vynutily na straně klienta (pomocí JavaScriptu a jQuery) a na straně serveru (Pokud uživatel má zakázaný JavaScript).

Významnou výhodou je, že nemusíte změnit jeden řádek kódu ve třídě `MoviesController` nebo v zobrazení *vytvořit. cshtml* , aby bylo možné toto uživatelské rozhraní pro ověřování povolit. Kontroler a zobrazení, které jste vytvořili dříve v tomto kurzu, automaticky vybrala ověřovací pravidla, která jste zadali pomocí atributů ověřování ve vlastnostech třídy `Movie` modelu. Ověření testu pomocí metody `Edit` akce a je použito stejné ověřování.

Data formuláře se neodesílají na server, dokud nedojde k žádným chybám při ověřování na straně klienta. To můžete ověřit tak, že umístíte bod přerušení do metody `HTTP Post`, pomocí [nástroje Fiddler](https://www.telerik.com/fiddler) nebo [vývojářských nástrojů F12](/microsoft-edge/devtools-guide).

## <a name="how-validation-works"></a>Jak funguje ověřování

Můžete se setkat s tím, jak se ověřovací uživatelské rozhraní vygenerovalo bez jakýchkoli aktualizací kódu v řadiči nebo zobrazeních. Následující kód ukazuje dvě metody `Create`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

První metoda (HTTP GET) `Create` akce zobrazí počáteční formulář pro vytvoření. Druhá verze (`[HttpPost]`) zpracovává příspěvek formuláře. Druhá metoda `Create` (verze `[HttpPost]`) volá `ModelState.IsValid`, aby zkontrolovala, zda film obsahuje chyby ověřování. Volání této metody vyhodnotí všechny atributy ověřování, které byly aplikovány na objekt. Pokud objekt obsahuje chyby ověřování, metoda `Create` znovu zobrazí formulář. Pokud nejsou k dispozici žádné chyby, metoda uloží nový film do databáze. V našem příkladu filmu není formulář na straně klienta publikovaný na serveru, když se zjistily chyby ověřování. Druhá metoda `Create` se nikdy nevolá, když dojde k chybám ověřování na straně klienta. Zakážete-li jazyk JavaScript v prohlížeči, bude ověřování klienta zakázáno a můžete otestovat metodu HTTP POST `Create` `ModelState.IsValid` zjistit chyby ověřování.

Můžete nastavit bod přerušení v metodě `[HttpPost] Create` a ověřit, zda metoda není nikdy volána, ověřování na straně klienta nebude odesílat data formuláře, pokud jsou zjištěny chyby ověřování. Pokud v prohlížeči zakážete JavaScript, pak formulář odešle s chybami, bude k dispozice bod přerušení. Pořád se vám zobrazí úplné ověření bez JavaScriptu. 

Následující obrázek ukazuje, jak zakázat JavaScript v prohlížeči FireFox.

![Firefox: na kartě obsah u možnosti zrušte políčko Povolit JavaScript.](~/tutorials/first-mvc-app/validation/_static/ff.png)

Následující obrázek ukazuje, jak zakázat JavaScript v prohlížeči Chrome.

![Google Chrome: v části JavaScript nastavení obsahu vyberte Nepovolit spouštění JavaScriptu na žádném webu.](~/tutorials/first-mvc-app/validation/_static/chrome.png)

Po zakázání JavaScriptu vystavte neplatná data a Projděte je prostřednictvím ladicího programu.

![Při ladění na příspěvku neplatných dat IntelliSense v ModelState. IsValid zobrazuje hodnotu false.](~/tutorials/first-mvc-app/validation/_static/ms.png)

Část šablony zobrazení *Create. cshtml* je uvedena v následujícím kódu:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/CreateRatingBrevity.html)]

Předchozí kód je používán metodami akcí k zobrazení počátečního formuláře a jeho zobrazení v případě chyby.

[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) používá atributy [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné k ověření jQuery na straně klienta. [Pomocník pro ověřování značek](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zobrazí chyby ověřování. Další informace najdete v tématu [ověření](xref:mvc/models/validation) .

To je prakticky Skvělé o tomto přístupu, protože řadič ani šablona zobrazení `Create` neví žádné informace o skutečných ověřovacích pravidlech, která se vynucuje, nebo o konkrétních zobrazených chybových zprávách. Ověřovací pravidla a řetězce chyb jsou určeny pouze ve třídě `Movie`. Tato pravidla ověřování se automaticky aplikují na `Edit` zobrazení a na všechny další šablony zobrazení, které můžete vytvořit, když tento model upravíte.

Pokud potřebujete změnit logiku ověřování, můžete tak učinit přesně na jednom místě přidáním ověřovacích atributů do modelu (v tomto příkladu třída `Movie`). Nemusíte se starat o různé části aplikace, které jsou nekonzistentní s tím, jak se pravidla uplatňují – veškerá logika ověřování bude definovaná na jednom místě a bude se používat všude. Tím se kód neustále čistí a usnadňuje se jeho údržba a vývoj. A to znamená, že budete plně dodržovat zásadu SUCHÉho.

## <a name="using-datatype-attributes"></a>Použití atributů DataType

Otevřete soubor *Movie.cs* a prověřte třídu `Movie`. Obor názvů `System.ComponentModel.DataAnnotations` poskytuje kromě předdefinované sady ověřovacích atributů také atributy formátování. Pro datum vydání a pole s cenami již jsme použili hodnotu výčtu `DataType`. Následující kód ukazuje vlastnosti `ReleaseDate` a `Price` s odpovídajícím atributem `DataType`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Atributy `DataType` poskytují pouze pomocné parametry pro modul zobrazení k formátování dat (a poskytování prvků nebo atributů, jako je například `<a>` pro adresu URL a `<a href="mailto:EmailAddress.com">` pro e-maily. K ověření formátu dat lze použít atribut `RegularExpression`. Atribut `DataType` slouží k zadání datového typu, který je konkrétnější než vnitřní typ databáze, ale nejedná se o atributy ověřování. V tomto případě chceme sledovat pouze datum, nikoli čas. Výčet `DataType` poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress a další. Atribut `DataType` může také povolit aplikaci automatické poskytování funkcí specifických pro typ. Například odkaz `mailto:` lze vytvořit pro `DataType.EmailAddress`a selektor data lze zadat pro `DataType.Date` v prohlížečích, které podporují HTML5. Atributy `DataType` emitují atributy HTML 5 `data-` (vyslovované datové pomlčky), které mohou prohlížeče HTML 5 pochopit. Atributy `DataType` **neposkytují žádné** ověřování.

`DataType.Date` neurčuje formát data, které se zobrazí. Ve výchozím nastavení se datové pole zobrazuje v závislosti na výchozích formátech na základě `CultureInfo`serveru.

Atribut `DisplayFormat` slouží k explicitnímu zadání formátu data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

Nastavení `ApplyFormatInEditMode` určuje, zda má být formátování použito také v případě, že je hodnota zobrazena v textovém poli pro úpravy. (Pro některá pole (například pro hodnoty měny možná nebudete chtít), v textovém poli pro úpravy pravděpodobně nebudete chtít symbol měny.)

Atribut `DisplayFormat` lze použít samostatně, ale obecně je vhodné použít atribut `DataType`. Atribut `DataType` předává sémantiku dat na rozdíl od způsobu vykreslování na obrazovce a poskytuje následující výhody, které nezískáte pomocí DisplayFormat:

* Prohlížeč může povolit funkce HTML5 (například pro zobrazení ovládacího prvku kalendáře, symbolu měny odpovídající národním prostředí, e-mailových odkazů atd.)

* Ve výchozím nastavení bude prohlížeč data vykreslovat pomocí správného formátu na základě vašeho národního prostředí.

* Atribut `DataType` umožňuje MVC zvolit šablonu pravého pole pro vykreslení dat (`DisplayFormat` Pokud se používá samostatně, používá šablonu řetězce).

> [!NOTE]
> ověřování jQuery nefunguje s atributem `Range` a `DateTime`. Například následující kód bude zobrazovat chybu ověřování na straně klienta, a to i v případě, že je datum v zadaném rozsahu:
>
> `[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]`

Chcete-li použít atribut `Range` s `DateTime`, bude nutné zakázat ověření data jQuery. Obvykle není dobrým zvykem při kompilování pevných dat ve vašich modelech, takže použijte atribut `Range` a `DateTime` se nedoporučuje.

Následující kód ukazuje kombinování atributů na jednom řádku:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

V další části této série si projdeme aplikaci a provedeme některá vylepšení automaticky generovaných `Details` a `Delete`ch metod.

## <a name="additional-resources"></a>Další zdroje

* [Práce s formuláři](xref:mvc/views/working-with-forms)
* [Globalizace a lokalizace](xref:fundamentals/localization)
* [Úvod k pomocníkům značek](xref:mvc/views/tag-helpers/intro)
* [Vytváření pomocníků se značkami](xref:mvc/views/tag-helpers/authoring)

> [!div class="step-by-step"]
> [Předchozí](new-field.md)
> [Další](details.md)  
