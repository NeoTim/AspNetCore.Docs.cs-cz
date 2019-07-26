# <a name="add-validation-to-an-aspnet-core-mvc-app"></a>Přidání ověřování do ASP.NET Core aplikace MVC

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části přidáte logiku ověřování k `Movie` modelu a zajistěte, aby se ověřovací pravidla vynutila pokaždé, když uživatel vytvoří nebo upraví film.

## <a name="keeping-things-dry"></a>Udržování věcí v SUŠINě

Jedna z principy návrhu MVC je suchá [](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("princip"Neopakuj se""). ASP.NET Core MVC vám doporučuje zadat funkce nebo chování jenom jednou a pak je nechat odrážet všude v aplikaci. Tím se sníží množství kódu, který musíte napsat, a kód, který zapíšete méně náchylnou k chybám, bude snazší ho testovat a snáze udržovat.

Podpora ověřování poskytovaná MVC a Entity Framework Core Code First je dobrým příkladem SUCHÉho principu v akci. Můžete deklarativně zadat pravidla ověřování na jednom místě (ve třídě modelu) a pravidla se vynutila všude v aplikaci.

## <a name="adding-validation-rules-to-the-movie-model"></a>Přidání ověřovacích pravidel do modelu filmů

Otevřete soubor *Movie.cs* . Tato vlastnost poskytuje vestavěnou sadu ověřovacích atributů, které lze použít deklarativně pro libovolnou třídu nebo vlastnost. (Obsahuje také atributy formátování, jako `DataType` je například formátování a neposkytuje žádné ověřování.)

`Range` `RegularExpression` `StringLength` `Required`Aktualizujte tříduprovyužitívestavěnýchatributůověřování,,a.`Movie`

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie21/Models/MovieDateRatingDA.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDA.cs?name=snippet1)]

::: moniker-end

Atributy ověřování určují chování, které chcete vyhovět pro vlastnosti modelu, na které se aplikují. Atributy `Required` a`MinimumLength` označují, že vlastnost musí mít hodnotu, ale nic nebrání uživateli v zadání prázdného místa pro splnění tohoto ověření. `RegularExpression` Atribut slouží k omezení znaků, které lze zadat. Ve výše uvedeném `Genre` `Rating` kódu musí být použita pouze písmena (velká písmena, mezera, číslice a speciální znaky nejsou povoleny). `Range` Atribut omezuje hodnotu na v zadaném rozsahu. `StringLength` Atribut umožňuje nastavit maximální délku řetězcové vlastnosti a volitelně její minimální délku. Typy hodnot (například `decimal`, `int`, `float`, `DateTime`) jsou podstatně požadovány a nepotřebují `[Required]` atribut.

Automatické vynucení ověřovacích pravidel nástrojem ASP.NET Core pomáhá zajistit větší odolnost aplikace. Také zajišťuje, že se nebudete moci zapomenout a neúmyslně ověřit data v databázi.

## <a name="validation-error-ui-in-mvc"></a>Uživatelské rozhraní chyby ověřování v MVC

Spusťte aplikaci a přejděte na kontroler filmů.

Klepnutím na odkaz **vytvořit nový** přidejte nový film. Vyplňte formulář s některými neplatnými hodnotami. Jakmile při ověřování na straně klienta jQuery dojde k chybě, zobrazí se chybová zpráva.

![Formulář zobrazení videa s několika chybami ověřování na straně klienta jQuery](~/tutorials/first-mvc-app/validation/_static/val.png)

[!INCLUDE[](~/includes/currency.md)]

Všimněte si, jak formulář automaticky vygeneroval příslušnou chybovou zprávu ověřování v každém poli, které obsahuje neplatnou hodnotu. Chyby se vynutily na straně klienta (pomocí JavaScriptu a jQuery) a na straně serveru (Pokud uživatel má zakázaný JavaScript).

Významnou výhodou je, že nemusíte změnit jeden řádek kódu ve `MoviesController` třídě nebo v zobrazení *vytvořit. cshtml* , aby bylo možné toto uživatelské rozhraní pro ověřování povolit. Kontroler a zobrazení, které jste vytvořili dříve v tomto kurzu, automaticky vybrala ověřovací pravidla, která jste zadali pomocí atributů ověřování ve vlastnostech `Movie` třídy modelu. Ověření testu pomocí `Edit` metody Action a je použito stejné ověřování.

