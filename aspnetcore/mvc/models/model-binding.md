---
title: Vazba modelu v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak vazba modelu v ASP.NET Core funguje a jak přizpůsobit jeho chování.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
uid: mvc/models/model-binding
ms.openlocfilehash: 19580768679f30131683717792252c03aade68f9
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666276"
---
# <a name="model-binding-in-aspnet-core"></a>Vazba modelu v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Tento článek vysvětluje, co je vazba modelů, jak funguje a jak přizpůsobit její chování.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).

## <a name="what-is-model-binding"></a>Co je vazba modelu

Řadiče a stránky Razor fungují s daty, která pocházejí z požadavků HTTP. Například data směrování můžou poskytovat klíč záznamu a pole odeslaných formulářů můžou poskytovat hodnoty pro vlastnosti modelu. Psaní kódu pro načtení každé z těchto hodnot a jejich převod z řetězců na typy .NET by byly únavné a náchylné k chybám. Vazba modelu automatizuje tento proces. Systém vazby modelů:

* Načte data z různých zdrojů, jako jsou například data směrování, pole formuláře a řetězce dotazů.
* Poskytuje data pro řadiče a stránky Razor v parametrech metod a veřejných vlastnostech.
* Převádí řetězcová data na typy .NET.
* Aktualizuje vlastnosti komplexních typů.

## <a name="example"></a>Příklad

Předpokládejme, že máte následující metodu akce:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

A aplikace obdrží požadavek s touto adresou URL:

```
http://contoso.com/api/pets/2?DogsOnly=true
```

Vazba modelu projde následujícím postupem a poté, co systém směrování vybere metodu akce:

* Najde první parametr `GetByID`, celé číslo s názvem `id`.
* Vyhledá z dostupných zdrojů v požadavku HTTP a v datech směrování najde `id` = "2".
* Převede řetězec "2" na celé číslo 2.
* Najde další parametr `GetByID`, logická hodnota s názvem `dogsOnly`.
* Prohledá zdroje a v řetězci dotazu vyhledá "DogsOnly = true". U porovnávání názvů se nerozlišují malá a velká písmena.
* Převede řetězec "true" na Boolean `true`.

Rozhraní potom zavolá metodu `GetById`, předává 2 pro parametr `id` a `true` pro parametr `dogsOnly`.

