# <a name="add-validation-to-an-aspnet-core-mvc-app"></a>Přidání ověřování do aplikace ASP.NET Core MVC

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části přidáte logiku ověřování k `Movie` model a zajistíte, že ověřovacích pravidel se vynucují kdykoli uživatel vytvoří nebo upraví videa.

## <a name="keeping-things-dry"></a>Udržování věci zkušební

Jedním z principů návrhu MVC je [suchého](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("není opakujte sami"). ASP.NET Core MVC umožňuje zadat funkci nebo chování pouze jednou a pak jste ji všude, kde se projeví v aplikaci. To snižuje množství kódu, které potřebujete k zápisu a provede kód, který napíšete méně chyby náchylné k chybám, usnadňuje testování a jednodušší správu.

Podpora ověřování poskytované MVC a Entity Framework Core Code First je dobrým příkladem zásada SUCHÝCH v akci. Ověřovací pravidla můžete zadat pomocí deklarace na jednom místě (ve třídě modelu) a pravidel se vynucují kdekoli v aplikaci.

## <a name="adding-validation-rules-to-the-movie-model"></a>Přidání pravidel ověřování do modelu movie

Otevřít *Movie.cs* souboru. DataAnnotations poskytuje integrovanou sadu atributů ověření, které se vztahují deklarativně třída nebo vlastnost. (Také obsahuje formátování atributů, jako je `DataType` , pomoct při formátování a neposkytují žádné ověřování.)

Aktualizace `Movie` třídy, které chcete využít výhod integrovaného `Required`, `StringLength`, `RegularExpression`, a `Range` atributů ověření.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie21/Models/MovieDateRatingDA.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDA.cs?name=snippet1)]

::: moniker-end

Ověření atributy určují chování, které chcete vynutit na vlastnosti projektu, které se použijí. `Required` a `MinimumLength` atributy znamená, že vlastnost musí mít hodnotu, ale nic zabraňuje uživateli v zadávání prázdnými znaky až splňovat toto ověření. `RegularExpression` Atribut se používá k omezení znaků, může být vstupní. Ve výše uvedeném kódu `Genre` a `Rating` musí používat jen písmena (první písmeno velké písmeno, bílý prostor, číslice a speciální znaky nejsou povoleny). `Range` Atribut omezuje hodnotu do zadaného rozsahu. `StringLength` Atribut umožňuje nastavit maximální délka vlastnosti typu string a volitelně jeho minimální délka. Typy hodnot (například `decimal`, `int`, `float`, `DateTime`) jsou ze své podstaty povinné a nemusíte `[Required]` atribut.

Ověřovací pravidla automaticky vynucuje sada ASP.NET Core s pomáhá vytvářet aplikace pro více robustní. Také zajistí, že nelze zapomenete něco ověření a neúmyslně nechat chybná data do databáze.

## <a name="validation-error-ui-in-mvc"></a>Chyba ověření uživatelského rozhraní v aplikaci MVC

Spusťte aplikaci a přejděte na filmy kontroleru.

Klepněte **vytvořit nový** odkaz na přidání nového videa. Vyplňte formulář pomocí některé neplatné hodnoty. Jakmile jQuery ověřování na straně klienta zjistí chyby, zobrazí chybovou zprávu.

![Video zobrazit formulář s více chyby ověření na straně klienta jQuery](~/tutorials/first-mvc-app/validation/_static/val.png)

[!INCLUDE[](~/includes/currency.md)]

Všimněte si, jak formulář automaticky vykreslí příslušné ověřovací chybovou zprávu do každého pole, který obsahuje neplatnou hodnotu. Chyby se vynucují straně klienta (pomocí jazyků JavaScript a jQuery) a na straně serveru (v případě má zakázaný JavaScript).

Významné výhodou je, že nemusíte změnit jediný řádek kódu v `MoviesController` třídy nebo *Create.cshtml* zobrazení, chcete-li povolit toto ověření uživatelského rozhraní. Kontroler a zobrazení, které jste vytvořili dříve v tomto kurzu automaticky vybere nahoru ověřovací pravidla, které jste zadali pomocí atributů ověření na vlastnosti `Movie` třída modelu. Test ověření pomocí `Edit` je použít metody akce a stejné ověřování.