Data formuláře se neodesílají na server, dokud nedojde k žádným chybám při ověřování na straně klienta. To můžete ověřit tak, že umístíte bod přerušení do `HTTP Post` metody, pomocí [nástroje Fiddler](https://www.telerik.com/fiddler) nebo vývojářských [nástrojů F12](/microsoft-edge/devtools-guide).

## <a name="how-validation-works"></a>Jak funguje ověřování

Můžete se setkat s tím, jak se ověřovací uživatelské rozhraní vygenerovalo bez jakýchkoli aktualizací kódu v řadiči nebo zobrazeních. Následující kód ukazuje dvě `Create` metody.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

První metoda akce (http Get `Create` ) zobrazí počáteční formulář pro vytvoření. Druhá (`[HttpPost]`) verze zpracovává příspěvek formuláře. Druhá `Create` metoda `ModelState.IsValid` (verze) volá k ověření, zda film obsahuje chyby ověřování. `[HttpPost]` Volání této metody vyhodnotí všechny atributy ověřování, které byly aplikovány na objekt. Pokud objekt obsahuje chyby ověřování, `Create` metoda znovu zobrazí formulář. Pokud nejsou k dispozici žádné chyby, metoda uloží nový film do databáze. V našem příkladu filmu není formulář na straně klienta publikovaný na serveru, když se zjistily chyby ověřování. Druhá `Create` metoda se nikdy nevolá, když dojde k chybám ověřování na straně klienta. Zakážete-li jazyk JavaScript v prohlížeči, bude ověřování klienta zakázáno a můžete otestovat metodu `Create` `ModelState.IsValid` http post, která detekuje chyby ověřování.

V `[HttpPost] Create` metodě můžete nastavit bod přerušení a ověřit, že metoda není nikdy volána, ověřování na straně klienta při zjištění chyb ověřování neodešle data formuláře. Pokud v prohlížeči zakážete JavaScript, pak formulář odešle s chybami, bude k dispozice bod přerušení. Pořád se vám zobrazí úplné ověření bez JavaScriptu. 

Následující obrázek ukazuje, jak zakázat JavaScript v prohlížeči FireFox.

![Firefox Na kartě obsah u možnosti zrušte zaškrtávací políčko Povolit JavaScript.](~/tutorials/first-mvc-app/validation/_static/ff.png)

Následující obrázek ukazuje, jak zakázat JavaScript v prohlížeči Chrome.

![Google Chrome: V části JavaScript nastavení obsahu vyberte možnost nepovolit žádnému webovému serveru spustit JavaScript.](~/tutorials/first-mvc-app/validation/_static/chrome.png)

Po zakázání JavaScriptu vystavte neplatná data a Projděte je prostřednictvím ladicího programu.

![Při ladění na příspěvku neplatných dat IntelliSense v ModelState. IsValid zobrazuje hodnotu false.](~/tutorials/first-mvc-app/validation/_static/ms.png)

Níže je uvedená část šablony zobrazení *Create. cshtml* , kterou jste vystavili dříve v tomto kurzu. Používá metody akcí zobrazené výše k zobrazení počátečního formuláře a jeho zobrazení v případě chyby.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Views/Movies/CreateRatingBrevity.cshtml)]