V předchozím příkladu jsou cíle vazby modelů parametry metod, které jsou jednoduché typy. Cíle mohou být také vlastnostmi komplexního typu. Po úspěšném vytvoření vazby každé vlastnosti dojde k [ověření modelu](xref:mvc/models/validation) pro danou vlastnost. Záznam o tom, jaká data jsou vázána na model a všechny chyby vazby nebo ověřování, jsou uloženy v [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) nebo [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState). Chcete-li zjistit, zda byl tento proces úspěšný, aplikace zkontroluje příznak [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .

## <a name="targets"></a>Cíle

Vazba modelu se pokusí najít hodnoty pro následující typy cílů:

* Parametry metody akce kontroleru, na kterou je směrován požadavek
* Parametry metody obslužné rutiny Razor Pages, na kterou je směrován požadavek. 
* Veřejné vlastnosti řadiče nebo třídy `PageModel`, pokud jsou určeny atributy.

### <a name="bindproperty-attribute"></a>[BindProperty] – atribut

Dá se použít na veřejnou vlastnost kontroleru nebo `PageModel` třídy, aby vazba modelu mohla cílit na tuto vlastnost:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a>[BindProperties] – atribut

K dispozici v ASP.NET Core 2,1 a novějším.  Dá se použít na kontrolér nebo `PageModel` třídu pro oznámení, že vazba modelu cílí na všechny veřejné vlastnosti třídy:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>Vazba modelu pro požadavky HTTP GET

Ve výchozím nastavení nejsou vlastnosti pro požadavky HTTP GET vázané. Obvykle stačí pro požadavek GET parametr ID záznamu. ID záznamu slouží k vyhledání položky v databázi. Proto není nutné navazovat vlastnost, která obsahuje instanci modelu. Ve scénářích, kdy chcete vlastnosti navázané na data z požadavků GET, nastavte vlastnost `SupportsGet` na `true`:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>Zdroje

Ve výchozím nastavení ve vazbě modelu získává data ve formě párů klíč-hodnota z následujících zdrojů v požadavku HTTP:

1. Pole formuláře
1. Tělo žádosti (pro [řadiče, které mají atribut [ApiController]](xref:web-api/index#binding-source-parameter-inference).)
1. Směrování dat
1. Parametry řetězce dotazu
1. Nahrané soubory

Pro každý cílový parametr nebo vlastnost jsou zdroje prohledávány v pořadí uvedeném v předchozím seznamu. Existuje několik výjimek:

* Data směrování a hodnoty řetězce dotazu jsou používány pouze pro jednoduché typy.
* Nahrané soubory jsou vázány pouze na cílové typy, které implementují `IFormFile` nebo `IEnumerable<IFormFile>`.

Pokud výchozí zdroj není správný, použijte k určení zdroje jeden z následujících atributů:

* [`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) – načte hodnoty z řetězce dotazu. 
* [`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) – načte hodnoty z dat směrování.
* [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) – Získá hodnoty z publikovaných polí formuláře.
* [`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) – načte hodnoty z textu žádosti.
* [`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) – Získá hodnoty z hlaviček protokolu HTTP.

Tyto atributy:

* Jsou přidány do vlastností modelu jednotlivě (nikoli do třídy modelu), jako v následujícím příkladu:

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* Volitelně akceptuje hodnotu názvu modelu v konstruktoru. Tato možnost je k dispozici v případě, že se název vlastnosti neshoduje s hodnotou v požadavku. Například hodnota v požadavku může být záhlavím s pomlčkou v názvu, jako v následujícím příkladu:

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>[FromBody] – atribut

Použijte atribut `[FromBody]` pro parametr k naplnění vlastností z těla požadavku HTTP. Modul runtime ASP.NET Core deleguje zodpovědnost za čtení těla na vstupní formátovací modul. Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).

Při použití `[FromBody]` pro parametr komplexního typu se všechny zdrojové atributy vazby použité na jeho vlastnosti ignorují. Například následující akce `Create` určuje, že `pet` parametr je vyplněný z těla:

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

Třída `Pet` určuje, že jeho vlastnost `Breed` je naplněná z parametru řetězce dotazu:

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

V předchozím příkladu:

* Atribut `[FromQuery]` je ignorován.
* Vlastnost `Breed` není naplněna z parametru řetězce dotazu. 

Formátovací moduly vstupu čtou pouze tělo a nerozumí vazbě zdrojových atributů. Pokud se v těle najde vhodná hodnota, použije se tato hodnota k naplnění vlastnosti `Breed`.

Nepoužívejte `[FromBody]` pro více než jeden parametr na metodu Action. Jakmile je datový proud požadavku čten vstupním formátovacím modulem, již není nadále k dispozici pro vázání dalších parametrů `[FromBody]`.

### <a name="additional-sources"></a>Další zdroje

Zdrojová data jsou k dispozici pro systém vázání modelů podle *zprostředkovatelů hodnot*. Můžete zapisovat a registrovat vlastní poskytovatele hodnot, který získá data pro vazbu modelu z jiných zdrojů. Například můžete chtít data ze souborů cookie nebo stavu relace. Načtení dat z nového zdroje:

* Vytvořte třídu, která implementuje `IValueProvider`.
* Vytvořte třídu, která implementuje `IValueProviderFactory`.
* Zaregistrujte třídu factory v `Startup.ConfigureServices`.

Ukázková aplikace obsahuje [poskytovatele hodnot](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) a [výrobní](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) příklad, který získává hodnoty z souborů cookie. Zde je registrační kód v `Startup.ConfigureServices`:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

Zobrazený kód vloží zprostředkovatele vlastních hodnot po všech vestavěných poskytovatelích hodnot.  Chcete-li jej v seznamu nastavit jako první, zavolejte místo `Add``Insert(0, new CookieValueProviderFactory())`.

## <a name="no-source-for-a-model-property"></a>Žádný zdroj pro vlastnost modelu

Ve výchozím nastavení se chyba stavu modelu nevytvoří, pokud se pro vlastnost modelu nenajde žádná hodnota. Vlastnost je nastavena na hodnotu null nebo na výchozí hodnotu:

* Jednoduché typy s možnou hodnotou null jsou nastavené na `null`.
* Typy hodnot, které nejsou null, jsou nastaveny na `default(T)`. Například parametr `int id` je nastaven na hodnotu 0.
* Pro komplexní typy vazba modelu vytvoří instanci pomocí výchozího konstruktoru bez nastavení vlastností.
* Pole jsou nastavena na `Array.Empty<T>()`, s tím rozdílem, že `byte[]` pole jsou nastavena na `null`.

Pokud má být stav modelu neplatný, pokud se v polích formuláře pro vlastnost modelu nenalezne žádné, použijte atribut [`[BindRequired]`](#bindrequired-attribute) .

Všimněte si, že toto chování `[BindRequired]` se vztahuje na vazbu modelu z publikovaných dat formuláře, nikoli na data JSON nebo XML v těle požadavku. Data těla žádosti jsou zpracována [vstupními formátovacími](#input-formatters)moduly.

## <a name="type-conversion-errors"></a>Chyby konverze typu

Pokud je zdroj nalezen, ale nelze jej převést na cílový typ, stav modelu je označen jako neplatný. Parametr Target nebo Property je nastaven na hodnotu null nebo na výchozí hodnotu, jak je uvedeno v předchozí části.

V kontroleru rozhraní API, který má atribut `[ApiController]`, má neplatný stav modelu za následek automatickou odpověď HTTP 400.

Na stránce Razor znovu zobrazte stránku s chybovou zprávou:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

Ověřování na straně klienta zachytí nejvíc chybných dat, která by jinak byla odeslána do Razor Pages formuláře. Díky tomuto ověření je obtížné aktivovat předchozí zvýrazněný kód. Ukázková aplikace obsahuje tlačítko **Odeslat s neplatným datem** , které do pole **Datum přijetí** vloží nesprávná data a formulář odešle. Toto tlačítko ukazuje, jak kód pro zobrazení stránky funguje, když dojde k chybám převodu dat.

V případě, že je stránka znovu zobrazena v předchozím kódu, není v poli formuláře zobrazen neplatný vstup. Důvodem je to, že vlastnost modelu byla nastavena na hodnotu null nebo na výchozí hodnotu. V chybové zprávě se zobrazí neplatný vstup. Pokud ale chcete, aby se v poli formuláře znovu zobrazila chybná data, je vhodné vytvořit řetězec vlastnosti modelu a provést převod dat ručně.

Pokud nechcete, aby se chyby převodu typů způsobily při chybách stavu modelu, doporučujeme stejnou strategii. V takovém případě nastavte vlastnost model na řetězec.

## <a name="simple-types"></a>Jednoduché typy

Jednoduché typy, které modelový pořadač může převést na zdrojové řetězce, do zahrnují následující:

* [Datového](xref:System.ComponentModel.BooleanConverter)
* [Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)
* [Char](xref:System.ComponentModel.CharConverter)
* [Hodnotu](xref:System.ComponentModel.DateTimeConverter)
* [DateTimeOffset](xref:System.ComponentModel.DateTimeOffsetConverter)
* [Notaci](xref:System.ComponentModel.DecimalConverter)
* [Klepat](xref:System.ComponentModel.DoubleConverter)
* [Vytváření](xref:System.ComponentModel.EnumConverter)
* [Hlavních](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)
* [Konkrétní](xref:System.ComponentModel.SingleConverter)
* [TimeSpan](xref:System.ComponentModel.TimeSpanConverter)
* [UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)
* [Identifikátor URI](xref:System.UriTypeConverter)
* [Verze](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a>Komplexní typy

Aby bylo možné vytvořit vazby komplexního typu, musí mít veřejný výchozí konstruktor a veřejné vlastnosti s možností zápisu. Když dojde k vazbě modelu, instance třídy je vytvořena pomocí veřejného výchozího konstruktoru. 

Pro každou vlastnost komplexního typu vyhledá vazba modelu ve zdrojích *předponu vzoru názvu. property_name*. Pokud není nic nalezeno, vyhledá pouze *Property_Name* bez předpony.

Pro svázání s parametrem je předpona názvem parametru. Pro vazbu na veřejnou vlastnost `PageModel` je předpona název veřejné vlastnosti. Některé atributy mají vlastnost `Prefix`, která umožňuje přepsat výchozí použití parametru nebo názvu vlastnosti.

Předpokládejme například, že komplexní typ je následující třída `Instructor`:

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a>Prefix = název parametru

Pokud je model, který má být svázán, parametr s názvem `instructorToUpdate`:

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

Vazba na model začíná prohledáním zdrojů pro klíčovou `instructorToUpdate.ID`. Pokud se nenajde, vyhledá `ID` bez předpony.

### <a name="prefix--property-name"></a>Prefix = název vlastnosti

Pokud je model, který chcete svázat, vlastnost s názvem `Instructor` kontroleru nebo `PageModel` třídy:

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

Vazba na model začíná prohledáním zdrojů pro klíčovou `Instructor.ID`. Pokud se nenajde, vyhledá `ID` bez předpony.

### <a name="custom-prefix"></a>Vlastní předpona

Pokud je model, který má být svázán, parametr pojmenovaný `instructorToUpdate` a atribut `Bind` určuje `Instructor` jako předponu:

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

Vazba na model začíná prohledáním zdrojů pro klíčovou `Instructor.ID`. Pokud se nenajde, vyhledá `ID` bez předpony.

### <a name="attributes-for-complex-type-targets"></a>Atributy pro cíle komplexního typu

K dispozici je několik předdefinovaných atributů pro řízení vazeb modelu komplexních typů:

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> Tyto atributy ovlivňují vazbu modelu, když jsou publikovaná data formuláře zdrojem hodnot. Neovlivňují vstupní formátovací moduly, které zpracovávají odeslané texty JSON a XML požadavku. Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).
>
> Viz také diskuze o atributu `[Required]` v [ověřování modelu](xref:mvc/models/validation#required-attribute).

### <a name="bindrequired-attribute"></a>[BindRequired] – atribut

Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody. Způsobí, že vazba modelu přidá chybu stavu modelu, pokud pro vlastnost modelu neproběhne vazba. Tady je příklad:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a>[BindNever] – atribut

Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody. Zabraňuje vazbě modelu v nastavení vlastnosti modelu. Tady je příklad:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a>[BIND] – atribut

Lze použít pro třídu nebo parametr metody. Určuje, které vlastnosti modelu by měly být zahrnuty ve vazbě modelu.

V následujícím příkladu jsou při volání jakékoli obslužné rutiny nebo metody akce vázány pouze zadané vlastnosti `Instructor` modelu:

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

V následujícím příkladu jsou při volání metody `OnPost` vázány pouze zadané vlastnosti `Instructor`ho modelu:

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

Atribut `[Bind]` lze použít k ochraně před přeúčtováním ve scénářích *vytváření* . Nefunguje dobře v scénářích úprav, protože vyloučené vlastnosti jsou nastavené na hodnotu null nebo výchozí hodnota místo toho, aby byla ponechána beze změny. Pro obranu před přeúčtováním se doporučuje zobrazit modely namísto atributu `[Bind]`. Další informace najdete v části [Poznámka k zabezpečení týkající se přestavování](xref:data/ef-mvc/crud#security-note-about-overposting).

## <a name="collections"></a>Kolekce

Pro cíle, které jsou kolekcemi jednoduchých typů, vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*. Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony. Příklad:

* Předpokládejme, že parametr, který má být svázán, je pole s názvem `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* Data formuláře nebo řetězce dotazu mohou být v jednom z následujících formátů:
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* Následující formát je podporován pouze v datech formuláře:

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* Pro všechny předchozí ukázkové formáty předává vazba modelu pole dvou položek do parametru `selectedCourses`:

  * selectedCourses[0]=1050
  * selectedCourses[1]=2000

  Formáty dat, které používají čísla v dolním indexu (... [0]... [1]...) musí se ujistit, že jsou číslovány sekvenčně počínaje nulou. Pokud jsou v číslování dolních indexů nějaké mezery, všechny položky po mezerě se ignorují. Například pokud jsou v dolním indexu 0 a 2 místo 0 a 1, bude druhá položka ignorována.

## <a name="dictionaries"></a>Slovníky

U `Dictionary`ch cílů vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*. Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony. Příklad:

* Předpokládejme, že cílový parametr je `Dictionary<int, string>` s názvem `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* Odeslaná data formuláře nebo řetězce dotazu mohou vypadat jako jeden z následujících příkladů:

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* Pro všechny předchozí ukázkové formáty model vazby předává slovník dvou položek do parametru `selectedCourses`:

  * selectedCourses["1050"]="Chemistry"
  * selectedCourses["2000"]="Economics"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a>Chování globalizace dat trasy vazby modelu a řetězce dotazů

Zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu:

* Považovat hodnoty za invariantní jazykovou verzi.
* Je očekáváno, že adresy URL jsou invariantní jazykové verze.

Na rozdíl od hodnoty, které pocházejí z dat formuláře, procházejí převod zohledňující jazykovou verzi. To je záměrné, takže adresy URL lze sdílet napříč národními prostředími.

Aby zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu prošly převodem závislým na jazykové verzi:

* Zdědit z <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>
* Kopírování kódu z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) nebo [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)
* Nahraďte [hodnotu jazykové verze](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) předanou konstruktoru zprostředkovatele hodnoty pomocí [CultureInfo. CurrentCulture.](xref:System.Globalization.CultureInfo.CurrentCulture)
* V možnostech MVC nahraďte výchozí továrnu poskytovatele hodnot pomocí nového:

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a>Speciální datové typy

Existují některé speciální datové typy, které mohou vázání modelů zpracovat.

### <a name="iformfile-and-iformfilecollection"></a>IFormFile a IFormFileCollection

Nahraný soubor zahrnutý v požadavku HTTP.  Podporuje se taky `IEnumerable<IFormFile>` pro víc souborů.

### <a name="cancellationtoken"></a>cancellationToken

Slouží k zrušení aktivity v asynchronních řadičích.

### <a name="formcollection"></a>FormCollection

Používá se k načtení všech hodnot z publikovaných dat formuláře.

## <a name="input-formatters"></a>Vstupní formátovací moduly

Data v textu požadavku mohou být v kódu JSON, XML nebo v jiném formátu. Pro analýzu těchto dat používá vazba modelu *vstupní formátovací modul* , který je nakonfigurován pro zpracování konkrétního typu obsahu. Ve výchozím nastavení ASP.NET Core zahrnuje vstupní formátovací moduly založené na formátu JSON pro zpracování dat JSON. Můžete přidat další formátovací moduly pro ostatní typy obsahu.

ASP.NET Core vybere vstupní formátovací modul založený na atributu [spotřebes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) . Pokud není přítomen žádný atribut, používá [hlavičku Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).

Chcete-li použít předdefinované vstupní formátovací moduly XML:

* Nainstalujte balíček NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.

* V `Startup.ConfigureServices`volejte <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> nebo <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* Použijte atribut `Consumes` na třídy kontroleru nebo metody akcí, které by měly v textu požadavku očekávat XML.

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  Další informace najdete v tématu [představení XML serializace](/dotnet/standard/serialization/introducing-xml-serialization).

### <a name="customize-model-binding-with-input-formatters"></a>Přizpůsobení vazby modelu pomocí vstupních formátovacích prvků

Vstupní formátovací modul má plnou zodpovědnost za čtení dat z textu žádosti. Chcete-li tento proces přizpůsobit, nakonfigurujte rozhraní API používaná vstupním formátovacím modulem. Tato část popisuje, jak upravit vstupní formátovací modul založený na `System.Text.Json`pro pochopení vlastního typu s názvem `ObjectId`. 

Vezměte v úvahu následující model, který obsahuje vlastní vlastnost `ObjectId` s názvem `Id`:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

Chcete-li přizpůsobit proces vazby modelu při použití `System.Text.Json`, vytvořte třídu odvozenou z <xref:System.Text.Json.Serialization.JsonConverter%601>:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

Chcete-li použít vlastní převaděč, použijte atribut <xref:System.Text.Json.Serialization.JsonConverterAttribute> pro typ. V následujícím příkladu je typ `ObjectId` nakonfigurovaný pomocí `ObjectIdConverter` jako svůj vlastní převaděč:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

Další informace najdete v tématu [jak psát vlastní převaděče](/dotnet/standard/serialization/system-text-json-converters-how-to).

## <a name="exclude-specified-types-from-model-binding"></a>Vyloučit zadané typy z vazby modelu

Chování vazeb modelů a ověřovacích systémů řídí [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata). `ModelMetadata` můžete přizpůsobit přidáním poskytovatele podrobností do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders). Předdefinovaná poskytovatelé podrobností jsou k dispozici pro zakázání vazby modelu nebo ověření pro zadané typy.

Chcete-li zakázat vazbu modelu u všech modelů zadaného typu, přidejte do `Startup.ConfigureServices`<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>. Například pro zákaz vazby modelu u všech modelů typu `System.Version`:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

Chcete-li zakázat ověřování vlastností zadaného typu, přidejte do `Startup.ConfigureServices`<xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>. Chcete-li například zakázat ověřování vlastností typu `System.Guid`:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a>Vlastní pořadače modelů

Vazbu modelu můžete roztáhnout tak, že napíšete vlastní pořadač modelů a pomocí atributu `[ModelBinder]` ho vyberete pro daný cíl. Přečtěte si další informace o [vazbě vlastního modelu](xref:mvc/advanced/custom-model-binding).

## <a name="manual-model-binding"></a>Ruční vazba modelu 

Vazbu modelu lze vyvolat ručně pomocí metody <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>. Metoda je definována v obou třídách `ControllerBase` i `PageModel`. Přetížení metody umožňují určit poskytovatele předpony a hodnoty, které se mají použít. Metoda vrátí `false`, pokud se vazba modelu nezdařila. Tady je příklad:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> používá zprostředkovatele hodnot k získávání dat z textu formuláře, řetězce dotazu a dat směrování. `TryUpdateModelAsync` je obvykle: 

* Používá se s aplikacemi Razor Pages a MVC k tomu, aby se zabránilo převzetí služeb při selhání pomocí řadičů a zobrazení.
* Nepoužívá se s webovým rozhraním API, pokud se nevyužívá pro data formulářů, řetězce dotazů a data směrování. Koncové body webového rozhraní API, které využívají [Formátovací moduly vstupu](#input-formatters) JSON k deserializaci těla požadavku do objektu.

Další informace najdete v tématu [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).

## <a name="fromservices-attribute"></a>[FromServices] – atribut

Název tohoto atributu se řídí vzorem atributů vazby modelu, které určují zdroj dat. Nejedná se ale o vazbu dat od poskytovatele hodnot. Získává instanci typu z kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) . Jeho účelem je poskytnout alternativu k injektáže konstruktoru, pokud potřebujete službu pouze v případě, že je volána konkrétní metoda.

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

Tento článek vysvětluje, co je vazba modelů, jak funguje a jak přizpůsobit její chování.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).

## <a name="what-is-model-binding"></a>Co je vazba modelu

Řadiče a stránky Razor fungují s daty, která pocházejí z požadavků HTTP. Například data směrování můžou poskytovat klíč záznamu a pole odeslaných formulářů můžou poskytovat hodnoty pro vlastnosti modelu. Psaní kódu pro načtení každé z těchto hodnot a jejich převod z řetězců na typy .NET by byly únavné a náchylné k chybám. Vazba modelu automatizuje tento proces. Systém vazby modelů:

* Načte data z různých zdrojů, jako jsou například data směrování, pole formuláře a řetězce dotazů.
* Poskytuje data pro řadiče a stránky Razor v parametrech metod a veřejných vlastnostech.
* Převádí řetězcová data na typy .NET.
* Aktualizuje vlastnosti komplexních typů.

## <a name="example"></a>Příklad

Předpokládejme, že máte následující metodu akce:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

A aplikace obdrží požadavek s touto adresou URL:

```
http://contoso.com/api/pets/2?DogsOnly=true
```

Vazba modelu projde následujícím postupem a poté, co systém směrování vybere metodu akce:

* Najde první parametr `GetByID`, celé číslo s názvem `id`.
* Vyhledá z dostupných zdrojů v požadavku HTTP a v datech směrování najde `id` = "2".
* Převede řetězec "2" na celé číslo 2.
* Najde další parametr `GetByID`, logická hodnota s názvem `dogsOnly`.
* Prohledá zdroje a v řetězci dotazu vyhledá "DogsOnly = true". U porovnávání názvů se nerozlišují malá a velká písmena.
* Převede řetězec "true" na Boolean `true`.

Rozhraní potom zavolá metodu `GetById`, předává 2 pro parametr `id` a `true` pro parametr `dogsOnly`.

V předchozím příkladu jsou cíle vazby modelů parametry metod, které jsou jednoduché typy. Cíle mohou být také vlastnostmi komplexního typu. Po úspěšném vytvoření vazby každé vlastnosti dojde k [ověření modelu](xref:mvc/models/validation) pro danou vlastnost. Záznam o tom, jaká data jsou vázána na model a všechny chyby vazby nebo ověřování, jsou uloženy v [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) nebo [PageModel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState). Chcete-li zjistit, zda byl tento proces úspěšný, aplikace zkontroluje příznak [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .

## <a name="targets"></a>Cíle

Vazba modelu se pokusí najít hodnoty pro následující typy cílů:

* Parametry metody akce kontroleru, na kterou je směrován požadavek
* Parametry metody obslužné rutiny Razor Pages, na kterou je směrován požadavek. 
* Veřejné vlastnosti řadiče nebo třídy `PageModel`, pokud jsou určeny atributy.

### <a name="bindproperty-attribute"></a>[BindProperty] – atribut

Dá se použít na veřejnou vlastnost kontroleru nebo `PageModel` třídy, aby vazba modelu mohla cílit na tuto vlastnost:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a>[BindProperties] – atribut

K dispozici v ASP.NET Core 2,1 a novějším.  Dá se použít na kontrolér nebo `PageModel` třídu pro oznámení, že vazba modelu cílí na všechny veřejné vlastnosti třídy:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>Vazba modelu pro požadavky HTTP GET

Ve výchozím nastavení nejsou vlastnosti pro požadavky HTTP GET vázané. Obvykle stačí pro požadavek GET parametr ID záznamu. ID záznamu slouží k vyhledání položky v databázi. Proto není nutné navazovat vlastnost, která obsahuje instanci modelu. Ve scénářích, kdy chcete vlastnosti navázané na data z požadavků GET, nastavte vlastnost `SupportsGet` na `true`:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>Zdroje

Ve výchozím nastavení ve vazbě modelu získává data ve formě párů klíč-hodnota z následujících zdrojů v požadavku HTTP:

1. Pole formuláře
1. Tělo žádosti (pro [řadiče, které mají atribut [ApiController]](xref:web-api/index#binding-source-parameter-inference).)
1. Směrování dat
1. Parametry řetězce dotazu
1. Nahrané soubory

Pro každý cílový parametr nebo vlastnost jsou zdroje prohledávány v pořadí uvedeném v předchozím seznamu. Existuje několik výjimek:

* Data směrování a hodnoty řetězce dotazu jsou používány pouze pro jednoduché typy.
* Nahrané soubory jsou vázány pouze na cílové typy, které implementují `IFormFile` nebo `IEnumerable<IFormFile>`.

Pokud výchozí zdroj není správný, použijte k určení zdroje jeden z následujících atributů:

* [`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) – načte hodnoty z řetězce dotazu. 
* [`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) – načte hodnoty z dat směrování.
* [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) – Získá hodnoty z publikovaných polí formuláře.
* [`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) – načte hodnoty z textu žádosti.
* [`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) – Získá hodnoty z hlaviček protokolu HTTP.

Tyto atributy:

* Jsou přidány do vlastností modelu jednotlivě (nikoli do třídy modelu), jako v následujícím příkladu:

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* Volitelně akceptuje hodnotu názvu modelu v konstruktoru. Tato možnost je k dispozici v případě, že se název vlastnosti neshoduje s hodnotou v požadavku. Například hodnota v požadavku může být záhlavím s pomlčkou v názvu, jako v následujícím příkladu:

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>[FromBody] – atribut

Použijte atribut `[FromBody]` pro parametr k naplnění vlastností z těla požadavku HTTP. Modul runtime ASP.NET Core deleguje zodpovědnost za čtení těla na vstupní formátovací modul. Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).

Při použití `[FromBody]` pro parametr komplexního typu se všechny zdrojové atributy vazby použité na jeho vlastnosti ignorují. Například následující akce `Create` určuje, že `pet` parametr je vyplněný z těla:

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

Třída `Pet` určuje, že jeho vlastnost `Breed` je naplněná z parametru řetězce dotazu:

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

V předchozím příkladu:

* Atribut `[FromQuery]` je ignorován.
* Vlastnost `Breed` není naplněna z parametru řetězce dotazu. 

Formátovací moduly vstupu čtou pouze tělo a nerozumí vazbě zdrojových atributů. Pokud se v těle najde vhodná hodnota, použije se tato hodnota k naplnění vlastnosti `Breed`.

Nepoužívejte `[FromBody]` pro více než jeden parametr na metodu Action. Jakmile je datový proud požadavku čten vstupním formátovacím modulem, již není nadále k dispozici pro vázání dalších parametrů `[FromBody]`.

### <a name="additional-sources"></a>Další zdroje

Zdrojová data jsou k dispozici pro systém vázání modelů podle *zprostředkovatelů hodnot*. Můžete zapisovat a registrovat vlastní poskytovatele hodnot, který získá data pro vazbu modelu z jiných zdrojů. Například můžete chtít data ze souborů cookie nebo stavu relace. Načtení dat z nového zdroje:

* Vytvořte třídu, která implementuje `IValueProvider`.
* Vytvořte třídu, která implementuje `IValueProviderFactory`.
* Zaregistrujte třídu factory v `Startup.ConfigureServices`.

Ukázková aplikace obsahuje [poskytovatele hodnot](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) a [výrobní](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) příklad, který získává hodnoty z souborů cookie. Zde je registrační kód v `Startup.ConfigureServices`:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

Zobrazený kód vloží zprostředkovatele vlastních hodnot po všech vestavěných poskytovatelích hodnot.  Chcete-li jej v seznamu nastavit jako první, zavolejte místo `Add``Insert(0, new CookieValueProviderFactory())`.

## <a name="no-source-for-a-model-property"></a>Žádný zdroj pro vlastnost modelu

Ve výchozím nastavení se chyba stavu modelu nevytvoří, pokud se pro vlastnost modelu nenajde žádná hodnota. Vlastnost je nastavena na hodnotu null nebo na výchozí hodnotu:

* Jednoduché typy s možnou hodnotou null jsou nastavené na `null`.
* Typy hodnot, které nejsou null, jsou nastaveny na `default(T)`. Například parametr `int id` je nastaven na hodnotu 0.
* Pro komplexní typy vazba modelu vytvoří instanci pomocí výchozího konstruktoru bez nastavení vlastností.
* Pole jsou nastavena na `Array.Empty<T>()`, s tím rozdílem, že `byte[]` pole jsou nastavena na `null`.

Pokud má být stav modelu neplatný, pokud se v polích formuláře pro vlastnost modelu nenalezne žádné, použijte atribut [`[BindRequired]`](#bindrequired-attribute) .

Všimněte si, že toto chování `[BindRequired]` se vztahuje na vazbu modelu z publikovaných dat formuláře, nikoli na data JSON nebo XML v těle požadavku. Data těla žádosti jsou zpracována [vstupními formátovacími](#input-formatters)moduly.

## <a name="type-conversion-errors"></a>Chyby konverze typu

Pokud je zdroj nalezen, ale nelze jej převést na cílový typ, stav modelu je označen jako neplatný. Parametr Target nebo Property je nastaven na hodnotu null nebo na výchozí hodnotu, jak je uvedeno v předchozí části.

V kontroleru rozhraní API, který má atribut `[ApiController]`, má neplatný stav modelu za následek automatickou odpověď HTTP 400.

Na stránce Razor znovu zobrazte stránku s chybovou zprávou:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

Ověřování na straně klienta zachytí nejvíc chybných dat, která by jinak byla odeslána do Razor Pages formuláře. Díky tomuto ověření je obtížné aktivovat předchozí zvýrazněný kód. Ukázková aplikace obsahuje tlačítko **Odeslat s neplatným datem** , které do pole **Datum přijetí** vloží nesprávná data a formulář odešle. Toto tlačítko ukazuje, jak kód pro zobrazení stránky funguje, když dojde k chybám převodu dat.

V případě, že je stránka znovu zobrazena v předchozím kódu, není v poli formuláře zobrazen neplatný vstup. Důvodem je to, že vlastnost modelu byla nastavena na hodnotu null nebo na výchozí hodnotu. V chybové zprávě se zobrazí neplatný vstup. Pokud ale chcete, aby se v poli formuláře znovu zobrazila chybná data, je vhodné vytvořit řetězec vlastnosti modelu a provést převod dat ručně.

Pokud nechcete, aby se chyby převodu typů způsobily při chybách stavu modelu, doporučujeme stejnou strategii. V takovém případě nastavte vlastnost model na řetězec.

## <a name="simple-types"></a>Jednoduché typy

Jednoduché typy, které modelový pořadač může převést na zdrojové řetězce, do zahrnují následující:

* [Datového](xref:System.ComponentModel.BooleanConverter)
* [Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)
* [Char](xref:System.ComponentModel.CharConverter)
* [Hodnotu](xref:System.ComponentModel.DateTimeConverter)
* [DateTimeOffset](xref:System.ComponentModel.DateTimeOffsetConverter)
* [Notaci](xref:System.ComponentModel.DecimalConverter)
* [Klepat](xref:System.ComponentModel.DoubleConverter)
* [Vytváření](xref:System.ComponentModel.EnumConverter)
* [Hlavních](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)
* [Konkrétní](xref:System.ComponentModel.SingleConverter)
* [TimeSpan](xref:System.ComponentModel.TimeSpanConverter)
* [UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)
* [Identifikátor URI](xref:System.UriTypeConverter)
* [Verze](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a>Komplexní typy

Aby bylo možné vytvořit vazby komplexního typu, musí mít veřejný výchozí konstruktor a veřejné vlastnosti s možností zápisu. Když dojde k vazbě modelu, instance třídy je vytvořena pomocí veřejného výchozího konstruktoru. 

Pro každou vlastnost komplexního typu vyhledá vazba modelu ve zdrojích *předponu vzoru názvu. property_name*. Pokud není nic nalezeno, vyhledá pouze *Property_Name* bez předpony.

Pro svázání s parametrem je předpona názvem parametru. Pro vazbu na veřejnou vlastnost `PageModel` je předpona název veřejné vlastnosti. Některé atributy mají vlastnost `Prefix`, která umožňuje přepsat výchozí použití parametru nebo názvu vlastnosti.

Předpokládejme například, že komplexní typ je následující třída `Instructor`:

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a>Prefix = název parametru

Pokud je model, který má být svázán, parametr s názvem `instructorToUpdate`:

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

Vazba na model začíná prohledáním zdrojů pro klíčovou `instructorToUpdate.ID`. Pokud se nenajde, vyhledá `ID` bez předpony.

### <a name="prefix--property-name"></a>Prefix = název vlastnosti

Pokud je model, který chcete svázat, vlastnost s názvem `Instructor` kontroleru nebo `PageModel` třídy:

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

Vazba na model začíná prohledáním zdrojů pro klíčovou `Instructor.ID`. Pokud se nenajde, vyhledá `ID` bez předpony.

### <a name="custom-prefix"></a>Vlastní předpona

Pokud je model, který má být svázán, parametr pojmenovaný `instructorToUpdate` a atribut `Bind` určuje `Instructor` jako předponu:

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

Vazba na model začíná prohledáním zdrojů pro klíčovou `Instructor.ID`. Pokud se nenajde, vyhledá `ID` bez předpony.

### <a name="attributes-for-complex-type-targets"></a>Atributy pro cíle komplexního typu

K dispozici je několik předdefinovaných atributů pro řízení vazeb modelu komplexních typů:

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> Tyto atributy ovlivňují vazbu modelu, když jsou publikovaná data formuláře zdrojem hodnot. Neovlivňují vstupní formátovací moduly, které zpracovávají odeslané texty JSON a XML požadavku. Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).
>
> Viz také diskuze o atributu `[Required]` v [ověřování modelu](xref:mvc/models/validation#required-attribute).

### <a name="bindrequired-attribute"></a>[BindRequired] – atribut

Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody. Způsobí, že vazba modelu přidá chybu stavu modelu, pokud pro vlastnost modelu neproběhne vazba. Tady je příklad:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a>[BindNever] – atribut

Dá se použít jenom pro vlastnosti modelu, nikoli na parametry metody. Zabraňuje vazbě modelu v nastavení vlastnosti modelu. Tady je příklad:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a>[BIND] – atribut

Lze použít pro třídu nebo parametr metody. Určuje, které vlastnosti modelu by měly být zahrnuty ve vazbě modelu.

V následujícím příkladu jsou při volání jakékoli obslužné rutiny nebo metody akce vázány pouze zadané vlastnosti `Instructor` modelu:

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

V následujícím příkladu jsou při volání metody `OnPost` vázány pouze zadané vlastnosti `Instructor`ho modelu:

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

Atribut `[Bind]` lze použít k ochraně před přeúčtováním ve scénářích *vytváření* . Nefunguje dobře v scénářích úprav, protože vyloučené vlastnosti jsou nastavené na hodnotu null nebo výchozí hodnota místo toho, aby byla ponechána beze změny. Pro obranu před přeúčtováním se doporučuje zobrazit modely namísto atributu `[Bind]`. Další informace najdete v části [Poznámka k zabezpečení týkající se přestavování](xref:data/ef-mvc/crud#security-note-about-overposting).

## <a name="collections"></a>Kolekce

Pro cíle, které jsou kolekcemi jednoduchých typů, vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*. Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony. Příklad:

* Předpokládejme, že parametr, který má být svázán, je pole s názvem `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* Data formuláře nebo řetězce dotazu mohou být v jednom z následujících formátů:
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* Následující formát je podporován pouze v datech formuláře:

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* Pro všechny předchozí ukázkové formáty předává vazba modelu pole dvou položek do parametru `selectedCourses`:

  * selectedCourses[0]=1050
  * selectedCourses[1]=2000

  Formáty dat, které používají čísla v dolním indexu (... [0]... [1]...) musí se ujistit, že jsou číslovány sekvenčně počínaje nulou. Pokud jsou v číslování dolních indexů nějaké mezery, všechny položky po mezerě se ignorují. Například pokud jsou v dolním indexu 0 a 2 místo 0 a 1, bude druhá položka ignorována.

## <a name="dictionaries"></a>Slovníky

U `Dictionary`ch cílů vyhledá vazba modelu shody pro *parameter_name* nebo *Property_Name*. Pokud se nenajde žádná shoda, vyhledá jeden z podporovaných formátů bez předpony. Příklad:

* Předpokládejme, že cílový parametr je `Dictionary<int, string>` s názvem `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* Odeslaná data formuláře nebo řetězce dotazu mohou vypadat jako jeden z následujících příkladů:

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* Pro všechny předchozí ukázkové formáty model vazby předává slovník dvou položek do parametru `selectedCourses`:

  * selectedCourses["1050"]="Chemistry"
  * selectedCourses["2000"]="Economics"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a>Chování globalizace dat trasy vazby modelu a řetězce dotazů

Zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu:

* Považovat hodnoty za invariantní jazykovou verzi.
* Je očekáváno, že adresy URL jsou invariantní jazykové verze.

Na rozdíl od hodnoty, které pocházejí z dat formuláře, procházejí převod zohledňující jazykovou verzi. To je záměrné, takže adresy URL lze sdílet napříč národními prostředími.

Aby zprostředkovatel hodnoty trasy ASP.NET Core a zprostředkovatel hodnoty řetězce dotazu prošly převodem závislým na jazykové verzi:

* Zdědit z <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>
* Kopírování kódu z [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) nebo [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)
* Nahraďte [hodnotu jazykové verze](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) předanou konstruktoru zprostředkovatele hodnoty pomocí [CultureInfo. CurrentCulture.](xref:System.Globalization.CultureInfo.CurrentCulture)
* V možnostech MVC nahraďte výchozí továrnu poskytovatele hodnot pomocí nového:

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a>Speciální datové typy

Existují některé speciální datové typy, které mohou vázání modelů zpracovat.

### <a name="iformfile-and-iformfilecollection"></a>IFormFile a IFormFileCollection

Nahraný soubor zahrnutý v požadavku HTTP.  Podporuje se taky `IEnumerable<IFormFile>` pro víc souborů.

### <a name="cancellationtoken"></a>cancellationToken

Slouží k zrušení aktivity v asynchronních řadičích.

### <a name="formcollection"></a>FormCollection

Používá se k načtení všech hodnot z publikovaných dat formuláře.

## <a name="input-formatters"></a>Vstupní formátovací moduly

Data v textu požadavku mohou být v kódu JSON, XML nebo v jiném formátu. Pro analýzu těchto dat používá vazba modelu *vstupní formátovací modul* , který je nakonfigurován pro zpracování konkrétního typu obsahu. Ve výchozím nastavení ASP.NET Core zahrnuje vstupní formátovací moduly založené na formátu JSON pro zpracování dat JSON. Můžete přidat další formátovací moduly pro ostatní typy obsahu.

ASP.NET Core vybere vstupní formátovací modul založený na atributu [spotřebes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) . Pokud není přítomen žádný atribut, používá [hlavičku Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).

Chcete-li použít předdefinované vstupní formátovací moduly XML:

* Nainstalujte balíček NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.

* V `Startup.ConfigureServices`volejte <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> nebo <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* Použijte atribut `Consumes` na třídy kontroleru nebo metody akcí, které by měly v textu požadavku očekávat XML.

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  Další informace najdete v tématu [představení XML serializace](/dotnet/standard/serialization/introducing-xml-serialization).

## <a name="exclude-specified-types-from-model-binding"></a>Vyloučit zadané typy z vazby modelu

Chování vazeb modelů a ověřovacích systémů řídí [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata). `ModelMetadata` můžete přizpůsobit přidáním poskytovatele podrobností do [MvcOptions. ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders). Předdefinovaná poskytovatelé podrobností jsou k dispozici pro zakázání vazby modelu nebo ověření pro zadané typy.

Chcete-li zakázat vazbu modelu u všech modelů zadaného typu, přidejte do `Startup.ConfigureServices`<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>. Například pro zákaz vazby modelu u všech modelů typu `System.Version`:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

Chcete-li zakázat ověřování vlastností zadaného typu, přidejte do `Startup.ConfigureServices`<xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>. Chcete-li například zakázat ověřování vlastností typu `System.Guid`:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a>Vlastní pořadače modelů

Vazbu modelu můžete roztáhnout tak, že napíšete vlastní pořadač modelů a pomocí atributu `[ModelBinder]` ho vyberete pro daný cíl. Přečtěte si další informace o [vazbě vlastního modelu](xref:mvc/advanced/custom-model-binding).

## <a name="manual-model-binding"></a>Ruční vazba modelu

Vazbu modelu lze vyvolat ručně pomocí metody <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>. Metoda je definována v obou třídách `ControllerBase` i `PageModel`. Přetížení metody umožňují určit poskytovatele předpony a hodnoty, které se mají použít. Metoda vrátí `false`, pokud se vazba modelu nezdařila. Tady je příklad:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a>[FromServices] – atribut

Název tohoto atributu se řídí vzorem atributů vazby modelu, které určují zdroj dat. Nejedná se ale o vazbu dat od poskytovatele hodnot. Získává instanci typu z kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) . Jeho účelem je poskytnout alternativu k injektáže konstruktoru, pokud potřebujete službu pouze v případě, že je volána konkrétní metoda.

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
