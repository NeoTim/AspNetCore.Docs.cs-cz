---
title: Ověření modelu ve ASP.NET Core MVC
author: rick-anderson
description: Přečtěte si o ověřování modelu ve ASP.NET Core MVC a Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
uid: mvc/models/validation
ms.openlocfilehash: 0e3d4f4705dbfdae00943de2d85c603b6762a2f8
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205888"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a>Ověřování modelu ve ASP.NET Core MVC a Razor Pages

::: moniker range=">= aspnetcore-3.0"

Od [Kirka Larkin](https://github.com/serpent5)

Tento článek vysvětluje, jak ověřit vstup uživatele ve ASP.NET Core MVC nebo v aplikaci Razor Pages.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).

## <a name="model-state"></a>Stav modelu

Stav modelu představuje chyby, které pocházejí ze dvou subsystémů: vazby modelu a ověření modelu. Chyby, které pocházejí z [vazby mezi modely](model-binding.md) , jsou obvykle chyby převodu dat. Například "x" je zadáno v poli typu Integer. K ověření modelu dochází po vazbě modelu a hlášení chyb, kde data neodpovídají obchodním pravidlům. Například hodnota 0 se zadává v poli, které očekává hodnocení mezi 1 a 5.

Před provedením akce kontroleru nebo obslužné rutiny Razor Pages dojde k ověření vazby modelů i k ověřování modelu. U webových aplikací je zodpovědností aplikace vhodné je kontrolovat `ModelState.IsValid` a reagovat. Webové aplikace obvykle znovu zobrazí stránku s chybovou zprávou:

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

Řadiče webového rozhraní API nemusí kontrolovat `ModelState.IsValid` , jestli mají `[ApiController]` atribut. V takovém případě je vrácena Automatická odpověď HTTP 400 obsahující podrobnosti o chybě, pokud stav modelu není platný. Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).

## <a name="rerun-validation"></a>Znovu spustit ověření

Ověřování je automatické, ale můžete je chtít opakovat ručně. Můžete například vypočítat hodnotu pro vlastnost a chtít znovu spustit ověřování po nastavení vlastnosti na vypočítanou hodnotu. Chcete-li znovu spustit ověřování `TryValidateModel` , zavolejte metodu, jak je znázorněno zde:

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a>Atributy ověřování

Atributy ověřování umožňují zadat pravidla ověřování pro vlastnosti modelu. Následující příklad z ukázkové aplikace zobrazuje třídu modelu s poznámkou ověřování atributů. `[ClassicMovie]` Atribut je vlastní ověřovací atribut a jsou integrovány ostatní. Není zobrazeno `[ClassicMovieWithClientValidator]`. `[ClassicMovieWithClientValidator]`ukazuje alternativní způsob implementace vlastního atributu.

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a>Předdefinované atributy

Tady jsou některé z vestavěných ověřovacích atributů:

