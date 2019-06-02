---
title: Ověření modelu v ASP.NET Core MVC
author: tdykstra
description: Další informace o ověření modelu v ASP.NET Core MVC a stránky Razor.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
monikerRange: '>= aspnetcore-2.1'
uid: mvc/models/validation
ms.openlocfilehash: 9737e45729b4e5abd9a33824c4d6610ca21681c0
ms.sourcegitcommit: c5339594101d30b189f61761275b7d310e80d18a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/02/2019
ms.locfileid: "66458475"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a>Ověření modelu v ASP.NET Core MVC a stránky Razor

Tento článek vysvětluje, jak ověřit vstup uživatele v aplikaci ASP.NET Core MVC nebo stránky Razor.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([stažení](xref:index#how-to-download-a-sample)).

## <a name="model-state"></a>Stav modelu

Stav modelu reprezentuje chyby, které pocházejí z dvou subsystémů: vazby modelu a ověření modelu. Chyby, které pocházejí z [vazby modelu](model-binding.md) jsou obecně chyby převodu dat (například "x" je zadáno v poli, která očekává celé číslo). Ověření modelu dojde poté, co vazby modelu a sestavy chyb, pokud data neodpovídají obchodní pravidla (například 0 je zadáno v poli, která očekává hodnocení 1 až 5).

Vazby modelu a ověření dojít před spuštěním akce kontroleru nebo metodu obslužné rutiny pro stránky Razor. Pro web apps, zodpovídá aplikace ke kontrole `ModelState.IsValid` a reagují odpovídajícím způsobem. Webové aplikace obvykle opětovné zobrazení na stránce s chybovou zprávou:

[!code-csharp[](validation/sample_snapshot/Create.cshtml.cs?name=snippet&highlight=3-6)]

Kontrolerů webového rozhraní API není potřeba zkontrolovat `ModelState.IsValid` případě, že mají `[ApiController]` atribut. Automatické HTTP 400 odpověď obsahující podrobnosti o problému v takovém případě je vrácena, když neplatí stavu modelu. Další informace najdete v tématu [odpovědi HTTP 400 automatické](xref:web-api/index#automatic-http-400-responses).

## <a name="rerun-validation"></a>Znovu spustit ověření

Ověření je automatická, ale můžete zopakovat ručně. Například může vypočítat hodnotu pro vlastnost a chcete znovu spusťte ověření po nastavení vlastnosti vypočtená hodnota. Chcete-li znovu spustit ověřování, zavolejte `TryValidateModel` způsob, jak je znázorněno zde:

[!code-csharp[](validation/sample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a>Ověřování atributů

Ověřování atributů umožňují zadat ověřovacích pravidel pro vlastnosti modelu. Následující příklad z [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ukazuje třídu modelu, která je označena s atributy ověření. `[ClassicMovie]` Atribut vlastního ověřovacího atributu a ostatní jsou integrované. (Není vidět je `[ClassicMovie2]`, který ukazuje alternativní způsob, jak implementovat vlastní atribut.)

[!code-csharp[](validation/sample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a>Vestavěné atributy

Tady jsou některé atributy ověření integrované:

* `[CreditCard]`: Ověří, zda má vlastnost formátu platební karty.
* `[Compare]`: Ověřuje, že dvě vlastnosti v modelu shoda.
* `[EmailAddress]`: Ověří, zda vlastnost má formát e-mailu.
* `[Phone]`: Ověří, zda vlastnost má telefonní číslo formátu.
* `[Range]`: Ověřuje, že hodnota vlastnosti spadají do zadaného rozsahu.
* `[RegularExpression]`: Ověřuje, že hodnota vlastnosti odpovídá zadanému regulárnímu výrazu.
* `[Required]`: Ověřuje, že pole není null. Zobrazit [[povinné] atribut](#required-attribute) podrobné informace o chování tohoto atributu.
* `[StringLength]`: Ověřuje, že hodnota vlastnosti řetězce nepřekračuje omezení délky.
* `[Url]`: Ověří, zda vlastnost má formát adresy URL.
* `[Remote]`: Ověřuje vstup v klientovi po zavolání metody akce na serveru. V tématu [[vzdálené] atribut](#remote-attribute) podrobné informace o chování tohoto atributu.

Úplný seznam atributů ověřování najdete v [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) oboru názvů.

### <a name="error-messages"></a>Chybové zprávy

Ověřování atributů umožňují nastavit chybovou zprávu, který se má zobrazit pro neplatný vstup. Příklad:

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

Interně jsou volání atributy `String.Format` s zástupný symbol pro název pole a zástupné symboly někdy další. Příklad:

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

Při použití `Name` vlastnost, chybová zpráva vytvořili v předchozím kódu by být "Délka názvu musí být mezi 6 a 8.".

Chcete zjistit, jaké parametry jsou předány `String.Format` konkrétní atribut chybovou zprávu najdete v článku [DataAnnotations zdrojový kód](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).

## <a name="required-attribute"></a>[Povinné] atributu

Ve výchozím nastavení, ověření systém zpracovává null parametry nebo vlastnosti jako by měly `[Required]` atribut. [Typy hodnot](/dotnet/csharp/language-reference/keywords/value-types) například `decimal` a `int` jsou null.

### <a name="required-validation-on-the-server"></a>[Povinné] ověření na serveru

Na serveru požadovaná hodnota se považuje za chybějící Pokud vlastnost má hodnotu null. Null pole je vždy platné, a nikdy se zobrazí chybová zpráva atribut [povinné].

Ale model vazby pro vlastnost se zakázanou můžou selhat, a vytvářet v chybové zprávě, jako `The value '' is invalid`. Pokud chcete zadat vlastní chybovou zprávu pro ověřování na straně serveru Null typů, máte následující možnosti:

* Nastavit pole s možnou hodnotou Null (například `decimal?` místo `decimal`). [S povolenou hodnotou Null\<T >](/dotnet/csharp/programming-guide/nullable-types/) typy hodnot jsou považovány za standardních typů s povolenou hodnotou Null.
* Nastavit výchozí chybovou zprávu pro vazbu modelu, jak je znázorněno v následujícím příkladu:

  [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  Další informace o chybách vazby modelu, můžete nastavit výchozí zprávy pro, najdete v části <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.

### <a name="required-validation-on-the-client"></a>[Povinné] ověření na straně klienta

Typy neumožňující a řetězce jsou zpracovány jinak na straně klienta na server v porovnání. Na straně klienta:

* Hodnota se považuje za k dispozici pouze v případě, že jsou zadány pro něj. Proto ověřování na straně klienta zpracovává typy neumožňující stejný jako typů s povolenou hodnotou Null.
* Prázdné znaky na pole řetězce se považuje za platný vstup pomocí jQuery ověření [požadované](https://jqueryvalidation.org/required-method/) metody. Ověřování na straně serveru bude považovat za pole vyžaduje řetězec neplatný Pokud pouze zadané prázdné znaky.

Jak bylo uvedeno dříve, NULL typy se považují jako by se měli `[Required]` atribut. To znamená, že získáte ověřování na straně klienta i v případě, že jste se nevztahují `[Required]` atribut. Ale pokud nechcete použít atribut, můžete získat výchozí chybovou zprávu. Pokud chcete zadat vlastní chybové zprávy, použijte atribut.

## <a name="remote-attribute"></a>Atribut [vzdálené]

`[Remote]` Atribut implementuje ověření na straně klienta, který vyžaduje volání metody na serveru k určení, zda vstupní pole je platný. Aplikace může třeba ověřit, zda uživatelské jméno je již používán.

Implementace vzdáleného ověřování:

1. Vytvořte pro JavaScript volat metodu akce.  JQuery ověřit [vzdálené](https://jqueryvalidation.org/remote-method/) metoda očekává odpověď JSON:

   * `"true"` znamená to, že se vstupní data platná.
   * `"false"`, `undefined`, nebo `null` znamená, že vstup je neplatný.  Zobrazte výchozí chybovou zprávu.
   * Jakýkoli jiný řetězec znamená, že vstup je neplatný. Zobrazí řetězec jako vlastní chybovou zprávu.

   Tady je příklad metody akce, která vrací vlastní chybová zpráva:

   [!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. V třídě modelu poznámky vlastnost s `[Remote]` atribut, který odkazuje na metodu akce ověření, jak je znázorněno v následujícím příkladu:

   [!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   `[Remote]` Atribut je `Microsoft.AspNetCore.Mvc` oboru názvů. Nainstalujte [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) balíčku NuGet, pokud nepoužíváte `Microsoft.AspNetCore.App` nebo `Microsoft.AspNetCore.All` Microsoft.aspnetcore.all.
   
### <a name="additional-fields"></a>Další pole

`AdditionalFields` Vlastnost `[Remote]` atribut umožňuje ověření kombinace různých typů polí s daty na serveru. Například pokud `User` měl modelů `FirstName` a `LastName` vlastností, můžete chtít ověřit, že stávající uživatelé už nemají tohoto páru názvy. Následující příklad ukazuje, jak používat `AdditionalFields`:

[!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserNameProperties)]

`AdditionalFields` může být explicitně nastaveno na řetězce `"FirstName"` a `"LastName"`, ale pomocí [ `nameof` ](/dotnet/csharp/language-reference/keywords/nameof) operátor zjednodušuje později refaktoring. Metoda akce pro toto ověření musí přijmout název první a poslední název argumenty:

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyName)]

Pokud uživatel zadá název první nebo poslední, JavaScript zavolá vzdálené zobrazíte, pokud je už zabraný tohoto páru názvy.

K ověření nejmíň dva další pole, zadejte je jako seznam oddělený čárkami. Například, chcete-li přidat `MiddleName` nastavena vlastnost modelu, `[Remote]` atributu, jak je znázorněno v následujícím příkladu:

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`, jako jsou všechny argumenty atributu musí být konstantní výraz. Proto nepoužívejte [interpolovaný řetězec](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> inicializovat `AdditionalFields`.

## <a name="alternatives-to-built-in-attributes"></a>Alternativy k vestavěné atributy

Pokud potřebujete ověření integrované atributy není zadaný, můžete:

* [Vytvořit vlastní atributy](#custom-attributes).
* [Implementace IValidatableObject](#ivalidatableobject).

## <a name="custom-attributes"></a>Vlastní atributy

Pro scénáře, které atributy ověření integrované nezpracovávají můžete vytvořit vlastní ověřovací atributy. Vytvořte třídu, která dědí z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>a přepsat <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> metody.

`IsValid` Metoda přijímá objekt s názvem *hodnota*, což je vstup, který má být ověřen. Přetížení přijímá také `ValidationContext` objektu, který poskytuje dodatečné informace, jako je například instance modelu, která vytvořil vazby modelu.

Následující příklad ověří, jestli datum vydání verze pro video v *Classic* žánr není novější než zadaného roku. `[ClassicMovie2]` Atribut nejprve zkontroluje žánr a pokračuje pouze pokud má *Classic*. Identifikuje jako klasické filmů zkontroluje datum vydání, abyste měli jistotu, že není novější než limit předána do konstruktoru atributu.)

[!code-csharp[](validation/sample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

`movie` Proměnná v předchozím příkladu představuje `Movie` objekt, který obsahuje data z odeslání formuláře. `IsValid` Metoda zkontroluje data a rozšířením podle tematických. Po úspěšném ověření `IsValid` vrátí `ValidationResult.Success` kódu. Když se ověřování nezdaří, `ValidationResult` s chybou je vrácená zpráva.

## <a name="ivalidatableobject"></a>IValidatableObject

Předchozí příklad pracuje pouze s `Movie` typy. Další možností pro ověřování na úrovni třídy je implementovat `IValidatableObject` v třídě modelu, jak je znázorněno v následujícím příkladu:

[!code-csharp[](validation/sample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>Uzel nejvyšší úrovně ověření

Uzly nejvyšší úrovně patří:

* Parametry akce
* Vlastnosti kontroleru
* Parametry rutiny stránky
* Vlastnosti modelu stránky

Model vázaný uzly nejvyšší úrovně se ověří kromě ověřování vlastnosti modelu. V následujícím příkladu z ukázkové aplikace `VerifyPhone` metoda používá <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> k ověření `phone` parametr akce:

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

Uzly nejvyšší úrovně můžete použít <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověření. V následujícím příkladu z ukázkové aplikace `CheckAge` metody Určuje, že `age` parametr musí být vázán z řetězce dotazu, když se odešle formulář:

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_CheckAge)]

Na stránce Zkontrolovat stáří (*CheckAge.cshtml*), existují dva typy. První formulář odešle `Age` hodnotu `99` jako řetězec dotazu: `https://localhost:5001/Users/CheckAge?Age=99`.

Když správně formátovaná `age` parametr z řetězce dotazu se odešle, ověří formuláře.

Druhý formulář na stránce Zkontrolovat stáří odešle `Age` hodnoty v těle žádosti a ověření selže. Vazba se nezdaří, protože `age` parametr musí pocházet z řetězce dotazu.

Při spuštění s `CompatibilityVersion.Version_2_1` nebo novější, je ve výchozím nastavení povoleno ověření uzel nejvyšší úrovně. V opačném případě ověření uzel nejvyšší úrovně je zakázané. Výchozí možnost můžete přepsat tak, že nastavíte <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> vlastnost v (`Startup.ConfigureServices`), jak je znázorněno zde:

[!code-csharp[](validation/sample_snapshot/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a>Maximální počet chyb

Ověření zastaví, když je dosaženo maximálního počtu chyb (200 ve výchozím nastavení). Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices`:

[!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a>Maximální povolený počet rekurzí

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> prochází graf objektu model ověřován. U modelů, které jsou velmi podrobné nebo neomezeně rekurzivní ověření může způsobit přetečení zásobníku. [MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) poskytuje způsob, jak zastavit již v rané fázi ověřování, pokud překročí nakonfigurovanou hloubku rekurze návštěvníka. Výchozí hodnota `MvcOptions.MaxValidationDepth` je 200 při spuštění s `CompatibilityVersion.Version_2_2` nebo novější. U starších verzí hodnota je null, což znamená, že žádná omezení hloubky.

## <a name="automatic-short-circuit"></a>Automatické zkrácenou

Ověření se automaticky zkratována (vynechaný) Pokud graf modelu nevyžaduje ověření. Objekty, které modul runtime přeskočí ověřování zahrnout kolekce primitivních elementů (například `byte[]`, `string[]`, `Dictionary<string, string>`) a komplexní objekt, grafy, které nemají žádné validátory.

## <a name="disable-validation"></a>Zakázat ověřování

Postup při zakázání ověření:

1. Vytvořte implementaci třídy `IObjectModelValidator` všechna pole, která není označit jako neplatný.

   [!code-csharp[](validation/sample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. Přidejte následující kód, který `Startup.ConfigureServices` nahradit výchozí `IObjectModelValidator` implementace v kontejneru pro vkládání závislostí.

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_DisableValidation)]

Stále se může zobrazit chyby stavu modelu, které pocházejí z vazby modelu.

## <a name="client-side-validation"></a>Ověřování na straně klienta

Ověřování na straně klienta zabrání odeslání dokud formulář není platný. Tlačítka pro odeslání spustí jazyka JavaScript, který se odešle formulář nebo zobrazí chybové zprávy.

Ověřování na straně klienta se vyhnete zbytečným odezvy serveru když jsou vstupní chyby ve formuláři. Následující skript se odkazuje v *_Layout.cshtml* a *_ValidationScriptsPartial.cshtml* podporují ověřování na straně klienta:

[!code-cshtml[](validation/sample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

[JQuery Nerušivý ověření](https://github.com/aspnet/jquery-validation-unobtrusive) skript je vlastní Microsoft front-endu knihovnu, která staví na oblíbené [jQuery ověřit](https://jqueryvalidation.org/) modulu plug-in. Bez jQuery Nerušivý ověřování, bude muset kód stejnou logiku ověřování na dvou místech: jednou v atributech ověřování na straně serveru na vlastnosti projektu a pak znovu ve skriptech na straně klienta. Místo toho [pomocných rutin značek](xref:mvc/views/tag-helpers/intro) a [pomocných rutin HTML](xref:mvc/views/overview) atributů ověření a zadejte metadata z vlastnosti modelu k vykreslení HTML 5 `data-` atributy prvků formuláře, které vyžadují ověřování. analyzuje Nerušivý ověřování jQuery `data-` atributy a předá logiku jQuery ověřit efektivně "kopírování" logiku ověřování na straně serveru do klienta. Zobrazení chyb ověřování na straně klienta, použití pomocných rutin značek, jak je znázorněno zde:

[!code-cshtml[](validation/sample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

Předchozí pomocných rutin značek vykreslení následující kód HTML.

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

Všimněte si, že `data-` atributů v kódu HTML výstup odpovídají atributů ověření pro `ReleaseDate` vlastnost. `data-val-required` Atribut obsahuje chybovou zprávu se zobrazí, pokud uživatel nemá vyplnit pole Datum vydání verze. jQuery Nerušivý ověření tuto hodnotu předá jQuery ověřit [ `required()` ](https://jqueryvalidation.org/required-method/) metodu, která se zobrazí zpráva v souvisejícím  **\<span >** elementu.

Ověření typu dat vychází .NET typu vlastnosti, pokud není, který je přepsán `[DataType]` atribut. Prohlížečů mají své vlastní výchozí chybové zprávy, ale jQuery ověření Nerušivého ověření balíček můžete přepsat tyto zprávy. `[DataType]` atributy a podtříd, jako `[EmailAddress]` umožňují nastavit chybovou zprávu.

### <a name="add-validation-to-dynamic-forms"></a>Přidání ověřování pro dynamické formuláře

jQuery Nerušivý ověření předá logiku ověřování a parametry jQuery ověřit při prvním načtení stránky. Ověření proto nebude fungovat automaticky v dynamicky generovaném formuláři. Pokud chcete povolit ověřování, řekněte jQuery Nerušivý ověření analyzovat dynamická podoba ihned po jeho vytvoření. Například následující kód nastaví ověřování na straně klienta ve formuláři přidány prostřednictvím AJAX.

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

`$.validator.unobtrusive.parse()` Metoda přijímá selektor jQuery pro svůj argument. Tato metoda říká jQuery Nerušivý ověření analyzovat `data-` atributy formulářů v rámci tohoto selektoru. Hodnoty tyto atributy jsou potom předány do modulu plug-in jQuery ověřit.

### <a name="add-validation-to-dynamic-controls"></a>Přidání ověřování pro dynamické ovládací prvky

`$.validator.unobtrusive.parse()` Metoda se dá použít na celý formulář, nikoli na jednotlivé dynamicky generované ovládací prvky, jako například `<input>` a `<select/>`. Chcete-li rozboru formuláře, odeberte ověřovací data, která se přidal, když formulář byl analyzován dříve, jak je znázorněno v následujícím příkladu:

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

Vlastní ověřování na straně klienta se provádí pomocí generování `data-` atributy HTML, které pracují s adaptérem ověřit vlastní jQuery. Následující ukázkový kód adaptéru bylo napsáno pro `ClassicMovie` a `ClassicMovie2` atributy, které byly zavedeny dříve v tomto článku:

[!code-javascript[](validation/sample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

Informace o tom, jak psát adaptéry najdete v tématu [jQuery ověřit dokumentaci](http://jqueryvalidation.org/documentation/).

Použít adaptér pro dané pole aktivuje `data-` atributy, které:

* Označit pole jako neprošla ověřením (`data-val="true"`).
* Identifikace ověření pravidlo název a chyba text zprávy (třeba `data-val-rulename="Error message."`).
* Zadejte další parametry validátor potřebuje (například `data-val-rulename-parm1="value"`).

Následující příklad ukazuje `data-` atributy pro ukázkovou aplikaci `ClassicMovie` atribut:

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

Jak bylo uvedeno dříve, [pomocných rutin značek](xref:mvc/views/tag-helpers/intro) a [pomocných rutin HTML](xref:mvc/views/overview) pomocí informací z atributů ověření pro vykreslení `data-` atributy. Existují dvě možnosti pro psaní kódu, který má za následek vytvoření vlastní `data-` atributy HTML:

* Vytvořte třídu, která je odvozena z `AttributeAdapterBase<TAttribute>` a třídu, která implementuje `IValidationAttributeAdapterProvider`a registrace v DI atribut a jeho adaptéru. Tato metoda odpovídá [jednotnou zodpovědnost hlavní](https://wikipedia.org/wiki/Single_responsibility_principle) v tom, že je kód pro ověření související se server a klienta v samostatné třídy. Adaptér má také využívat, protože je registrován v DI, ostatní služby v DI jsou k dispozici v případě potřeby.
* Implementace `IClientModelValidator` ve vašich `ValidationAttribute` třídy. Tato metoda může být vhodné, pokud atribut neprovádí žádné ověřování na straně serveru a není nutné žádné služby z DI.

### <a name="attributeadapter-for-client-side-validation"></a>AttributeAdapter pro ověřování na straně klienta

Tato metoda vykreslování `data-` atributy ve formátu HTML je používán `ClassicMovie` atribut v ukázkové aplikaci. Přidání ověřování na straně klienta pomocí této metody:

1. Vytvořte třídu atributu adaptér pro atribut vlastní ověřování. Odvodit třídu z [AttributeAdapterBase\<T >](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2). Vytvoření `AddValidation` metodu, která přidá `data-` atributů vykresleného výstupu, jak je znázorněno v tomto příkladu:

   [!code-csharp[](validation/sample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. Vytvořit třídu adaptéru poskytovatele, který implementuje <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>. V `GetAttributeAdapter` metoda předat ve vlastním atributu je adaptér konstruktoru, jak je znázorněno v tomto příkladu:

   [!code-csharp[](validation/sample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. Zaregistrujte adaptér poskytovatele pro DI v `Startup.ConfigureServices`:

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>IClientModelValidator pro ověřování na straně klienta

Tato metoda vykreslování `data-` atributy ve formátu HTML je používán `ClassicMovie2` atribut v ukázkové aplikaci. Přidání ověřování na straně klienta pomocí této metody:

* V atributu vlastní ověřovací implementovat `IClientModelValidator` rozhraní a vytvořit `AddValidation` metody. V `AddValidation` metodu, přidejte `data-` atributů pro ověření, jak je znázorněno v následujícím příkladu:

  [!code-csharp[](validation/sample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a>Zakázat ověřování na straně klienta

Následující kód zakazuje ověření klienta v zobrazení MVC:

[!code-csharp[](validation/sample_snapshot/Startup2.cs?name=snippet_DisableClientValidation)]

A v stránky Razor:

[!code-csharp[](validation/sample_snapshot/Startup3.cs?name=snippet_DisableClientValidation)]

Další možností pro zakázání ověřování na straně klienta je odkaz na komentář `_ValidationScriptsPartial` ve vašich *.cshtml* souboru.

## <a name="additional-resources"></a>Další zdroje

* [Obor názvů System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations)
* [Vazby modelu](model-binding.md)
