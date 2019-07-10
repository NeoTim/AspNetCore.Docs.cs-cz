---
title: Vazby modelu v ASP.NET Core
author: tdykstra
description: Zjistěte, jak vazby modelu v ASP.NET Core funguje a jak můžete přizpůsobit její chování.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: tdykstra
ms.date: 05/31/2019
uid: mvc/models/model-binding
ms.openlocfilehash: 10a9f8327bf16d11ec1e04ac3888d701f1ab1778
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724527"
---
# <a name="model-binding-in-aspnet-core"></a>Vazby modelu v ASP.NET Core

Tento článek vysvětluje, jaké vazby modelu je, jak to funguje a jak přizpůsobit chování aplikace.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([stažení](xref:index#how-to-download-a-sample)).

## <a name="what-is-model-binding"></a>Co je vazby modelu

Řadiče a stránky Razor pracovat s daty, která pochází z požadavků HTTP. Například data trasy, která může poskytnout klíč záznamu a pole odeslaného formuláře může poskytnout hodnoty pro vlastnosti modelu. Zápis kódu pro každou z těchto hodnot načíst a je převést na typy .NET z řetězců může být zdlouhavé a náchylné k chybě. Tento proces automatizuje vazby modelu. Systém vazby modelu:

* Načte data z různých zdrojů, jako je směrování dat, pole formuláře a řetězce dotazu.
* Poskytuje data, která mají řadiče a stránek Razor parametry metody a veřejné vlastnosti.
* Převede řetězec data na typy .NET.
* Aktualizuje vlastnosti komplexních typů.

## <a name="example"></a>Příklad

Předpokládejme, že máte následující metody akce:

[!code-csharp[](model-binding/samples/2.x/Controllers/PetsController.cs?name=snippet_DogsOnly)]

A aplikace obdrží žádost o tuto adresu URL:

```
http://contoso.com/api/pets/2?DogsOnly=true
```

Vazby modelu přejde ale takto po směrování systému vybere metodu akce:

* Najde první parametr `GetByID`, celočíselným parametrem s názvem `id`.
* Prohlédne dostupné zdroje v požadavku HTTP a vyhledá `id` = "2" v datech trasy.
* Převede řetězec "2" do celého čísla 2.
* Najde další parametr `GetByID`, logickou hodnotu s názvem `dogsOnly`.
* Prohlédne zdroje a vyhledá "DogsOnly = true" v řetězci dotazu. Shoda názvu není malá a velká písmena.
* Převede řetězec "true" na logickou hodnotu `true`.

Pak zavolá rozhraní `GetById` metodu 2 pro `id` parametr, a `true` pro `dogsOnly` parametru.

V předchozím příkladu cíle vazby modelu jsou parametry metody, které jsou jednoduché typy. Cíle může být také vlastností komplexního typu. Po každé vlastnosti je úspěšně navázán, [ověření modelu](xref:mvc/models/validation) dojde k této vlastnosti. Požadovaný záznam, jaká data je vázán na model a všechny chyby ověření nebo vazba je uložena v [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) nebo [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState). Chcete-li zjistit, pokud tento proces byl úspěšný, je aplikace zkontroluje [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) příznak.

## <a name="targets"></a>Cíle

Vazby modelu se pokusí najít hodnoty pro následující typy cíle:

* Parametry metody akce kontroleru, který požadavek prochází, k.
* Parametry metody obslužné rutiny pro stránky Razor, který požadavek prochází, k. 
* Veřejné vlastnosti kontroleru nebo `PageModel` třídy, pokud zadané atributy.

### <a name="bindproperty-attribute"></a>Atribut [BindProperty]

Můžete použít pro veřejnou vlastnost řadiče nebo `PageModel` třídy způsobit vazby modelu pro tuto vlastnost cíle:

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=7-8)]

### <a name="bindpropertiesattribute"></a>Atribut [BindProperties]

K dispozici v ASP.NET Core 2.1 nebo novější.  Můžete použít k řadiči nebo `PageModel` třídy říct vazby modelu cílit na všechny veřejné vlastnosti třídy:

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>Vazby pro požadavky HTTP GET modelu

