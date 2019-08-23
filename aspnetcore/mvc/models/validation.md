---
title: Ověření modelu ve ASP.NET Core MVC
author: rick-anderson
description: Přečtěte si o ověřování modelu ve ASP.NET Core MVC a Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
monikerRange: '>= aspnetcore-2.1'
uid: mvc/models/validation
ms.openlocfilehash: eb18d3a701a4d1937ac6eb9f61916f348b95882a
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975261"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a>Ověřování modelu ve ASP.NET Core MVC a Razor Pages

Tento článek vysvětluje, jak ověřit vstup uživatele ve ASP.NET Core MVC nebo v aplikaci Razor Pages.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([stažení](xref:index#how-to-download-a-sample)).

## <a name="model-state"></a>Stav modelu

Stav modelu představuje chyby, které pocházejí ze dvou subsystémů: vazby modelu a ověření modelu. Chyby, které pocházejí z [vazby mezi modely](model-binding.md) , jsou obvykle chyby převodu dat (například "x" je zadáno v poli, které očekává celé číslo). K ověření modelu dochází po vazbě modelu a hlášení chyb, kde data neodpovídají obchodním pravidlům (například hodnota 0 je zadána v poli, které očekává hodnocení mezi 1 a 5).

Před provedením akce kontroleru nebo obslužné rutiny Razor Pages se vyskytuje jak vazba modelů, tak ověřování. U webových aplikací je zodpovědností aplikace vhodné je kontrolovat `ModelState.IsValid` a reagovat. Webové aplikace obvykle znovu zobrazí stránku s chybovou zprávou:

[!code-csharp[](validation/sample_snapshot/Create.cshtml.cs?name=snippet&highlight=3-6)]

Řadiče webového rozhraní API nemusí kontrolovat `ModelState.IsValid` , jestli `[ApiController]` mají atribut. V takovém případě je vrácena Automatická odpověď HTTP 400 obsahující podrobnosti o problému, pokud stav modelu není platný. Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).

## <a name="rerun-validation"></a>Znovu spustit ověření

Ověřování je automatické, ale můžete je chtít opakovat ručně. Můžete například vypočítat hodnotu pro vlastnost a chtít znovu spustit ověřování po nastavení vlastnosti na vypočítanou hodnotu. Chcete-li znovu spustit ověřování `TryValidateModel` , zavolejte metodu, jak je znázorněno zde:

[!code-csharp[](validation/sample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a>Atributy ověřování

Atributy ověřování umožňují zadat pravidla ověřování pro vlastnosti modelu. Následující příklad z [ukázkové aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) zobrazuje třídu modelu s poznámkou ověřování atributů. `[ClassicMovie]` Atribut je vlastní ověřovací atribut a jsou integrovány ostatní. (Nezobrazuje se `[ClassicMovie2]`, což ukazuje alternativní způsob implementace vlastního atributu.)

[!code-csharp[](validation/sample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a>Předdefinované atributy

Tady jsou některé z vestavěných ověřovacích atributů:

* `[CreditCard]`: Ověří, jestli má vlastnost formát kreditní karty.
* `[Compare]`: Ověřuje, že se shodují dvě vlastnosti v modelu.
* `[EmailAddress]`: Ověří, zda má vlastnost formát e-mailu.
* `[Phone]`: Ověří, zda má vlastnost formát telefonního čísla.
* `[Range]`: Ověří, zda hodnota vlastnosti spadá do zadaného rozsahu.
* `[RegularExpression]`: Ověří, zda hodnota vlastnosti odpovídá zadanému regulárnímu výrazu.
* `[Required]`: Ověří, zda pole nemá hodnotu null. Podrobnosti o chování tohoto atributu naleznete v [atributu [required]](#required-attribute) .
* `[StringLength]`: Ověří, že hodnota vlastnosti řetězce nepřekračuje zadané omezení délky.
* `[Url]`: Ověří, zda má vlastnost formát adresy URL.
* `[Remote]`: Ověří vstup na straně klienta voláním metody Action na serveru. Podrobnosti o chování tohoto atributu naleznete v [atributu [Remote]](#remote-attribute) .

Úplný seznam ověřovacích atributů najdete v oboru názvů [System. ComponentModel.](xref:System.ComponentModel.DataAnnotations) DataAnnotations.

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

Chcete-li zjistit, které parametry jsou `String.Format` předány pro konkrétní chybovou zprávu atributu, přečtěte si [zdrojový kód](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)pro anotace.

## <a name="required-attribute"></a>[Required] – atribut

Ve výchozím nastavení systém ověřování zpracovává parametry, které neumožňují hodnotu null nebo vlastnosti, jako by měly `[Required]` atribut. `int` [Typy hodnot,](/dotnet/csharp/language-reference/keywords/value-types) jako jsou a,nejsounull.`decimal`

### <a name="required-validation-on-the-server"></a>[Požadováno] ověření na serveru

Na serveru je požadovaná hodnota považována za chybějící, pokud má vlastnost hodnotu null. Pole, které nesmí mít hodnotu null, je vždy platné a chybová zpráva [required] se nezobrazí.

Vazba modelu pro vlastnost, která nemůže mít hodnotu null, může selhat, což vede k chybové zprávě, `The value '' is invalid`jako je například. Chcete-li zadat vlastní chybovou zprávu pro ověřování na straně serveru pro typy, které neumožňují hodnotu null, máte následující možnosti:

* Převést pole na hodnotu null (například `decimal?` `decimal`místo). Typy hodnot s možnou hodnotou [null\<>](/dotnet/csharp/programming-guide/nullable-types/) jsou považovány za standardní typy s možnou hodnotou null
* Zadejte výchozí chybovou zprávu, kterou má použít vazba modelu, jak je znázorněno v následujícím příkladu:

  [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

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
   * `"false"`, `undefined` nebo`null` znamená, že vstup není platný.  Zobrazí výchozí chybovou zprávu.
   * Jakýkoli jiný řetězec znamená, že vstup je neplatný. Zobrazí řetězec jako vlastní chybovou zprávu.

   Tady je příklad metody akce, která vrací vlastní chybovou zprávu:

   [!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. Ve třídě modelu poznámkujte vlastnost s `[Remote]` atributem, který odkazuje na metodu akce ověření, jak je znázorněno v následujícím příkladu:

   [!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   `[Remote]` Atribut je`Microsoft.AspNetCore.Mvc` v oboru názvů. Pokud nepoužíváte `Microsoft.AspNetCore.App` Metapackage nebo `Microsoft.AspNetCore.All` , nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) .
   
### <a name="additional-fields"></a>Další pole

`AdditionalFields` Vlastnost`[Remote]` atributu umožňuje ověřit kombinace polí s daty na serveru. Pokud má `User` `LastName` model například `FirstName` a vlastnosti, můžete chtít ověřit, že žádní stávající uživatelé již nemají odpovídající dvojici názvů. Následující příklad ukazuje, jak použít `AdditionalFields`:

[!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserNameProperties)]

`AdditionalFields`lze nastavit explicitně na řetězce `"FirstName"` a `"LastName"`, ale použití [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) operátoru zjednodušuje pozdější refaktoring. Metoda Action pro toto ověření musí přijmout argumenty jméno a příjmení:

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyName)]

Když uživatel zadá jméno nebo příjmení, JavaScript vytvoří vzdálené volání, aby viděli, jestli se tento pár názvů povedl.

Chcete-li ověřit dvě nebo více dalších polí, poskytněte je jako seznam oddělený čárkami. Chcete-li například přidat `MiddleName` vlastnost do modelu, `[Remote]` nastavte atribut, jak je znázorněno v následujícím příkladu:

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`Podobně jako všechny argumenty atributu musí být konstantní výraz. Proto nepoužívejte interpolované [řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> k inicializaci `AdditionalFields`.

## <a name="alternatives-to-built-in-attributes"></a>Alternativy k předdefinovaným atributům

Pokud potřebujete ověření, které neposkytuje předdefinované atributy, můžete:

* [Vytvořte vlastní atributy](#custom-attributes).
* [Implementujte IValidatableObject](#ivalidatableobject).

## <a name="custom-attributes"></a>Vlastní atributy

U scénářů, které vestavěné atributy ověřování nezpracovávají, můžete vytvořit vlastní ověřovací atributy. Vytvořte třídu, která dědí z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, a <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> přepište metodu.

Metoda přijímá objekt s názvem hodnota, což je vstup, který má být ověřen. `IsValid` Přetížení také přijímá `ValidationContext` objekt, který poskytuje další informace, jako je například instance modelu vytvořená vazbou modelu.

Následující příklad ověří, že datum vydání filmu v *klasickém* žánru nenásleduje po zadaném roce. Atribut nejprve zkontroluje Žánr a pokračuje pouze v případě, že je *klasický.* `[ClassicMovie2]` U filmů identifikovaných jako klasických kontroluje datum vydání, aby se zajistilo, že není pozdější než limit předaný konstruktoru atributu.)

[!code-csharp[](validation/sample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

Proměnná v předchozím příkladu `Movie` představuje objekt, který obsahuje data z odesílání formuláře. `movie` `IsValid` Metoda zkontroluje datum a Žánr. Po úspěšném ověření `IsValid` `ValidationResult.Success` vrátí kód. Pokud se ověření nepovede `ValidationResult` , vrátí se chybová zpráva.

## <a name="ivalidatableobject"></a>IValidatableObject

Předchozí příklad funguje pouze s `Movie` typy. Další možností pro ověřování na úrovni třídy je implementovat `IValidatableObject` ve třídě modelu, jak je znázorněno v následujícím příkladu:

[!code-csharp[](validation/sample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>Ověřování uzlu nejvyšší úrovně

Uzly nejvyšší úrovně zahrnují:

* Parametry akce
* Vlastnosti kontroleru
* Parametry obslužné rutiny stránky
* Vlastnosti modelu stránky

Kromě ověřování vlastností modelu jsou ověřovány i uzly nejvyšší úrovně svázané s modelem. V následujícím příkladu z ukázkové aplikace `VerifyPhone` metoda <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> používá k ověření `phone` parametru akce:

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

Uzly nejvyšší úrovně mohou být použity <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověřování. V následujícím příkladu z ukázkové aplikace `CheckAge` určuje metoda `age` , že parametr musí být svázán z řetězce dotazu při odeslání formuláře:

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_CheckAge)]

Na stránce pro kontrolu stáří (*check. cshtml*) Existují dva formuláře. První formulář odešle `Age` `99` hodnotu jako řetězec dotazu: `https://localhost:5001/Users/CheckAge?Age=99`.

Při odeslání správně formátovaného `age` parametru z řetězce dotazu se formulář ověří.

Druhý formulář na stránce Kontrola stáří odesílá `Age` hodnotu v těle žádosti a ověření se nepovede. Vazba se nezdařila, `age` protože parametr musí pocházet z řetězce dotazu.

Při spuštění s `CompatibilityVersion.Version_2_1` nebo novějším je ověřování uzlů nejvyšší úrovně ve výchozím nastavení povolené. V opačném případě je ověřování uzlu nejvyšší úrovně zakázané. Výchozí možnost může být přepsána nastavením <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> vlastnosti v (`Startup.ConfigureServices`), jak je znázorněno zde:

[!code-csharp[](validation/sample_snapshot/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a>Maximální počet chyb

Ověřování se zastaví, když se dosáhne maximálního počtu chyb (ve výchozím nastavení je 200). Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices`:

[!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a>Maximální rekurze

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>projde grafem objektu ověřovaného modelu. U modelů, které jsou velmi hlubokoelné nebo nekonečné rekurzivní, může ověřování způsobit přetečení zásobníku. [MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) poskytuje způsob, jak zastavit ověřování v brzkém případě, kdy rekurze návštěvníka překročí nakonfigurovanou hloubku. Výchozí hodnota `MvcOptions.MaxValidationDepth` je 200 při spuštění v systému `CompatibilityVersion.Version_2_2` nebo novějším. Pro starší verze je hodnota null, což znamená bez omezení hloubky.

## <a name="automatic-short-circuit"></a>Automatické krátké okruhy

Ověřování je automaticky zkrácené (vynecháno), pokud model grafu nevyžaduje ověření. Objekty, které modul runtime přeskočí ověřování pro zahrnutí kolekcí primitivních elementů ( `byte[]`například `string[]`, `Dictionary<string, string>`,) a složitých grafů objektů, které nemají žádné validátory.

## <a name="disable-validation"></a>Zakázat ověřování

Zakázání ověřování:

1. Vytvořte implementaci `IObjectModelValidator` , která neoznačí žádná pole jako neplatnou.

   [!code-csharp[](validation/sample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. Přidejte následující kód k `Startup.ConfigureServices` nahrazení výchozí `IObjectModelValidator` implementace v kontejneru vkládání závislostí.

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_DisableValidation)]

Pořád se můžou zobrazit chyby stavu modelu, které pocházejí z vazby modelu.

## <a name="client-side-validation"></a>Ověřování na straně klienta

Ověřování na straně klienta brání odeslání, dokud není formulář platný. Tlačítko Odeslat spustí JavaScript, který buď odešle formulář, nebo zobrazí chybové zprávy.

Ověřování na straně klienta zabrání zbytečnému přenosu na server, pokud dojde k chybám vstupu na formuláři. Následující odkazy skriptu v *_Layout. cshtml* a *_ValidationScriptsPartial. cshtml* podporují ověřování na straně klienta:

[!code-cshtml[](validation/sample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

Skript [jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) nenáročného ověřování je vlastní knihovna front-end Microsoftu, která se vytváří na oblíbený modul plug-in [jQuery pro ověření](https://jqueryvalidation.org/) . Bez nenáročného ověřování by bylo nutné kód stejné ověřovací logiky nakódovat na dvou místech: jednou v atributech ověřování na straně serveru u vlastností modelu a pak znovu v skriptech na straně klienta. Místo toho [můžou pomocníky značek](xref:mvc/views/tag-helpers/intro) a [nápovědu HTML](xref:mvc/views/overview) používat atributy ověřování a metadata typu z vlastností modelu k vykreslování atributů HTML 5 `data-` pro prvky formuláře, které vyžadují ověření. jQuery nenáročné ověřování analyzuje `data-` atributy a předá logiku do příkazu jQuery Validate a efektivně kopíruje logiku ověřování na straně serveru do klienta. Chyby ověřování můžete zobrazit na klientovi pomocí značek pomocníka, jak je znázorněno zde:

[!code-cshtml[](validation/sample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

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

Všimněte si, `data-` že atributy ve výstupu HTML odpovídají atributům ověřování `ReleaseDate` pro vlastnost. `data-val-required` Atribut obsahuje chybovou zprávu, která se zobrazí, pokud uživatel neplní pole Datum vydání. jQuery unpassing předá tuto hodnotu metodě jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) , která pak zobrazí tuto zprávu v doprovodném  **\<elementu span >** .

Ověřování datového typu je založené na typu .NET vlastnosti, pokud není přepsána `[DataType]` atributem. Prohlížeče mají vlastní výchozí chybové zprávy, ale tyto zprávy můžou potlačit ověření jQuery nenáročná ověřovací balíček. `[DataType]`atributy a podtřídy, jako je `[EmailAddress]` například umožňuje zadat chybovou zprávu.

### <a name="add-validation-to-dynamic-forms"></a>Přidání ověřování do dynamických formulářů

jQuery – neúspěšné ověření projde logiku ověření a parametry, které se při prvním načtení stránky ověřují. Proto ověřování nefunguje automaticky na dynamicky generovaných formulářích. Chcete-li povolit ověřování, řekněte jQuery nenápadu ověřování, aby se dynamický formulář analyzoval ihned po jeho vytvoření. Například následující kód nastaví ověřování na straně klienta na formuláři přidaném prostřednictvím jazyka AJAX.

```js
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

Metoda funguje na celém formuláři, nikoli na jednotlivých dynamicky generovaných ovládacích prvcích, `<input>` například a `<select/>`. `$.validator.unobtrusive.parse()` Chcete-li znovu analyzovat formulář, odeberte data ověřování, která byla přidána při analýze formuláře dříve, jak je znázorněno v následujícím příkladu:

```js
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

Vlastní ověřování na straně klienta se provádí generováním `data-` atributů HTML, které fungují s vlastním ověřovacím adaptérem jQuery. Následující vzorový kód adaptéru byl napsán pro `ClassicMovie` atributy a `ClassicMovie2` , které byly představeny dříve v tomto článku:

[!code-javascript[](validation/sample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

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

Jak bylo uvedeno dříve, [pomocníkům značek](xref:mvc/views/tag-helpers/intro) a značkám [HTML](xref:mvc/views/overview) se při vykreslování `data-` atributů používají informace z atributů ověřování. Existují dvě možnosti pro psaní kódu, který je výsledkem vytváření vlastních `data-` atributů HTML:

* Vytvořte třídu, která je odvozena `AttributeAdapterBase<TAttribute>` z třídy a třídu, `IValidationAttributeAdapterProvider`která implementuje, a zaregistrujte svůj atribut a jeho adaptér v di. Tato metoda následuje za [instančním objektem zodpovědnosti](https://wikipedia.org/wiki/Single_responsibility_principle) v tomto ověřovacím kódu souvisejícím se serverem a klientem je v samostatných třídách. Adaptér má také výhodu, že protože je zaregistrován v DI, jsou v případě potřeby k dispozici jiné služby v DI.
* Implementujte `IClientModelValidator` ve `ValidationAttribute` své třídě. Tato metoda může být vhodná, pokud atribut neprovádí žádné ověřování na straně serveru a nepotřebuje žádné služby od DI.

### <a name="attributeadapter-for-client-side-validation"></a>AttributeAdapter pro ověřování na straně klienta

Tato metoda vykreslování `data-` atributů ve formátu HTML je používána `ClassicMovie` atributem v ukázkové aplikaci. Přidání ověřování klientů pomocí této metody:

1. Vytvořte třídu adaptéru atributů pro vlastní ověřovací atribut. Odvodit třídu z [AttributeAdapterBase\<T >](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2). Vytvořte metodu, která přidá `data-` atributy do vykresleného výstupu, jak je znázorněno v následujícím příkladu: `AddValidation`

   [!code-csharp[](validation/sample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. Vytvořte třídu poskytovatele adaptéru, která implementuje <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>. `GetAttributeAdapter` V metodě předejte vlastní atribut konstruktoru adaptéru, jak je znázorněno v následujícím příkladu:

   [!code-csharp[](validation/sample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. Zaregistrujte poskytovatele adaptéru pro DI `Startup.ConfigureServices`v:

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>IClientModelValidator pro ověřování na straně klienta

Tato metoda vykreslování `data-` atributů ve formátu HTML je používána `ClassicMovie2` atributem v ukázkové aplikaci. Přidání ověřování klientů pomocí této metody:

* Ve vlastním ověřovacím atributu implementujte `IClientModelValidator` rozhraní a `AddValidation` vytvořte metodu. V metodě přidejte `data-` atributy pro ověření, jak je znázorněno v následujícím příkladu: `AddValidation`

  [!code-csharp[](validation/sample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a>Zakázat ověřování na straně klienta

Následující kód zakáže ověřování klienta v zobrazeních MVC:

[!code-csharp[](validation/sample_snapshot/Startup2.cs?name=snippet_DisableClientValidation)]

A v Razor Pages:

[!code-csharp[](validation/sample_snapshot/Startup3.cs?name=snippet_DisableClientValidation)]

Další možností pro zakázání ověřování klienta je přidat komentář k odkazu do `_ValidationScriptsPartial` souboru *. cshtml* .

## <a name="additional-resources"></a>Další zdroje

* [Obor názvů System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations)
* [Vazby modelu](model-binding.md)