* `[CreditCard]`: Ověří, zda má vlastnost formát kreditní karty.
* `[Compare]`: Ověří, že se dvě vlastnosti v modelu shodují.
* `[EmailAddress]`: Ověří, zda má vlastnost formát e-mailu.
* `[Phone]`: Ověří, zda má vlastnost formát telefonního čísla.
* `[Range]`: Ověří, že hodnota vlastnosti spadá do zadaného rozsahu.
* `[RegularExpression]`: Ověří, že hodnota vlastnosti odpovídá zadanému regulárnímu výrazu.
* `[Required]`: Ověří, že pole nemá hodnotu null. Podrobnosti o chování tohoto atributu naleznete v [ `[Required]` atributu Attribute](#required-attribute) .
* `[StringLength]`: Ověří, že hodnota vlastnosti řetězce nepřekračuje zadané omezení délky.
* `[Url]`: Ověří, zda má vlastnost formát adresy URL.
* `[Remote]`: Ověří vstup na straně klienta voláním metody Action na serveru. Podrobnosti o chování tohoto atributu naleznete v [ `[Remote]` atributu Attribute](#remote-attribute) .

Úplný seznam ověřovacích atributů najdete v oboru názvů [System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations) .

### <a name="error-messages"></a>Chybové zprávy

Atributy ověřování umožňují zadat chybovou zprávu, která se má zobrazit pro neplatný vstup. Příklad:

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

Interně atributy volají `String.Format` jako zástupný symbol pro název pole a někdy další zástupné symboly. Příklad:

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

Při použití na `Name` vlastnost bude chybová zpráva vytvořená v předchozím kódu "Délka názvu musí být v rozmezí 6 až 8."

Chcete-li zjistit, které parametry jsou `String.Format` předány pro konkrétní chybovou zprávu atributu, přečtěte si [zdrojový kód pro anotace](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).

## <a name="required-attribute"></a>[Required] – atribut

Systém ověřování v .NET Core 3,0 a novějším zpracovává parametry bez hodnoty null nebo vázané vlastnosti, jako kdyby měl `[Required]` atribut. [Typy hodnot](/dotnet/csharp/language-reference/keywords/value-types) , jako `decimal` jsou `int` a, nejsou null. Toto chování je možné zakázat konfigurací <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> v `Startup.ConfigureServices`nástroji:

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a>[Požadováno] ověření na serveru

Na serveru je požadovaná hodnota považována za chybějící, pokud má vlastnost hodnotu null. Pole, které nesmí mít hodnotu null, je vždy platné `[Required]` a chybová zpráva atributu se nikdy nezobrazuje.

Vazba modelu pro vlastnost, která nemůže mít hodnotu null, může selhat, což vede k chybové zprávě, `The value '' is invalid`jako je například. Chcete-li zadat vlastní chybovou zprávu pro ověřování na straně serveru pro typy, které neumožňují hodnotu null, máte následující možnosti:

* Převést pole na hodnotu null (například `decimal?` místo `decimal`). Typy hodnot s [možnou hodnotou null\<>](/dotnet/csharp/programming-guide/nullable-types/) jsou považovány za standardní typy s možnou hodnotou null
* Zadejte výchozí chybovou zprávu, kterou má použít vazba modelu, jak je znázorněno v následujícím příkladu:

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  Další informace o chybách vazeb modelů, které lze nastavit jako výchozí zprávy pro, naleznete <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>v tématu.

### <a name="required-validation-on-the-client"></a>[Požadováno] ověřování na klientovi

Typy a řetězce, které neumožňují hodnotu null, jsou v porovnání s tímto serverem zpracovávány jinak v klientovi. Na klientovi:

* Hodnota se považuje za přítomnou pouze v případě, že je pro ni zadán vstup. Proto ověřování na straně klienta zpracovává typy, které neumožňují hodnotu null, stejné jako typy s možnou hodnotou null.
* Prázdné znaky v poli řetězce se považují za platný vstup metodou jQuery [vyžadované](https://jqueryvalidation.org/required-method/) ověřením. Ověřování na straně serveru považuje požadované pole řetězce za neplatné, pokud je zadána pouze mezera.

Jak bylo uvedeno dříve, typy neumožňující hodnotu null jsou považovány za `[Required]` , i když mají atribut. To znamená, že získáte ověřování na straně klienta i v `[Required]` případě, že atribut nepoužijete. Pokud však atribut nepoužíváte, zobrazí se výchozí chybová zpráva. Chcete-li zadat vlastní chybovou zprávu, použijte atribut.

## <a name="remote-attribute"></a>[Remote] – atribut

`[Remote]` Atribut implementuje ověřování na straně klienta, které vyžaduje volání metody na serveru, aby bylo možné určit, zda je vstup pole platný. Aplikace může například potřebovat ověřit, zda se uživatelské jméno již používá.

Implementace vzdáleného ověřování:

1. Vytvořte metodu Action pro volání JavaScriptu.  Metoda jQuery Validate [Remote](https://jqueryvalidation.org/remote-method/) očekává odpověď JSON:

   * `true`znamená, že jsou vstupní data platná.
   * `false`, `undefined`nebo `null` znamená, že vstup není platný. Zobrazí výchozí chybovou zprávu.
   * Jakýkoli jiný řetězec znamená, že vstup je neplatný. Zobrazí řetězec jako vlastní chybovou zprávu.

   Tady je příklad metody akce, která vrací vlastní chybovou zprávu:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. Ve třídě modelu poznámkujte vlastnost s `[Remote]` atributem, který odkazuje na metodu akce ověření, jak je znázorněno v následujícím příkladu:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   `[Remote]` Atribut je v `Microsoft.AspNetCore.Mvc` oboru názvů.
   
### <a name="additional-fields"></a>Další pole

`AdditionalFields` Vlastnost `[Remote]` atributu umožňuje ověřit kombinace polí s daty na serveru. Pokud má `User` model například `FirstName` a `LastName` vlastnosti, můžete chtít ověřit, že žádní stávající uživatelé již nemají odpovídající dvojici názvů. Následující příklad ukazuje, jak použít `AdditionalFields`:

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

`AdditionalFields`lze nastavit explicitně na řetězce "FirstName" a "LastName", ale pomocí operátoru [nameof](/dotnet/csharp/language-reference/keywords/nameof) se zjednoduší pozdější refaktoring. Metoda Action pro toto ověření musí přijmout oba `firstName` argumenty a `lastName` :

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

Když uživatel zadá jméno nebo příjmení, JavaScript vytvoří vzdálené volání, aby viděli, jestli se tento pár názvů povedl.

Chcete-li ověřit dvě nebo více dalších polí, poskytněte je jako seznam oddělený čárkami. Chcete-li například přidat `MiddleName` vlastnost do modelu, nastavte `[Remote]` atribut, jak je znázorněno v následujícím příkladu:

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`Podobně jako všechny argumenty atributu musí být konstantní výraz. Proto nepoužívejte [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> k inicializaci `AdditionalFields`.

## <a name="alternatives-to-built-in-attributes"></a>Alternativy k předdefinovaným atributům

Pokud potřebujete ověření, které neposkytuje předdefinované atributy, můžete:

* [Vytvořte vlastní atributy](#custom-attributes).
* [Implementujte IValidatableObject](#ivalidatableobject).

## <a name="custom-attributes"></a>Vlastní atributy

U scénářů, které vestavěné atributy ověřování nezpracovávají, můžete vytvořit vlastní ověřovací atributy. Vytvořte třídu, která dědí z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, a přepište <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> metodu.

`IsValid` Metoda přijímá objekt s názvem *hodnota*, což je vstup, který má být ověřen. Přetížení také přijímá `ValidationContext` objekt, který poskytuje další informace, jako je například instance modelu vytvořená vazbou modelu.

Následující příklad ověří, že datum vydání filmu v *klasickém* žánru nenásleduje po zadaném roce. `[ClassicMovie]` Atribut:

* Se spouští jenom na serveru.
* U klasických filmů ověří datum vydání:

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

`movie` Proměnná v předchozím příkladu představuje `Movie` objekt, který obsahuje data z odesílání formuláře. Pokud se ověření nepovede `ValidationResult` , vrátí se chybová zpráva.

## <a name="ivalidatableobject"></a>IValidatableObject

Předchozí příklad funguje pouze s `Movie` typy. Další možností pro ověřování na úrovni třídy je implementovat `IValidatableObject` ve třídě modelu, jak je znázorněno v následujícím příkladu:

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>Ověřování uzlu nejvyšší úrovně

Uzly nejvyšší úrovně zahrnují:

* Parametry akce
* Vlastnosti kontroleru
* Parametry obslužné rutiny stránky
* Vlastnosti modelu stránky

Kromě ověřování vlastností modelu jsou ověřovány i uzly nejvyšší úrovně svázané s modelem. V následujícím příkladu z ukázkové aplikace `VerifyPhone` metoda používá <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> k ověření parametru `phone` akce:

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

Uzly nejvyšší úrovně mohou být použity <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověřování. V následujícím příkladu z ukázkové aplikace určuje `CheckAge` metoda, že `age` parametr musí být svázán z řetězce dotazu při odeslání formuláře:

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

Na stránce pro kontrolu stáří (*check. cshtml*) Existují dva formuláře. První formulář odešle `Age` hodnotu `99` jako parametr řetězce dotazu:. `https://localhost:5001/Users/CheckAge?Age=99`

Při odeslání správně formátovaného `age` parametru z řetězce dotazu se formulář ověří.

Druhý formulář na stránce Kontrola stáří odesílá `Age` hodnotu v těle žádosti a ověření se nepovede. Vazba se nezdařila, `age` protože parametr musí pocházet z řetězce dotazu.

## <a name="maximum-errors"></a>Maximální počet chyb

Ověřování se zastaví, když se dosáhne maximálního počtu chyb (ve výchozím nastavení je 200). Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices`:

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a>Maximální rekurze

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>projde grafem objektu ověřovaného modelu. U modelů, které jsou hluboko nebo jsou nekonečně rekurzivní, může ověřování způsobit přetečení zásobníku. [MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) poskytuje způsob, jak zastavit ověřování v brzkém případě, kdy rekurze návštěvníka překročí nakonfigurovanou hloubku. Výchozí hodnota `MvcOptions.MaxValidationDepth` je 32.

## <a name="automatic-short-circuit"></a>Automatické krátké okruhy

Ověřování je automaticky zkrácené (vynecháno), pokud model grafu nevyžaduje ověření. Objekty, které modul runtime přeskočí ověřování pro zahrnutí kolekcí primitivních elementů ( `byte[]`například `string[]`, `Dictionary<string, string>`,) a složitých grafů objektů, které nemají žádné validátory.

## <a name="disable-validation"></a>Zakázat ověřování

Zakázání ověřování:

1. Vytvořte implementaci `IObjectModelValidator` , která neoznačí žádná pole jako neplatnou.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. Přidejte následující kód k `Startup.ConfigureServices` nahrazení výchozí `IObjectModelValidator` implementace v kontejneru vkládání závislostí.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

Pořád se můžou zobrazit chyby stavu modelu, které pocházejí z vazby modelu.

## <a name="client-side-validation"></a>Ověřování na straně klienta

Ověřování na straně klienta brání odeslání, dokud není formulář platný. Tlačítko Odeslat spustí JavaScript, který buď odešle formulář, nebo zobrazí chybové zprávy.

Ověřování na straně klienta zabrání zbytečnému přenosu na server, pokud dojde k chybám vstupu na formuláři. Následující odkazy skriptu v *_Layout. cshtml* a *_ValidationScriptsPartial. cshtml* podporují ověřování na straně klienta:

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

Skript [jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) nenáročného ověřování je vlastní knihovna front-end Microsoftu, která se vytváří na oblíbený modul plug-in [jQuery pro ověření](https://jqueryvalidation.org/) . Bez nenáročného ověřování by bylo nutné kód stejné ověřovací logiky nakódovat na dvou místech: jednou v atributech ověřování na straně serveru u vlastností modelu a pak znovu v skriptech na straně klienta. Místo toho [můžou pomocníky značek](xref:mvc/views/tag-helpers/intro) a [nápovědu HTML](xref:mvc/views/overview) používat atributy ověřování a metadata typu z vlastností modelu k vykreslování atributů HTML 5 `data-` pro prvky formuláře, které vyžadují ověření. jQuery nenáročné ověřování analyzuje `data-` atributy a předá logiku do příkazu jQuery Validate a efektivně kopíruje logiku ověřování na straně serveru do klienta. Chyby ověřování můžete zobrazit na klientovi pomocí značek pomocníka, jak je znázorněno zde:

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

Předchozí pomocník značek vykresluje následující kód HTML:

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

Všimněte si, `data-` že atributy ve výstupu HTML odpovídají atributům ověřování pro `Movie.ReleaseDate` vlastnost. `data-val-required` Atribut obsahuje chybovou zprávu, která se zobrazí, pokud uživatel neplní pole Datum vydání. jQuery unpassing předá tuto hodnotu metodě jQuery [Required ()](https://jqueryvalidation.org/required-method/) , která pak zobrazí tuto zprávu v doprovodném ** \<elementu span>** .

Ověřování datového typu je založené na typu .NET vlastnosti, pokud není přepsána `[DataType]` atributem. Prohlížeče mají vlastní výchozí chybové zprávy, ale tyto zprávy můžou potlačit ověření jQuery nenáročná ověřovací balíček. `[DataType]`atributy a podtřídy, jako je `[EmailAddress]` například umožňuje zadat chybovou zprávu.

## <a name="unobtrusive-validation"></a>Nenáročná ověření

Informace o nenáročnosti ověřování najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/1111).

### <a name="add-validation-to-dynamic-forms"></a>Přidání ověřování do dynamických formulářů

jQuery – neúspěšné ověření projde logiku ověření a parametry, které se při prvním načtení stránky ověřují. Proto ověřování nefunguje automaticky na dynamicky generovaných formulářích. Chcete-li povolit ověřování, řekněte jQuery nenápadu ověřování, aby se dynamický formulář analyzoval ihned po jeho vytvoření. Například následující kód nastaví ověřování na straně klienta na formuláři přidaném prostřednictvím jazyka AJAX.

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

`$.validator.unobtrusive.parse()` Metoda přijímá selektor jQuery pro svůj jeden argument. Tato metoda oznamuje nenáročné ověřování, aby bylo `data-` možné analyzovat atributy formulářů v rámci tohoto selektoru. Hodnoty těchto atributů jsou poté předány modulu plug-in jQuery Validate.

### <a name="add-validation-to-dynamic-controls"></a>Přidání ověřování do dynamických ovládacích prvků

`$.validator.unobtrusive.parse()` Metoda funguje na celém formuláři, nikoli na jednotlivých dynamicky generovaných ovládacích prvcích, například `<input>` a `<select/>`. Chcete-li znovu analyzovat formulář, odeberte data ověřování, která byla přidána při analýze formuláře dříve, jak je znázorněno v následujícím příkladu:

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a>Vlastní ověřování na straně klienta

Vlastní ověřování na straně klienta se provádí generováním `data-` atributů HTML, které fungují s vlastním ověřovacím adaptérem jQuery. Následující vzorový kód adaptéru byl napsán pro atributy `[ClassicMovie]` a `[ClassicMovieWithClientValidator]` , které byly představeny dříve v tomto článku:

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

Informace o tom, jak psát adaptéry, najdete v [dokumentaci ke službě jQuery Validate](https://jqueryvalidation.org/documentation/).

Použití adaptéru pro dané pole se spustí pomocí `data-` atributů, které:

* Označte pole jako podléhající ověřování (`data-val="true"`).
* Identifikujte název ověřovacího pravidla a text chybové zprávy (například `data-val-rulename="Error message."`).
* Zadejte další parametry, které vyžaduje validátor (například `data-val-rulename-param1="value"`).

Následující příklad ukazuje `data-` atributy pro `ClassicMovie` atribut ukázkové aplikace:

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

Jak bylo uvedeno dříve, [pomocníkům značek a značkám](xref:mvc/views/tag-helpers/intro) [HTML](xref:mvc/views/overview) se při vykreslování `data-` atributů používají informace z atributů ověřování. Existují dvě možnosti pro psaní kódu, který je výsledkem vytváření vlastních `data-` atributů HTML:

* Vytvořte třídu, která je odvozena `AttributeAdapterBase<TAttribute>` z třídy a třídu, `IValidationAttributeAdapterProvider`která implementuje, a zaregistrujte svůj atribut a jeho adaptér v di. Tato metoda následuje za [instančním objektem zodpovědnosti](https://wikipedia.org/wiki/Single_responsibility_principle) v tomto ověřovacím kódu souvisejícím se serverem a klientem je v samostatných třídách. Adaptér má také výhodu, že protože je zaregistrován v DI, jsou v případě potřeby k dispozici jiné služby v DI.
* Implementujte `IClientModelValidator` ve `ValidationAttribute` své třídě. Tato metoda může být vhodná, pokud atribut neprovádí žádné ověřování na straně serveru a nepotřebuje žádné služby od DI.

### <a name="attributeadapter-for-client-side-validation"></a>AttributeAdapter pro ověřování na straně klienta

Tato metoda vykreslování `data-` atributů ve formátu HTML je používána `ClassicMovie` atributem v ukázkové aplikaci. Přidání ověřování klientů pomocí této metody:

1. Vytvořte třídu adaptéru atributů pro vlastní ověřovací atribut. Odvodit třídu z [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2). Vytvořte `AddValidation` metodu, která přidá `data-` atributy do vykresleného výstupu, jak je znázorněno v následujícím příkladu:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. Vytvořte třídu poskytovatele adaptéru, která implementuje <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>. V `GetAttributeAdapter` metodě předejte vlastní atribut konstruktoru adaptéru, jak je znázorněno v následujícím příkladu:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. Zaregistrujte poskytovatele adaptéru pro DI `Startup.ConfigureServices`v:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>IClientModelValidator pro ověřování na straně klienta

Tato metoda vykreslování `data-` atributů ve formátu HTML je používána `ClassicMovieWithClientValidator` atributem v ukázkové aplikaci. Přidání ověřování klientů pomocí této metody:

* Ve vlastním ověřovacím atributu implementujte `IClientModelValidator` rozhraní a vytvořte `AddValidation` metodu. V `AddValidation` metodě přidejte `data-` atributy pro ověření, jak je znázorněno v následujícím příkladu:

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a>Zakázat ověřování na straně klienta

Následující kód zakáže ověřování klienta v Razor Pages:

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

Další možnosti zakázání ověřování na straně klienta:

* Odkomentujte odkaz na `_ValidationScriptsPartial` ve všech souborech *. cshtml* .
* Odeberte obsah souboru *Pages\Shared\_ValidationScriptsPartial. cshtml* .

Předchozí přístup nezabrání ověřování na straně klienta ASP.NET Core knihovně tříd identity Razor. Další informace naleznete v tématu <xref:security/authentication/scaffold-identity>.

## <a name="additional-resources"></a>Další zdroje

* [Obor názvů System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations)
* [Vazba modelu](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento článek vysvětluje, jak ověřit vstup uživatele ve ASP.NET Core MVC nebo v aplikaci Razor Pages.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).

## <a name="model-state"></a>Stav modelu

Stav modelu představuje chyby, které pocházejí ze dvou subsystémů: vazby modelu a ověření modelu. Chyby, které pocházejí z [vazby mezi modely](model-binding.md) , jsou obvykle chyby převodu dat (například "x" je zadáno v poli, které očekává celé číslo). K ověření modelu dochází po vazbě modelu a hlášení chyb, kde data neodpovídají obchodním pravidlům (například hodnota 0 je zadána v poli, které očekává hodnocení mezi 1 a 5).

Před provedením akce kontroleru nebo obslužné rutiny Razor Pages se vyskytuje jak vazba modelů, tak ověřování. U webových aplikací je zodpovědností aplikace vhodné je kontrolovat `ModelState.IsValid` a reagovat. Webové aplikace obvykle znovu zobrazí stránku s chybovou zprávou:

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

Řadiče webového rozhraní API nemusí kontrolovat `ModelState.IsValid` , jestli mají `[ApiController]` atribut. V takovém případě je vrácena Automatická odpověď HTTP 400 obsahující podrobnosti o chybě, pokud stav modelu není platný. Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).

## <a name="rerun-validation"></a>Znovu spustit ověření

Ověřování je automatické, ale můžete je chtít opakovat ručně. Můžete například vypočítat hodnotu pro vlastnost a chtít znovu spustit ověřování po nastavení vlastnosti na vypočítanou hodnotu. Chcete-li znovu spustit ověřování `TryValidateModel` , zavolejte metodu, jak je znázorněno zde:

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a>Atributy ověřování

Atributy ověřování umožňují zadat pravidla ověřování pro vlastnosti modelu. Následující příklad z [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) zobrazuje třídu modelu s poznámkou ověřování atributů. `[ClassicMovie]` Atribut je vlastní ověřovací atribut a jsou integrovány ostatní. Není zobrazeno `[ClassicMovie2]`, který ukazuje alternativní způsob implementace vlastního atributu.

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a>Předdefinované atributy

Mezi předdefinované atributy ověřování patří:

* `[CreditCard]`: Ověří, zda má vlastnost formát kreditní karty.
* `[Compare]`: Ověří, že se dvě vlastnosti v modelu shodují. Například soubor *Register.cshtml.cs* používá `[Compare]` k ověření, že se dvě zadaná hesla shodují. [Identita uživatelského rozhraní](xref:security/authentication/scaffold-identity) pro zobrazení kódu registrace.
* `[EmailAddress]`: Ověří, zda má vlastnost formát e-mailu.
* `[Phone]`: Ověří, zda má vlastnost formát telefonního čísla.
* `[Range]`: Ověří, že hodnota vlastnosti spadá do zadaného rozsahu.
* `[RegularExpression]`: Ověří, že hodnota vlastnosti odpovídá zadanému regulárnímu výrazu.
* `[Required]`: Ověří, že pole nemá hodnotu null. Podrobnosti o chování tohoto atributu naleznete v [ `[Required]` atributu Attribute](#required-attribute) .
* `[StringLength]`: Ověří, že hodnota vlastnosti řetězce nepřekračuje zadané omezení délky.
* `[Url]`: Ověří, zda má vlastnost formát adresy URL.
* `[Remote]`: Ověří vstup na straně klienta voláním metody Action na serveru. Podrobnosti o chování tohoto atributu naleznete v [ `[Remote]` atributu Attribute](#remote-attribute) .

Při použití `[RegularExpression]` atributu s ověřováním na straně klienta je regulární výraz spuštěn v jazyce JavaScript na klientovi. To znamená, že se použije chování pro porovnání [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) . Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/corefx/issues/42487).

Úplný seznam ověřovacích atributů najdete v oboru názvů [System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations) .

### <a name="error-messages"></a>Chybové zprávy

Atributy ověřování umožňují zadat chybovou zprávu, která se má zobrazit pro neplatný vstup. Příklad:

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

Interně atributy volají `String.Format` jako zástupný symbol pro název pole a někdy další zástupné symboly. Příklad:

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

Při použití na `Name` vlastnost bude chybová zpráva vytvořená v předchozím kódu "Délka názvu musí být v rozmezí 6 až 8."

Chcete-li zjistit, které parametry jsou `String.Format` předány pro konkrétní chybovou zprávu atributu, přečtěte si [zdrojový kód pro anotace](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).

## <a name="required-attribute"></a>[Required] – atribut

Ve výchozím nastavení systém ověřování zpracovává parametry, které neumožňují hodnotu null nebo vlastnosti, jako by měly `[Required]` atribut. [Typy hodnot](/dotnet/csharp/language-reference/keywords/value-types) , jako `decimal` jsou `int` a, nejsou null.

### <a name="required-validation-on-the-server"></a>[Požadováno] ověření na serveru

Na serveru je požadovaná hodnota považována za chybějící, pokud má vlastnost hodnotu null. Pole, které nesmí mít hodnotu null, je vždy platné a chybová zpráva [required] se nezobrazí.

Vazba modelu pro vlastnost, která nemůže mít hodnotu null, může selhat, což vede k chybové zprávě, `The value '' is invalid`jako je například. Chcete-li zadat vlastní chybovou zprávu pro ověřování na straně serveru pro typy, které neumožňují hodnotu null, máte následující možnosti:

* Převést pole na hodnotu null (například `decimal?` místo `decimal`). Typy hodnot s [možnou hodnotou null\<>](/dotnet/csharp/programming-guide/nullable-types/) jsou považovány za standardní typy s možnou hodnotou null
* Zadejte výchozí chybovou zprávu, kterou má použít vazba modelu, jak je znázorněno v následujícím příkladu:

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  Další informace o chybách vazeb modelů, které lze nastavit jako výchozí zprávy pro, naleznete <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>v tématu.

### <a name="required-validation-on-the-client"></a>[Požadováno] ověřování na klientovi

Typy a řetězce, které neumožňují hodnotu null, jsou v porovnání s tímto serverem zpracovávány jinak v klientovi. Na klientovi:

* Hodnota se považuje za přítomnou pouze v případě, že je pro ni zadán vstup. Proto ověřování na straně klienta zpracovává typy, které neumožňují hodnotu null, stejné jako typy s možnou hodnotou null.
* Prázdné znaky v poli řetězce se považují za platný vstup metodou jQuery [vyžadované](https://jqueryvalidation.org/required-method/) ověřením. Ověřování na straně serveru považuje požadované pole řetězce za neplatné, pokud je zadána pouze mezera.

Jak bylo uvedeno dříve, typy neumožňující hodnotu null jsou považovány za `[Required]` , i když mají atribut. To znamená, že získáte ověřování na straně klienta i v `[Required]` případě, že atribut nepoužijete. Pokud však atribut nepoužíváte, zobrazí se výchozí chybová zpráva. Chcete-li zadat vlastní chybovou zprávu, použijte atribut.

## <a name="remote-attribute"></a>[Remote] – atribut

`[Remote]` Atribut implementuje ověřování na straně klienta, které vyžaduje volání metody na serveru, aby bylo možné určit, zda je vstup pole platný. Aplikace může například potřebovat ověřit, zda se uživatelské jméno již používá.

Implementace vzdáleného ověřování:

1. Vytvořte metodu Action pro volání JavaScriptu.  Metoda jQuery Validate [Remote](https://jqueryvalidation.org/remote-method/) očekává odpověď JSON:

   * `"true"`znamená, že jsou vstupní data platná.
   * `"false"`, `undefined`nebo `null` znamená, že vstup není platný.  Zobrazí výchozí chybovou zprávu.
   * Jakýkoli jiný řetězec znamená, že vstup je neplatný. Zobrazí řetězec jako vlastní chybovou zprávu.

   Tady je příklad metody akce, která vrací vlastní chybovou zprávu:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. Ve třídě modelu poznámkujte vlastnost s `[Remote]` atributem, který odkazuje na metodu akce ověření, jak je znázorněno v následujícím příkladu:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   `[Remote]` Atribut je v `Microsoft.AspNetCore.Mvc` oboru názvů. Pokud nepoužíváte Metapackage `Microsoft.AspNetCore.App` nebo `Microsoft.AspNetCore.All` , nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) .
   
### <a name="additional-fields"></a>Další pole

`AdditionalFields` Vlastnost `[Remote]` atributu umožňuje ověřit kombinace polí s daty na serveru. Pokud má `User` model například `FirstName` a `LastName` vlastnosti, můžete chtít ověřit, že žádní stávající uživatelé již nemají odpovídající dvojici názvů. Následující příklad ukazuje, jak použít `AdditionalFields`:

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

`AdditionalFields`lze nastavit explicitně na řetězce `"FirstName"` a `"LastName"`, ale použití operátoru [nameof](/dotnet/csharp/language-reference/keywords/nameof) zjednodušuje pozdější refaktoring. Metoda Action pro toto ověření musí přijmout argumenty jméno a příjmení:

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

Když uživatel zadá jméno nebo příjmení, JavaScript vytvoří vzdálené volání, aby viděli, jestli se tento pár názvů povedl.

Chcete-li ověřit dvě nebo více dalších polí, poskytněte je jako seznam oddělený čárkami. Chcete-li například přidat `MiddleName` vlastnost do modelu, nastavte `[Remote]` atribut, jak je znázorněno v následujícím příkladu:

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`Podobně jako všechny argumenty atributu musí být konstantní výraz. Proto nepoužívejte [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> k inicializaci `AdditionalFields`.

## <a name="alternatives-to-built-in-attributes"></a>Alternativy k předdefinovaným atributům

Pokud potřebujete ověření, které neposkytuje předdefinované atributy, můžete:

* [Vytvořte vlastní atributy](#custom-attributes).
* [Implementujte IValidatableObject](#ivalidatableobject).

## <a name="custom-attributes"></a>Vlastní atributy

U scénářů, které vestavěné atributy ověřování nezpracovávají, můžete vytvořit vlastní ověřovací atributy. Vytvořte třídu, která dědí z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, a přepište <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> metodu.

`IsValid` Metoda přijímá objekt s názvem *hodnota*, což je vstup, který má být ověřen. Přetížení také přijímá `ValidationContext` objekt, který poskytuje další informace, jako je například instance modelu vytvořená vazbou modelu.

Následující příklad ověří, že datum vydání filmu v *klasickém* žánru nenásleduje po zadaném roce. `[ClassicMovie2]` Atribut nejprve zkontroluje Žánr a pokračuje pouze v případě, že je *klasický*. U filmů identifikovaných jako klasických kontroluje datum vydání, aby se zajistilo, že není pozdější než limit předaný konstruktoru atributu.)

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

`movie` Proměnná v předchozím příkladu představuje `Movie` objekt, který obsahuje data z odesílání formuláře. `IsValid` Metoda zkontroluje datum a Žánr. Po úspěšném ověření `IsValid` vrátí `ValidationResult.Success` kód. Pokud se ověření nepovede `ValidationResult` , vrátí se chybová zpráva.

## <a name="ivalidatableobject"></a>IValidatableObject

Předchozí příklad funguje pouze s `Movie` typy. Další možností pro ověřování na úrovni třídy je implementovat `IValidatableObject` ve třídě modelu, jak je znázorněno v následujícím příkladu:

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>Ověřování uzlu nejvyšší úrovně

Uzly nejvyšší úrovně zahrnují:

* Parametry akce
* Vlastnosti kontroleru
* Parametry obslužné rutiny stránky
* Vlastnosti modelu stránky

Kromě ověřování vlastností modelu jsou ověřovány i uzly nejvyšší úrovně svázané s modelem. V následujícím příkladu z ukázkové aplikace `VerifyPhone` metoda používá <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> k ověření parametru `phone` akce:

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

Uzly nejvyšší úrovně mohou být použity <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověřování. V následujícím příkladu z ukázkové aplikace určuje `CheckAge` metoda, že `age` parametr musí být svázán z řetězce dotazu při odeslání formuláře:

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

Na stránce pro kontrolu stáří (*check. cshtml*) Existují dva formuláře. První formulář odešle `Age` hodnotu `99` jako řetězec dotazu:. `https://localhost:5001/Users/CheckAge?Age=99`

Při odeslání správně formátovaného `age` parametru z řetězce dotazu se formulář ověří.

Druhý formulář na stránce Kontrola stáří odesílá `Age` hodnotu v těle žádosti a ověření se nepovede. Vazba se nezdařila, `age` protože parametr musí pocházet z řetězce dotazu.

Při spuštění s `CompatibilityVersion.Version_2_1` nebo novějším je ověřování uzlů nejvyšší úrovně ve výchozím nastavení povolené. V opačném případě je ověřování uzlu nejvyšší úrovně zakázané. Výchozí možnost může být přepsána nastavením <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> vlastnosti v (`Startup.ConfigureServices`), jak je znázorněno zde:

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a>Maximální počet chyb

Ověřování se zastaví, když se dosáhne maximálního počtu chyb (ve výchozím nastavení je 200). Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices`:

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a>Maximální rekurze

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>projde grafem objektu ověřovaného modelu. U modelů, které jsou velmi hlubokoelné nebo nekonečné rekurzivní, může ověřování způsobit přetečení zásobníku. [MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) poskytuje způsob, jak zastavit ověřování v brzkém případě, kdy rekurze návštěvníka překročí nakonfigurovanou hloubku. Výchozí hodnota `MvcOptions.MaxValidationDepth` je 32 při spuštění v systému `CompatibilityVersion.Version_2_2` nebo novějším. Pro starší verze je hodnota null, což znamená bez omezení hloubky.

## <a name="automatic-short-circuit"></a>Automatické krátké okruhy

Ověřování je automaticky zkrácené (vynecháno), pokud model grafu nevyžaduje ověření. Objekty, které modul runtime přeskočí ověřování pro zahrnutí kolekcí primitivních elementů ( `byte[]`například `string[]`, `Dictionary<string, string>`,) a složitých grafů objektů, které nemají žádné validátory.

## <a name="disable-validation"></a>Zakázat ověřování

Zakázání ověřování:

1. Vytvořte implementaci `IObjectModelValidator` , která neoznačí žádná pole jako neplatnou.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. Přidejte následující kód k `Startup.ConfigureServices` nahrazení výchozí `IObjectModelValidator` implementace v kontejneru vkládání závislostí.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

Pořád se můžou zobrazit chyby stavu modelu, které pocházejí z vazby modelu.

## <a name="client-side-validation"></a>Ověřování na straně klienta

Ověřování na straně klienta brání odeslání, dokud není formulář platný. Tlačítko Odeslat spustí JavaScript, který buď odešle formulář, nebo zobrazí chybové zprávy.

Ověřování na straně klienta zabrání zbytečnému přenosu na server, pokud dojde k chybám vstupu na formuláři. Následující odkazy skriptu v *_Layout. cshtml* a *_ValidationScriptsPartial. cshtml* podporují ověřování na straně klienta:

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

Skript [jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) nenáročného ověřování je vlastní knihovna front-end Microsoftu, která se vytváří na oblíbený modul plug-in [jQuery pro ověření](https://jqueryvalidation.org/) . Bez nenáročného ověřování by bylo nutné kód stejné ověřovací logiky nakódovat na dvou místech: jednou v atributech ověřování na straně serveru u vlastností modelu a pak znovu v skriptech na straně klienta. Místo toho [můžou pomocníky značek](xref:mvc/views/tag-helpers/intro) a [nápovědu HTML](xref:mvc/views/overview) používat atributy ověřování a metadata typu z vlastností modelu k vykreslování atributů HTML 5 `data-` pro prvky formuláře, které vyžadují ověření. jQuery nenáročné ověřování analyzuje `data-` atributy a předá logiku do příkazu jQuery Validate a efektivně kopíruje logiku ověřování na straně serveru do klienta. Chyby ověřování můžete zobrazit na klientovi pomocí značek pomocníka, jak je znázorněno zde:

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

Předchozí pomocník značek vykresluje následující kód HTML.

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

Všimněte si, `data-` že atributy ve výstupu HTML odpovídají atributům ověřování pro `ReleaseDate` vlastnost. `data-val-required` Atribut obsahuje chybovou zprávu, která se zobrazí, pokud uživatel neplní pole Datum vydání. jQuery unpassing předá tuto hodnotu metodě jQuery [Required ()](https://jqueryvalidation.org/required-method/) , která pak zobrazí tuto zprávu v doprovodném ** \<elementu span>** .

Ověřování datového typu je založené na typu .NET vlastnosti, pokud není přepsána `[DataType]` atributem. Prohlížeče mají vlastní výchozí chybové zprávy, ale tyto zprávy můžou potlačit ověření jQuery nenáročná ověřovací balíček. `[DataType]`atributy a podtřídy, jako je `[EmailAddress]` například umožňuje zadat chybovou zprávu.

### <a name="add-validation-to-dynamic-forms"></a>Přidání ověřování do dynamických formulářů

jQuery – neúspěšné ověření projde logiku ověření a parametry, které se při prvním načtení stránky ověřují. Proto ověřování nefunguje automaticky na dynamicky generovaných formulářích. Chcete-li povolit ověřování, řekněte jQuery nenápadu ověřování, aby se dynamický formulář analyzoval ihned po jeho vytvoření. Například následující kód nastaví ověřování na straně klienta na formuláři přidaném prostřednictvím jazyka AJAX.

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

`$.validator.unobtrusive.parse()` Metoda přijímá selektor jQuery pro svůj jeden argument. Tato metoda oznamuje nenáročné ověřování, aby bylo `data-` možné analyzovat atributy formulářů v rámci tohoto selektoru. Hodnoty těchto atributů jsou poté předány modulu plug-in jQuery Validate.

### <a name="add-validation-to-dynamic-controls"></a>Přidání ověřování do dynamických ovládacích prvků

`$.validator.unobtrusive.parse()` Metoda funguje na celém formuláři, nikoli na jednotlivých dynamicky generovaných ovládacích prvcích, například `<input>` a `<select/>`. Chcete-li znovu analyzovat formulář, odeberte data ověřování, která byla přidána při analýze formuláře dříve, jak je znázorněno v následujícím příkladu:

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a>Vlastní ověřování na straně klienta

Vlastní ověřování na straně klienta se provádí generováním `data-` atributů HTML, které fungují s vlastním ověřovacím adaptérem jQuery. Následující vzorový kód adaptéru byl napsán pro atributy `ClassicMovie` a `ClassicMovie2` , které byly představeny dříve v tomto článku:

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

Informace o tom, jak psát adaptéry, najdete v [dokumentaci ke službě jQuery Validate](https://jqueryvalidation.org/documentation/).

Použití adaptéru pro dané pole se spustí pomocí `data-` atributů, které:

* Označte pole jako podléhající ověřování (`data-val="true"`).
* Identifikujte název ověřovacího pravidla a text chybové zprávy (například `data-val-rulename="Error message."`).
* Zadejte další parametry, které vyžaduje validátor (například `data-val-rulename-parm1="value"`).

Následující příklad ukazuje `data-` atributy pro `ClassicMovie` atribut ukázkové aplikace:

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

Jak bylo uvedeno dříve, [pomocníkům značek a značkám](xref:mvc/views/tag-helpers/intro) [HTML](xref:mvc/views/overview) se při vykreslování `data-` atributů používají informace z atributů ověřování. Existují dvě možnosti pro psaní kódu, který je výsledkem vytváření vlastních `data-` atributů HTML:

* Vytvořte třídu, která je odvozena `AttributeAdapterBase<TAttribute>` z třídy a třídu, `IValidationAttributeAdapterProvider`která implementuje, a zaregistrujte svůj atribut a jeho adaptér v di. Tato metoda následuje za [instančním objektem zodpovědnosti](https://wikipedia.org/wiki/Single_responsibility_principle) v tomto ověřovacím kódu souvisejícím se serverem a klientem je v samostatných třídách. Adaptér má také výhodu, že protože je zaregistrován v DI, jsou v případě potřeby k dispozici jiné služby v DI.
* Implementujte `IClientModelValidator` ve `ValidationAttribute` své třídě. Tato metoda může být vhodná, pokud atribut neprovádí žádné ověřování na straně serveru a nepotřebuje žádné služby od DI.

### <a name="attributeadapter-for-client-side-validation"></a>AttributeAdapter pro ověřování na straně klienta

Tato metoda vykreslování `data-` atributů ve formátu HTML je používána `ClassicMovie` atributem v ukázkové aplikaci. Přidání ověřování klientů pomocí této metody:

1. Vytvořte třídu adaptéru atributů pro vlastní ověřovací atribut. Odvodit třídu z [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2). Vytvořte `AddValidation` metodu, která přidá `data-` atributy do vykresleného výstupu, jak je znázorněno v následujícím příkladu:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. Vytvořte třídu poskytovatele adaptéru, která implementuje <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>. V `GetAttributeAdapter` metodě předejte vlastní atribut konstruktoru adaptéru, jak je znázorněno v následujícím příkladu:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. Zaregistrujte poskytovatele adaptéru pro DI `Startup.ConfigureServices`v:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>IClientModelValidator pro ověřování na straně klienta

Tato metoda vykreslování `data-` atributů ve formátu HTML je používána `ClassicMovie2` atributem v ukázkové aplikaci. Přidání ověřování klientů pomocí této metody:

* Ve vlastním ověřovacím atributu implementujte `IClientModelValidator` rozhraní a vytvořte `AddValidation` metodu. V `AddValidation` metodě přidejte `data-` atributy pro ověření, jak je znázorněno v následujícím příkladu:

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a>Zakázat ověřování na straně klienta

Následující kód zakáže ověřování klienta v zobrazeních MVC:

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

A v Razor Pages:

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

Další možností pro zakázání ověřování klienta je přidat komentář k odkazu do `_ValidationScriptsPartial` souboru *. cshtml* .

## <a name="additional-resources"></a>Další zdroje

* [Obor názvů System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations)
* [Vazba modelu](model-binding.md)

::: moniker-end