Ve výchozím nastavení nejsou vázané vlastnosti pro požadavky HTTP GET. Obvykle je vše, co potřebujete pro požadavek GET parametr ID záznamu. ID záznamu slouží k vyhledání položky v databázi. Proto není nutné vytvořit vazbu vlastnosti, která obsahuje instance modelu. Ve scénářích, kde má vlastnosti, které jsou vázány na data z požadavků GET, nastavte `SupportsGet` vlastnost `true`:

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>Zdroje

Ve výchozím nastavení vazby modelu získá data ve formě dvojic klíč / hodnota z následujících zdrojů v jednom požadavku HTTP:

1. Pole formuláře 
1. Text požadavku (pro [řadiči, které mají atribut [objektu ApiController]](xref:web-api/index#binding-source-parameter-inference).)
1. Data trasy
1. Parametry řetězce dotazu
1. Nahrané soubory 

Pro každý cílový parametr nebo vlastnost zdroje jsou prohledávány v uvedeném pořadí v tomto seznamu. Existuje pár výjimek:

* Směrování dat a dotazu řetězcové hodnoty se používají pouze pro jednoduché typy.
* Nahrané soubory jsou propojeny pouze cílové typy, které implementují `IFormFile` nebo `IEnumerable<IFormFile>`.

Pokud výchozí chování nedává správných výsledků, můžete použít jednu z následujících atributů k určení zdroje použít pro libovolný daný cíl. 

* [[FromQuery] ](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Získá hodnoty z řetězce dotazu. 
* [[FromRoute] ](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Získá hodnoty z dat trasy.
* [[FromForm] ](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Získá hodnoty z polí odeslaného formuláře.
* [[FromBody] ](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Získá hodnoty z textu požadavku.
* [[FromHeader] ](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Získá hodnoty z hlavičky protokolu HTTP.

Tyto atributy:

* Jsou přidány do vlastnosti modelu jednotlivě (ne na třídu modelu), jako v následujícím příkladu:

  [!code-csharp[](model-binding/samples/2.x/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* Volitelně můžete přijměte hodnotu názvu modelu v konstruktoru. Tato možnost je k dispozici v případě, že název vlastnosti neodpovídá hodnotě v identifikátoru požadavku. Například může být hodnota v požadavku hlavička s pomlčkou ve svém názvu, jako v následujícím příkladu:

  [!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>Atribut [FromBody]

Data těla požadavku je analyzován pomocí vstupní formátování specifické pro typ obsahu požadavku. Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).

Nemůžete použít `[FromBody]` k více než jeden parametr na metodu akce. Modul runtime ASP.NET Core deleguje na starost čtení datový proud požadavku na vstupní formátovacího modulu. Jakmile je datový proud požadavku pro čtení, již není k dispozici ke čtení pro druhé vazby `[FromBody]` parametry.

### <a name="additional-sources"></a>Další zdroje

Zdroj dat je určena k systému vazby modelu *hodnota poskytovatelé*. Můžete napsat a registraci zprostředkovatele vlastního hodnot, které získávají data pro vazbu modelu z jiných zdrojů. Například můžete data ze souborů cookie nebo stav relace. Pokud chcete získat data z nového zdroje:

* Vytvořte třídu, která implementuje `IValueProvider`.
* Vytvořte třídu, která implementuje `IValueProviderFactory`.
* Registrovat třídu objektů factory v `Startup.ConfigureServices`.

Obsahuje ukázkovou aplikaci [zprostředkovatele hodnot](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) a [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) příklad, který získá hodnoty z soubory cookie. Zde je registrační kód v `Startup.ConfigureServices`:

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=3)]

Kód zobrazený vloží zprostředkovatele vlastní hodnoty koneckonců zprostředkovatele předdefinovaných hodnot.  Aby byla první v seznamu, volání `Insert(0, new CookieValueProviderFactory())` místo `Add`.

## <a name="no-source-for-a-model-property"></a>Žádný zdroj pro vlastnosti modelu

Ve výchozím nastavení se nevytvoří chyby stavu modelu, pokud není nalezena žádná hodnota pro vlastnosti modelu. Vlastnost je nastavena na hodnotu null nebo výchozí hodnotu:

* Jednoduché typy s možnou hodnotou Null jsou nastaveny na `null`.
* Typy hodnot neumožňující jsou nastaveny na `default(T)`. Například parametr `int id` je nastavena na hodnotu 0.
* Vazby modelu pro komplexní typy, vytvoří instanci pomocí výchozího konstruktoru, aniž byste museli nastavovat vlastnosti.
* Pole jsou nastaveny na `Array.Empty<T>()`, s tím rozdílem, že `byte[]` pole jsou nastaveny na `null`.

Pokud stav modelu by měl zrušit, když nelze najít v polích formuláře pro vlastnosti modelu, použijte [atribut [BindRequired]](#bindrequired-attribute).

Všimněte si, že tento `[BindRequired]` platí chování vazby modelu z odeslaného formuláře dat, nikoli data XML nebo JSON v textu požadavku. Data těla požadavku se zpracovává souborem [vstup formátovací moduly](#input-formatters).

## <a name="type-conversion-errors"></a>Chyby převodu typů

Pokud zdroj nenajde, ale nelze převést na typ cíle, stav modelu označen jako neplatný. Cílový parametr nebo vlastnost je nastavena na hodnotu null nebo výchozí hodnotu, jak je uvedeno v předchozí části.

V kontroler API, která má `[ApiController]` atribut, výsledné stavy modelu je neplatný v automatické odpovědi HTTP 400.

Na stránce Razor opětovné zobrazení na stránce s chybovou zprávou:

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

Ověřování na straně klienta zachycuje většinu chybnými daty, které by jinak odeslána do formuláře pro stránky Razor. Toto ověření je obtížné aktivovat předchozí zvýrazněný kód. Obsahuje ukázkovou aplikaci **odeslat pomocí neplatné datum** tlačítko, které umístí chybnými daty **datum přijetí** pole a formulář odešle. Toto tlačítko ukazuje, jak kód pro opětovné zobrazení na stránce funguje při výskytu chyby převodu dat.

Pokud na stránce se zobrazí znovu v předchozím kódu, neplatný vstup nezobrazuje pole formuláře. Je to proto, že vlastnost modelu byla nastavena na hodnotu null nebo výchozí hodnotu. Neplatný vstup nezobrazí v chybové zprávě. Pokud ale chcete chybná data v poli formuláře znovu, zvažte provedení vlastnost modelu řetězec a udělat převod dat ručně.

Stejné strategie se doporučuje, pokud nechcete, aby chyby převodu typu za následek chyby stavu modelu. V takovém případě zkontrolujte vlastnost modelu řetězec.

## <a name="simple-types"></a>Jednoduché typy

Jednoduché typy, které vazače modelu můžete převést do zdrojové řetězce patří:

* [Datový typ Boolean](xref:System.ComponentModel.BooleanConverter)
* [Bajtů](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)
* [Char](xref:System.ComponentModel.CharConverter)
* [Datum a čas](xref:System.ComponentModel.DateTimeConverter)
* [DateTimeOffset](xref:System.ComponentModel.DateTimeOffsetConverter)
* [Decimal](xref:System.ComponentModel.DecimalConverter)
* [Double](xref:System.ComponentModel.DoubleConverter)
* [Výčet](xref:System.ComponentModel.EnumConverter)
* [identifikátor GUID](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)
* [Jeden](xref:System.ComponentModel.SingleConverter)
* [TimeSpan](xref:System.ComponentModel.TimeSpanConverter)
* [UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)
* [Identifikátor URI](xref:System.UriTypeConverter)
* [Verze](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a>Komplexní typy

Komplexní typ musí mít veřejný výchozí konstruktor a veřejný zapisovatelný vlastnosti pro vytvoření vazby. Pokud dojde k vazbě modelu, třída je vytvořen pomocí veřejný výchozí konstruktor. 

Pro každou vlastnost komplexní typ vazby modelu vypadá prostřednictvím zdroje pro vzor názvů *prefix.property_name*. Pokud se nic nenajde, vyhledá pouze *%{Property_Name/* bez předpony.

Předpona pro vazbu parametru, je název parametru. Vazba `PageModel` veřejné vlastnosti, předpona, která je název veřejné vlastnosti. Některé atributy mají `Prefix` vlastnost, která vám umožní potlačit výchozí použití názvu parametru nebo vlastnost.

Předpokládejme například, že komplexní typ je následující `Instructor` třídy:

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a>Předpona = název parametru

Pokud je model vázán parametr s názvem `instructorToUpdate`:

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

Spuštění vazby modelu projitím zdroje pro klíč `instructorToUpdate.ID`. Pokud, který nenajde, vyhledá `ID` bez předpony.

### <a name="prefix--property-name"></a>Předpona = název vlastnosti

Pokud je model vázán vlastnost s názvem `Instructor` kontroleru nebo `PageModel` třídy:

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

Spuštění vazby modelu projitím zdroje pro klíč `Instructor.ID`. Pokud, který nenajde, vyhledá `ID` bez předpony.

### <a name="custom-prefix"></a>Vlastní předponu

Pokud je model vázán parametr s názvem `instructorToUpdate` a `Bind` Určuje atribut `Instructor` jako předpona:

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

Spuštění vazby modelu projitím zdroje pro klíč `Instructor.ID`. Pokud, který nenajde, vyhledá `ID` bez předpony.

### <a name="attributes-for-complex-type-targets"></a>Atributy pro komplexní typ cíle

Několik vestavěné atributy jsou k dispozici pro řízení vazby modelu komplexních typů:

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> Tyto atributy ovlivňují model vazby při publikování formuláře je dat zdrojové hodnoty. Neovlivňují vstupní formátovací moduly, které proces publikování textem žádosti ve formátu JSON a XML. Vstupní formátovací moduly jsou vysvětleny [dále v tomto článku](#input-formatters).
>
> Viz také informace o `[Required]` atribut [ověření modelu](xref:mvc/models/validation#required-attribute).

### <a name="bindrequired-attribute"></a>Atribut [BindRequired]

Může používat jedině pro vlastnosti modelu, ne na parametry metod. Způsobí, že vazba modelu přidat chyby stavu modelu, pokud vazba nebyla vytvořena pro vlastnosti modelu. Tady je příklad:

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a>Atribut [BindNever]

Může používat jedině pro vlastnosti modelu, ne na parametry metod. Zabraňuje vazby modelu z nastavení vlastnosti modelu. Tady je příklad:

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a>Atribut [vazby]

Můžete použít pro třídu nebo parametr metody. Určuje vlastnosti modelu, které by měl být součástí vazby modelu.

V následujícím příkladu, pouze zadané vlastnosti `Instructor` modelu jsou vázána při volání metody všechny obslužné rutiny nebo akce:

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

V následujícím příkladu, pouze zadané vlastnosti `Instructor` modelu když jsou vázány `OnPost` volání metody:

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

`[Bind]` Atribut lze použít k ochraně proti overposting v *vytvořit* scénáře. Nebude fungovat dobře ve scénářích upravit, protože vyloučené vlastnosti nastavena na hodnotu null nebo výchozí hodnotu namísto vlevo beze změny. Pro ochranu proti overposting Zobrazit modely doporučujeme místo `[Bind]` atribut. Další informace najdete v tématu [Poznámka k zabezpečení o overposting](xref:data/ef-mvc/crud#security-note-about-overposting).

## <a name="collections"></a>Kolekce

Pro cíle, které jsou kolekce typů jednoduché, vazby modelu hledá odpovídající *parameter_name* nebo *%{Property_Name/* . Pokud není nalezena žádná shoda, hledá se pro jeden z podporovaných formátů bez předpony. Příklad:

* Předpokládejme, že je parametr vázat pole s názvem `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* Řetězcová data formuláře nebo dotazu může být v jednom z následujících formátů:
   
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

* Tento formát je podporován pouze v dat formuláře:

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* Pro všechny předchozí příklad formátuje vazby modelu předá pole dvou položek `selectedCourses` parametr:

  * selectedCourses[0]=1050
  * selectedCourses[1]=2000

  Formáty dat tohoto dolního indexu čísla použití (...) [0]... [1]...) Ujistěte se, že jsou číslovány postupně na nule. Pokud jsou všechny mezery v číslování dolního indexu, jsou ignorovány všechny položky po mezery. Například pokud dolní indexy jsou 0 až 2 místo 0 a 1, druhá položka je ignorována.

## <a name="dictionaries"></a>slovníky

Pro `Dictionary` cíle, vazby modelu hledá odpovídající *parameter_name* nebo *%{Property_Name/* . Pokud není nalezena žádná shoda, hledá se pro jeden z podporovaných formátů bez předpony. Příklad:

* Předpokládejme, že je parametr target `Dictionary<int, string>` s názvem `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* Odeslaná data řetězec formuláře nebo dotazu může vypadat například jeden z následujících příkladů:

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

* Pro všechny předchozí příklad formátuje vazby modelu předá slovník dvě položky `selectedCourses` parametr:

  * selectedCourses["1050"]="Chemistry"
  * selectedCourses["2000"]="Economics"

## <a name="special-data-types"></a>Speciální datové typy

Existují některé speciální datové typy, které dokáže zpracovat vazby modelu.

### <a name="iformfile-and-iformfilecollection"></a>IFormFile a IFormFileCollection

Nahraný soubor zahrnutý v požadavku HTTP.  Je také podporována `IEnumerable<IFormFile>` u více souborů.

### <a name="cancellationtoken"></a>cancellationToken

Použít pro zrušení aktivity v asynchronní řadiče.

### <a name="formcollection"></a>FormCollection

Umožňuje načíst všechny hodnoty z odeslaného formuláře data.

## <a name="input-formatters"></a>Vstupní formátovacích modulů

Data v textu požadavku může být ve formátu JSON, XML nebo jiném formátu. Tato data analyzovat, model vazby používá *vstupní formátování* , který je nakonfigurovaný pro zpracování konkrétního typu obsahu. Ve výchozím nastavení ASP.NET Core zahrnuje formátu JSON vstupní formátovacích modulů pro zpracování dat JSON. Můžete přidat další formátování pro jiné typy obsahu.

ASP.NET Core vybere vstupní formátovacích modulů na základě [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) atribut. Pokud je k dispozici žádný atribut, použije [hlavičku Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).

Použití předdefinované vstupní formátovací moduly XML:

* Nainstalujte `Microsoft.AspNetCore.Mvc.Formatters.Xml` balíček NuGet.

* V `Startup.ConfigureServices`, volání <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> nebo <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.

  [!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* Použít `Consumes` atribut třídy kontroleru nebo metody akce, které by měli počítat datovou část požadavku XML.

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  Další informace najdete v tématu [představení serializace XML](https://docs.microsoft.com/en-us/dotnet/standard/serialization/introducing-xml-serialization).

## <a name="exclude-specified-types-from-model-binding"></a>Vyloučit zadané typy z vazby modelu

V modelu a ověření vazby systémech chování vychází [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata). Můžete přizpůsobit `ModelMetadata` tak, že přidáte podrobnosti zprostředkovatele, aby [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders). Podrobnosti o předdefinovaných zprostředkovatelé jsou k dispozici pro zakázání ověření pro zadané typy nebo vazby modelu.

Chcete-li zakázat vazby modelu zadaného typu všech modelů, přidejte <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> v `Startup.ConfigureServices`. Například s vazbou modelu zakázat na všech modelů typu `System.Version`:

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

Chcete-li zakázat ověřování na vlastnosti zadaného typu, přidejte <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> v `Startup.ConfigureServices`. Například chcete-li zakázat ověřování na vlastnosti typu `System.Guid`:

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a>Vazače modelů vlastní

Vazby modelu můžete rozšířit pomocí zápisu vlastního modelu pořadače a použití `[ModelBinder]` atribut a vyberte pro daný cíl. Další informace o [vlastní vazba modelu](xref:mvc/advanced/custom-model-binding).

## <a name="manual-model-binding"></a>Vazby modelu ruční

Vazby modelu lze vyvolat ručně pomocí <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> metody. Metoda je definována v obou `ControllerBase` a `PageModel` třídy. Metoda přetížení umožňují určit zprostředkovatele předponu a hodnota má být použit. Metoda vrátí `false` selhání vazby modelu. Tady je příklad:

[!code-csharp[](model-binding/samples/2.x/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a>Atribut [FromServices]

Název tohoto atributu má následující formát, které určují zdroj dat atributů vazby modelu. Ale nejedná se o vázání dat z zprostředkovatele hodnot. Získá instanci typu z [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru. Jeho účelem je poskytnout alternativu vůči útoku prostřednictvím injektáže konstruktoru pro, když potřebujete službu pouze v případě, že konkrétní metoda je volána.

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>