[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) používá atributy DataAnnotations a vytváří atributy HTML potřebné k ověření jQuery na straně klienta. [](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) [Pomocník pro ověřování značek](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zobrazí chyby ověřování. Další informace najdete v tématu [ověření](xref:mvc/models/validation) .

To je prakticky Skvělé, že tento přístup není tím, že kontroler `Create` ani šablona zobrazení neví žádné informace o skutečných ověřovacích pravidlech a o tom, jaké jsou zobrazené chybové zprávy. Ověřovací pravidla a řetězce chyb jsou určeny pouze ve `Movie` třídě. Tato pravidla ověřování se automaticky aplikují na `Edit` zobrazení a na další šablony zobrazení, které můžete vytvořit, když tento model upravíte.

Pokud potřebujete změnit logiku ověřování, můžete tak učinit přesně na jednom místě přidáním ověřovacích atributů do modelu (v tomto příkladu `Movie` třídy). Nemusíte se starat o různé části aplikace, které jsou nekonzistentní s tím, jak se pravidla uplatňují – veškerá logika ověřování bude definovaná na jednom místě a bude se používat všude. Tím se kód neustále čistí a usnadňuje se jeho údržba a vývoj. A to znamená, že budete plně dodržovat zásadu SUCHÉho.

## <a name="using-datatype-attributes"></a>Použití atributů DataType

Otevřete soubor *Movie.cs* a prověřte `Movie` třídu. `System.ComponentModel.DataAnnotations` Obor názvů poskytuje kromě předdefinované sady ověřovacích atributů i atributy formátování. Pro datum vydání a pole `DataType` s cenami jsme už použili hodnotu výčtu. Následující kód ukazuje `ReleaseDate` vlastnosti a `Price` s odpovídajícím `DataType` atributem.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Atributy poskytují nápovědu pouze pro modul zobrazení k formátování dat (a poskytování prvků nebo atributů, `<a>` například pro adresu URL a `<a href="mailto:EmailAddress.com">` pro e-mail. `DataType` Můžete použít `RegularExpression` atribut k ověření formátu dat. `DataType` Atribut slouží k určení datového typu, který je konkrétnější než vnitřní typ databáze, ale nejedná se o atributy ověřování. V tomto případě chceme sledovat pouze datum, nikoli čas. `DataType` Výčet poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress a další. `DataType` Atribut může také povolit aplikaci automatické poskytování funkcí specifických pro typ. Například `mailto:` odkaz lze vytvořit pro `DataType.EmailAddress`a `DataType.Date` v prohlížečích, které podporují HTML5, lze zadat selektor data. Atributy generují atributy HTML 5 `data-` (s vyslovnou datovou pomlčkou), které mohou prohlížeče formátu HTML 5 pochopit. `DataType` Atributy neposkytují žádné ověřování.  `DataType`

`DataType.Date`neurčuje formát data, které se zobrazí. Ve výchozím nastavení se datové pole zobrazuje v závislosti na výchozích formátech založených na serveru `CultureInfo`.

`DisplayFormat` Atribut slouží k explicitnímu zadání formátu data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode` Nastavení určuje, že formátování by mělo být použito i v případě, že se hodnota zobrazí v textovém poli pro úpravy. (Pro některá pole (například pro hodnoty měny možná nebudete chtít), v textovém poli pro úpravy pravděpodobně nebudete chtít symbol měny.)

Můžete použít `DisplayFormat` atribut sám o sobě, ale obecně je vhodné `DataType` použít atribut. `DataType` Atribut předává sémantiku dat na rozdíl od způsobu vykreslování na obrazovce a poskytuje následující výhody, které nezískáte pomocí DisplayFormat:

* Prohlížeč může povolit funkce HTML5 (například pro zobrazení ovládacího prvku kalendáře, symbolu měny odpovídající národním prostředí, e-mailových odkazů atd.)

* Ve výchozím nastavení bude prohlížeč data vykreslovat pomocí správného formátu na základě vašeho národního prostředí.

* Atribut může MVC povolit, aby vybrali šablonu pravého pole pro vykreslení dat `DisplayFormat` (Pokud se používá v samotném případě, používá šablonu řetězce). `DataType`

> [!NOTE]
> ověřování jQuery nefunguje s `Range` atributem a. `DateTime` Například následující kód bude zobrazovat chybu ověřování na straně klienta, a to i v případě, že je datum v zadaném rozsahu:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Chcete-li použít `Range` atribut s `DateTime`, bude nutné zakázat ověření data jQuery. Obvykle není dobrým zvykem při kompilování pevných dat ve vašich modelech, takže použití `Range` atributu a `DateTime` nedoporučuje se.

Následující kód ukazuje kombinování atributů na jednom řádku:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie21/Models/MovieDateRatingDAmult.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDAmult.cs?name=snippet1)]

::: moniker-end

V další části série si probereme aplikaci a provedeme některá vylepšení automaticky generovaných `Details` a `Delete` metod.

## <a name="additional-resources"></a>Další zdroje

* [Práce s formuláři](xref:mvc/views/working-with-forms)
* [Globalizace a lokalizace](xref:fundamentals/localization)
* [Úvod k pomocníkům značek](xref:mvc/views/tag-helpers/intro)
* [Vytváření pomocníků se značkami](xref:mvc/views/tag-helpers/authoring)