Data formuláře neodešle na server, dokud nedojde k žádným chybám ověření na straně klienta. Můžete to ověřit tak, že vložíte přerušení `HTTP Post` metodu, pomocí [nástroj Fiddler](http://www.telerik.com/fiddler) , nebo [nástroje pro vývojáře F12](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).

## <a name="how-validation-works"></a>Jak funguje ověřování

Může vás zajímat, jak byl vygenerován ověření uživatelského rozhraní bez jakýchkoli aktualizací kódu v kontroleru nebo zobrazení. Následující kód ukazuje dva `Create` metody.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

První (HTTP GET) `Create` zobrazí úvodní formulář vytvořit metody akce. Druhý (`[HttpPost]`) verze zpracovává odeslaného formuláře. Druhá `Create` – metoda ( `[HttpPost]` verze) volání `ModelState.IsValid` ke kontrole, jestli má tento film všechny chyby ověření. Voláním této metody vyhodnotí všechny atributy ověření, které se použily k objektu. Pokud objekt má chyby ověření, `Create` metoda znovu zobrazí formulář. Pokud nejsou žádné chyby, metoda tento nový film uloží v databázi. V našem příkladu film formuláři není odeslána na server po ověření chyby zjištěné na straně klienta; Druhá `Create` metoda se nikdy nevolá, když jsou chyby ověření na straně klienta. Pokud zakážete jazyka JavaScript v prohlížeči, je zakázáno ověřování na straně klienta a budete moci otestovat HTTP POST `Create` metoda `ModelState.IsValid` detekce všech chyb ověření.

Lze nastavit bod přerušení v `[HttpPost] Create` metoda a ověřit se nikdy nevolá metodu, ověřování na straně klienta nebude odeslat data formuláře, když se zjistí chyby ověření. Pokud zakážete jazyka JavaScript v prohlížeči a pak odesláním formuláře s chybami, se setkají bodu přerušení. Vy i přesto úplného ověření bez jazyka JavaScript. 

Následující obrázek ukazuje, jak zakázat jazyka JavaScript v prohlížeči FireFox.

![Firefox:, Zrušte zaškrtnutí políčka Povolit Javascript na kartě obsah z možností.](~/tutorials/first-mvc-app/validation/_static/ff.png)

Následující obrázek ukazuje, jak zakázat jazyka JavaScript v prohlížeči Chrome.

![Google Chrome: V části Nastavení obsahu v jazyce Javascript, neumožňují vyberte libovolnou lokalitu ke spuštění jazyka JavaScript.](~/tutorials/first-mvc-app/validation/_static/chrome.png)

Po zakázání JavaScript odeslat neplatná data a projít ladicí program.

![Při ladění na příspěvek neplatných dat, technologie Intellisense v ModelState.IsValid ukazuje, že je hodnota false.](~/tutorials/first-mvc-app/validation/_static/ms.png)

Následuje část *Create.cshtml* zobrazit šablonu, která automaticky dříve v tomto kurzu. Používá se pomocí metody akce, které jsou uvedené výše obě počáteční formulář pro zobrazení a znovu ho zobrazit v případě chyby.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Views/Movies/CreateRatingBrevity.cshtml)]

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

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Je potřeba zakázat ověřování jQuery data použít `Range` atributem `DateTime`. Není obvykle vhodné pro kompilaci pevného data ve vašich modelů použít `Range` atribut a `DateTime` se nedoporučuje.

Následující kód ukazuje kombinace atributů na jednom řádku:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie21/Models/MovieDateRatingDAmult.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDAmult.cs?name=snippet1)]

::: moniker-end

V další části série, přidáme zkontrolujte, zda aplikace a vylepšování některé automaticky generované `Details` a `Delete` metody.

## <a name="additional-resources"></a>Další zdroje

* [Práce s formuláři](xref:mvc/views/working-with-forms)
* [Globalizace a lokalizace](xref:fundamentals/localization)
* [Úvod do pomocné rutiny značek](xref:mvc/views/tag-helpers/intro)
* [Autor pomocných rutin značek](xref:mvc/views/tag-helpers/authoring)
