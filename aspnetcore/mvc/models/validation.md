---
title: Ověření modelu v ASP.NET Core MVC
author: tdykstra
description: Další informace o ověření modelu v ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2019
uid: mvc/models/validation
ms.openlocfilehash: 7c8255097dfc72480794930ebe4d6cb568edbd7c
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396191"
---
# <a name="model-validation-in-aspnet-core-mvc"></a>Ověření modelu v ASP.NET Core MVC

Podle [Rachel Appel](https://github.com/rachelappel)

## <a name="introduction-to-model-validation"></a>Úvod k ověření modelu

Předtím, než aplikace ukládá data do databáze, aplikace musí ověřit data. Data musí být kontrolované potenciálních bezpečnostních hrozeb, ověřit, že je správně naformátovaný podle typu a velikosti a musí odpovídat pravidla. Ověření je nezbytné, i když může být redundantní chybám a únavná implementovat. V aplikaci MVC se stane ověření klienta i serveru.

Naštěstí má .NET abstrahovaná ověření do atributů ověření. Tyto atributy obsahují kód pro ověření, a tím snižuje množství kódu, který musíte napsat.

V ASP.NET Core 2.2 a novější modul runtime ASP.NET Core zkratům ověření (přeskočí), pokud můžete určit, že daný model grafu nevyžaduje ověření. Přeskočení ověření může poskytnout výrazné zlepšení výkonu při ověřování modelů, které nelze nebo nemáte žádné přidružené validátorů. Bylo přeskočeno ověření zahrnuje objekty, jako jsou kolekcí primitivních elementů (`byte[]`, `string[]`, `Dictionary<string, string>`atd), nebo komplexního objektu grafy bez žádné validátory.

[Zobrazit nebo stáhnout ukázky z Githubu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample).

## <a name="validation-attributes"></a>Ověřování atributů

Ověřování atributů představují způsob, jak nakonfigurovat ověřování modelu, tak, aby se koncepčně podobá ověření na pole v databázové tabulky. To zahrnuje omezení, jako je například přiřazení datových typů nebo povinná pole. Jiné typy ověření, které zahrnuje použití vzorců k datům a vynucování obchodních pravidel, jako je například platební kartu, telefonní číslo nebo e-mailovou adresu. Atributy ověření provést vynucují požadavky pro tyto mnohem jednodušší a usnadňuje používání.

Jsou zadané atributy ověření na úrovni vlastnost: 

```csharp
[Required]
public string MyProperty { get; set; }
```

Níže je s poznámkami `Movie` modelů z aplikace, která uchovává informace o filmů a televizních pořadů. Většina vlastností jsou povinné a několik vlastností řetězce mají požadavky na délku. Kromě toho je omezení číselného rozsahu v místě `Price` vlastnost od 0 do $999,99, spolu s vlastní ověřovací atribut.

[!code-csharp[](validation/sample/Movie.cs?range=6-29)]

Pouze pro čtení přes model odhalí pravidla týkající se data pro tuto aplikaci, což usnadňuje údržbu kódu. Níže jsou uvedeny několik oblíbených ověření integrované atributy:

* `[CreditCard]`: Ověří vlastnost má formát platební karty.

* `[Compare]`: Ověří dvě vlastnosti v modelu shoda.

* `[EmailAddress]`: Ověří vlastnost má formát e-mailu.

* `[Phone]`: Ověří vlastnost má formát telefonu.

* `[Range]`: Ověří spadá hodnotu vlastnosti v daném rozsahu.

* `[RegularExpression]`: Ověřuje, že data odpovídá zadanému regulárnímu výrazu.

* `[Required]`: Díky požadované vlastnosti.

* `[StringLength]`: Ověřuje, že řetězcovou vlastnost má nejvíce dané maximální délky.

* `[Url]`: Ověří vlastnost má formát adresy URL.

MVC podporuje všechny atributy, které je odvozena z `ValidationAttribute` pro účely ověření. Mnoho užitečných ověřování atributů najdete v [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) oboru názvů.

Může existovat instance, kdy potřebujete víc funkcí než vestavěné atributy. Pro situace, můžete vytvořit vlastní ověřovací atributy odvozený od `ValidationAttribute` nebo změna modelu k implementaci `IValidatableObject`.

## <a name="notes-on-the-use-of-the-required-attribute"></a>Poznámky k použití požadovaný atribut

Neumožňující [typů hodnot](/dotnet/csharp/language-reference/keywords/value-types) (například `decimal`, `int`, `float`, a `DateTime`) jsou ze své podstaty povinné a nemusíte `Required` atribut. Aplikace provádí žádné kontroly ověřování na straně serveru pro typy neumožňující, které jsou označeny `Required`.

Vazby modelu MVC, která se týká ověření a atributů ověření, zamítne formuláře pole obsahující chybí hodnota nebo prázdné znaky Null typu. Chybí `BindRequired` atribut na cílovou vlastnost vazby modelu přeskočí chybějící data pro typy neumožňující, ve kterém chybí pole formuláře z příchozí data formuláře.

[BindRequired atribut](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.bindrequiredattribute) (viz také <xref:mvc/models/model-binding#customize-model-binding-behavior-with-attributes>) je užitečný k zajištění toho, dokončení data formuláře. Při použití na vlastnost, systém vazby modelu vyžaduje hodnotu pro tuto vlastnost. Při použití na typ, systém vazby modelu vyžaduje hodnoty pro všechny vlastnosti tohoto typu.

Při použití [Nullable\<T > typ](/dotnet/csharp/programming-guide/nullable-types/) (například `decimal?` nebo `System.Nullable<decimal>`) a označte ji `Required`, se provádí kontroly ověřování na straně serveru, jako kdyby byly standardní typ připouštějící hodnotu Null (pro vlastnost například `string`).

Ověřování na straně klienta vyžaduje hodnotu pro pole formuláře, který odpovídá vlastnosti modelu, který jste označili `Required` a Null typu vlastnosti, která nejsou označena `Required`. `Required` můžete použít k řízení chybovou zprávu ověření na straně klienta.

::: moniker range=">= aspnetcore-2.1"

## <a name="top-level-node-validation"></a>Uzel nejvyšší úrovně ověření

Uzly nejvyšší úrovně patří:

* Parametry akce
* Vlastnosti kontroleru
* Parametry rutiny stránky
* Vlastnosti modelu stránky

Model vázaný uzly nejvyšší úrovně se ověří kromě ověřování vlastnosti modelu. V následujícím příkladu z ukázkové aplikace `VerifyPhone` metoda používá <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> ověřit data uživatele do pole Telefon formuláře:

[!code-csharp[](validation/sample/UsersController.cs?name=snippet_VerifyPhone)]

Uzly nejvyšší úrovně můžete použít <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověření. V následujícím příkladu z ukázkové aplikace `CheckAge` metody Určuje, že `age` parametr musí být vázán z řetězce dotazu, když se odešle formulář:

[!code-csharp[](validation/sample/UsersController.cs?name=snippet_CheckAge)]

Na stránce Zkontrolovat stáří (*CheckAge.cshtml*), existují dva typy. První formulář odešle `Age` hodnotu `99` jako řetězec dotazu: `https://localhost:5001/Users/CheckAge?Age=99`.

Když správně formátovaná `age` parametr z řetězce dotazu se odešle, ověří formuláře.

Druhý formulář na stránce Zkontrolovat stáří odešle `Age` hodnoty v těle žádosti a ověření selže. Vazba se nezdaří, protože `age` parametr musí pocházet z řetězce dotazu.

Ověření je ve výchozím nastavení povolené a řídí <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> vlastnost <xref:Microsoft.AspNetCore.Mvc.MvcOptions>. Chcete-li zakázat ověřování uzel nejvyšší úrovně, nastavte `AllowValidatingTopLevelNodes` k `false` v možnostech MVC (`Startup.ConfigureServices`):

[!code-csharp[](validation/sample_snapshot/Startup.cs?name=snippet_AddMvc&highlight=4)]

::: moniker-end

## <a name="model-state"></a>Stav modelu

Stav modelu představuje chyb při ověřování v zadané hodnoty formuláře HTML.

MVC bude pokračovat v ověřování polí, dokud nedosáhne maximální počet chyb (200 ve výchozím nastavení). Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices`:

[!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors)]

## <a name="handle-model-state-errors"></a>Chyby stavu modelu popisovač

Vyvolá se před spuštěním akce kontroleru ověření modelu. Je zodpovědností akce ke kontrole `ModelState.IsValid` a reagují odpovídajícím způsobem. V mnoha případech odpovídající reakce je reakce na chybu, ideálně s podrobnostmi o příčině selhání ověření modelu.

::: moniker range=">= aspnetcore-2.1"

Když `ModelState.IsValid` vyhodnotí jako `false` v kontrolerů webového rozhraní API pomocí `[ApiController]` atribut, je vrácena automatické odpovědi HTTP 400 obsahující podrobnosti o problému. Další informace najdete v tématu [odpovědi HTTP 400 automatické](xref:web-api/index#automatic-http-400-responses).

::: moniker-end

Některé aplikace zvolí dodržovat standardní konvence týkající se chyby ověření modelu, ve kterých může být případ filtr příslušné místo k implementaci těchto zásad. Měli byste otestovat chování vaše akce se stavy model platný nebo neplatný.

## <a name="manual-validation"></a>Ruční ověření

Po dokončení vazby modelu a ověření můžete opakovat jeho části. Například uživatel může zadat text v poli, očekává se celé číslo, nebo možná budete muset výpočtu hodnoty pro vlastnosti modelu.

Budete muset ručně spusťte ověření. Chcete-li tak učinit, zavolejte `TryValidateModel` způsob, jak je znázorněno zde:

[!code-csharp[](validation/sample/MoviesController.cs?name=snippet_TryValidateModel)]

## <a name="custom-validation"></a>Vlastní ověřování

Atributy ověření fungovat pro většinu ověření potřeb. Některá pravidla ověřování jsou však specifické pro vaše podnikání. Pravidla nemusí být běžná technika ověření dat, jako je zajištění pole je povinné nebo, který odpovídá rozsahu hodnot. Vlastní ověřovací atributy pro tyto scénáře jsou vynikající řešení. Vytvoření vlastní ověřovací atributy v aplikaci MVC je jednoduché. Dědit pouze z `ValidationAttribute`a přepsat `IsValid` metody. `IsValid` Metoda přijímá dva parametry, první je objekt s názvem *hodnotu* a druhá `ValidationContext` objekt s názvem *parametr validationContext*. *Hodnota* odkazuje na skutečnou hodnotu z pole, která ověřuje svůj vlastní validátor.

V následujícím příkladu obchodní pravidlo uvádí, zda uživatelé nemusí nastaveno žánr *Classic* filmu vydanou po 1960. `[ClassicMovie]` Atribut žánr nejprve zkontroluje, a pokud jde o klasický, pak zkontroluje, že je novější než 1960 datum vydání. Pokud se uvolní po 1960, ověření se nezdaří. Atribut přijímá jako parametr celé číslo představující rok, který vám pomůže ověřit data. Hodnota parametru v konstruktoru atributu, můžete zachytit, jak je znázorněno zde:

[!code-csharp[](validation/sample/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

`movie` Proměnné výše představuje `Movie` objekt, který obsahuje data z odeslání formuláře k ověření. V takovém případě ověřovací kód kontroluje data a rozšířením podle tematických v `IsValid` metodu `ClassicMovieAttribute` třídy podle pravidla. Po úspěšném ověření`IsValid` vrátí `ValidationResult.Success` kódu. Když se ověřování nezdaří, `ValidationResult` s chybou je vrácená zpráva:

[!code-csharp[](validation/sample/ClassicMovieAttribute.cs?name=snippet_GetErrorMessage)]

Když uživatel změní `Genre` pole a odešle formulář, `IsValid` metodu `ClassicMovieAttribute` ověří, zda je tento film klasický. Stejně jako všechny vestavěné atributy, použije `ClassicMovieAttribute` na vlastnost, jako `ReleaseDate` k zajištění ověření dojde, jak je znázorněno v předchozí ukázce kódu. Protože příklad funguje pouze s `Movie` typy, je vhodnější použít `IValidatableObject` jak je znázorněno v následujícím odstavci.

Alternativně může umístit tento stejný kód v modelu implementací `Validate` metodu na `IValidatableObject` rozhraní. Při vlastní ověřovací atributy fungovat dobře pro jednotlivé vlastnosti ověřování, implementace `IValidatableObject` je možné provádět ověřování na úrovni třídy, jak je vidět tady.

[!code-csharp[](validation/sample/MovieIValidatable.cs?name=snippet_Validate)]

## <a name="client-side-validation"></a>Ověřování na straně klienta

Ověřování na straně klienta je skvělé usnadnění práce pro uživatele. To šetří čas, který byste jinak strávili čekání na odezvu na server. V obchodní termíny dokonce i pár zlomky sekund vynásobené opakovaném každý den přidá až hodně času a výdajů, frustrace. Jednoduché a okamžité ověřování umožňuje uživatelům vyšší efektivity práce a vytvářet lepší kvalitu vstupu a výstupu.

Zobrazení s správné odkazy skriptu JavaScript musí mít nastavené pro ověřování na straně klienta fungovat jako tady vidíte.

[!code-cshtml[](validation/sample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml)]

[JQuery Nerušivý ověření](https://github.com/aspnet/jquery-validation-unobtrusive) skript je vlastní Microsoft front-endu knihovnu, která staví na oblíbené [jQuery ověřit](https://jqueryvalidation.org/) modulu plug-in. Bez jQuery Nerušivý ověřování, bude muset kód stejnou logiku ověřování na dvou místech: jednou v atributů ověření na straně serveru na vlastnosti projektu a poté znovu v skripty na straně klienta (příklady pro architekturu jQuery ověřením vaší [ `validate()` ](https://jqueryvalidation.org/validate/) metoda ukazuje, jak komplexní to může být). Místo toho MVC [pomocných rutin značek](xref:mvc/views/tag-helpers/intro) a [pomocných rutin HTML](xref:mvc/views/overview) budou moct používat atributy ověření a metadata z vlastnosti modelu k vykreslení HTML 5 typu [datové atributy](http://w3c.github.io/html/dom.html#embedding-custom-non-visible-data-with-the-data-attributes) v elementy formuláře, které vyžadují ověřování. MVC vygeneruje `data-` atributy pro předdefinované a vlastní atributy. pak analyzuje jQuery Nerušivý ověření `data-` atributy a předá logiku jQuery ověřit efektivně "kopírování" logiku ověřování na straně serveru do klienta. Zobrazení chyb ověřování na straně klienta, použití pomocných rutin značek relevantní, jak je znázorněno zde:

[!code-cshtml[](validation/sample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

Výše uvedené pomocných rutin značek vykreslení HTML níže. Všimněte si, že `data-` atributů v kódu HTML výstup odpovídají atributů ověření pro `ReleaseDate` vlastnost. `data-val-required` Atribut obsahuje chybovou zprávu se zobrazí, pokud uživatel nemá vyplnit pole Datum vydání verze. jQuery Nerušivý ověření tuto hodnotu předá jQuery ověřit [ `required()` ](https://jqueryvalidation.org/required-method/) metodu, která se zobrazí zpráva v souvisejícím  **\<span >** elementu.

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
                id="ReleaseDate" name="ReleaseDate" value="" />
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

Ověřování na straně klienta zabrání odeslání dokud formulář není platný. Tlačítka pro odeslání spustí jazyka JavaScript, který se odešle formulář nebo zobrazí chybové zprávy.

Určuje typ hodnoty atributů na základě .NET datového typu vlastnosti, případně přepsat pomocí MVC `[DataType]` atributy. Základní `[DataType]` atribut nemá žádné reálné serverové ověření. Prohlížeče zvolte vlastní chybové zprávy a zobrazit opravu těchto chyb, jak si přejí, ale můžete balíček ověření Nerušivého jQuery přepsat zprávy a zobrazí je konzistentní s ostatními. To se stane, když uživatelé nejvíce samozřejmě používat `[DataType]` podtřídy jako `[EmailAddress]`.

### <a name="add-validation-to-dynamic-forms"></a>Přidání ověřování pro dynamické formuláře

Protože jQuery Nerušivý ověření předá logiku ověřování a parametry jQuery ověřit při prvním načtení stránky, dynamicky generované formulářů neprojevuje automaticky ověření. Místo toho je zapotřebí sdělit jQuery Nerušivý ověření analyzovat dynamická podoba ihned po jeho vytvoření. Například následující kód ukazuje, jak můžete nastavit ověřování na straně klienta ve formuláři přidány prostřednictvím AJAX.

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

`$.validator.unobtrusive.parse()` Metoda přijímá selektor jQuery pro svůj argument. Tato metoda říká jQuery Nerušivý ověření analyzovat `data-` atributy formulářů v rámci tohoto selektoru. Hodnoty tyto atributy jsou potom předány do modulu plug-in jQuery ověřit tak, aby formuláře je třeba požadovanou na straně ověřovacích pravidel klienta atributu.

### <a name="add-validation-to-dynamic-controls"></a>Přidání ověřování pro dynamické ovládací prvky

Můžete také aktualizovat ověřovací pravidla ve formuláři, pokud jednotlivé ovládací prvky, jako `<input/>`s a `<select/>`s, generuje dynamicky. Nelze předat selektory pro tyto prvky, aby `parse()` metoda přímo protože okolní formulář již byl analyzován a nebude aktualizovat. Místo toho je nejprve odeberte stávající ověřovací data, potom rozboru celý formulář, jak je znázorněno níže:

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

## <a name="iclientmodelvalidator"></a>IClientModelValidator

Můžete vytvořit logiku na straně klienta pro vaše vlastní atribut a [nerušivý ověření](http://bradwilson.typepad.com/blog/2010/10/mvc3-unobtrusive-validation.html) která vytvoří adaptér pro [k ověřování jquery](http://jqueryvalidation.org/documentation/) se spustí na straně klienta za vás automaticky jako součást ověření. Prvním krokem je řídit, jaké atributy dat. přidá implementace `IClientModelValidator` rozhraní, jak je znázorněno zde:

[!code-csharp[](validation/sample/ClassicMovieAttribute.cs?name=snippet_AddValidation)]

Atributy, které toto rozhraní implementují můžete přidat do generované pole atributů HTML. Zkoumání výstup `ReleaseDate` element odhalí kód HTML, který je podobný jako předchozí příklad, s tím rozdílem, nyní je `data-val-classicmovie` atribut, který byl definován v `AddValidation` metodu `IClientModelValidator`.

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="" />
```

Ověření nerušivého používá data v `data-` atributy se mají zobrazit chybové zprávy. Nicméně jQuery nebude vědět o pravidlech nebo zprávy, dokud je nepřidáte do prvku jQuery `validator` objektu. To je ukázáno v následujícím příkladu, který přidá vlastní `classicmovie` způsob ověření klienta jQuery `validator` objektu. Vysvětlení `unobtrusive.adapters.add` metodu, najdete v článku [Nerušivý ověření klienta v architektuře ASP.NET MVC](http://bradwilson.typepad.com/blog/2010/10/mvc3-unobtrusive-validation.html).

[!code-javascript[](validation/sample/Views/Movies/Create.cshtml?name=snippet_UnobtrusiveValidation)]

S předchozím kódu `classicmovie` metoda provádí ověřování na straně klienta na film datum vydání. Chybová zpráva se zobrazí, pokud metoda vrátí `false`.

## <a name="remote-validation"></a>Vzdálené ověření

Vzdálené ověření je skvělé funkce, která použijte, pokud je potřeba ověřit data na straně klienta s daty na serveru. Vaše aplikace může například potřebovat a ověřte, jestli e-mail nebo uživatelské jméno je již používán a dotáže velké množství dat k tomu. Stahování velkých sad dat pro ověření jednu nebo několik polí spotřebovává příliš mnoho prostředků. Také to může zveřejnit citlivé informace. Další možností je vytvořit round-trip žádost o ověření pole.

Implementace vzdáleného ověřování ve dvou krocích. Nejprve musíte označit modelu pomocí `[Remote]` atribut. `[Remote]` Atribut je možné zadat více přetížení, můžete použít ke směrování JavaScript na straně klienta na odpovídající kód pro volání. Následující příklad ukazuje na `VerifyEmail` metody akce `Users` kontroleru.

[!code-csharp[](validation/sample/User.cs?name=snippet_UserEmailProperty)]

Druhý krok je uvedení kód pro ověření v odpovídající metody akce, jak jsou definovány v `[Remote]` atribut. Podle jQuery ověřit [vzdálené](https://jqueryvalidation.org/remote-method/) metoda dokumentaci odpovědi serveru musí být řetězec formátu JSON, který je buď:

* `"true"` pro platné prvky.
* `"false"`, `undefined`, nebo `null` neplatné elementy pomocí výchozí chybovou zprávu.

Pokud odpověď serveru je řetězec (například `"That name is already taken, try peter123 instead"`), řetězec se zobrazí jako vlastní chybovou zprávu místo výchozí řetězec.

Definice `VerifyEmail` metoda řídí následujícími pravidly, jak je znázorněno níže. Vrátí Chyba ověřování zpráv, pokud se používá e-mailu, nebo `true` Pokud e-mailu je zdarma a zabalí výsledek `JsonResult` objektu. Na straně klienta můžete použít vrácené hodnoty pak pokračujte v případě potřeby zobrazí chybu.

[!code-csharp[](validation/sample/UsersController.cs?name=snippet_VerifyEmail)]

Nyní když uživatelé zadají e-mailu, JavaScript v zobrazení zavolá vzdálené zobrazíte-li tuto e-mailu je už zabraný a pokud ano, zobrazí se chybová zpráva. V opačném případě uživatel odešle formulář jako obvykle.

`AdditionalFields` Vlastnost `[Remote]` atribut je užitečné pro ověření kombinace různých typů polí s daty na serveru. Například pokud `User` modelu výše má dvě další vlastnosti volá `FirstName` a `LastName`, můžete chtít ověřit, že stávající uživatelé už nemají tohoto páru názvy. Můžete definovat nové vlastnosti, jak je znázorněno v následujícím kódu:

[!code-csharp[](validation/sample/User.cs?name=snippet_UserNameProperties)]

`AdditionalFields` mohli jste explicitně nastavena na řetězce `"FirstName"` a `"LastName"`, ale pomocí [ `nameof` ](/dotnet/csharp/language-reference/keywords/nameof) operátor takto zjednodušuje později refaktoring. Metody akce k provedení ověření pak musí přijmout dva argumenty, jeden pro hodnotu vlastnosti `FirstName` a jeden pro hodnotu `LastName`.

[!code-csharp[](validation/sample/UsersController.cs?name=snippet_VerifyName)]

Nyní když uživatelé zadat křestní jméno a příjmení, JavaScript:

* Díky vzdálené volání zobrazíte, pokud je už zabraný tohoto dvojice názvů.
* Pokud je už zabraný dvojici, zobrazí se chybová zpráva.
* Pokud ne, uživatel odešle formulář.

Pokud je potřeba ověřit dvě nebo více polí s `[Remote]` atribut, je uvádíte jako seznam oddělený čárkami. Například, chcete-li přidat `MiddleName` nastavena vlastnost modelu, `[Remote]` atributu, jak je znázorněno v následujícím kódu:

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`, jako jsou všechny argumenty atributu musí být konstantní výraz. Proto se nesmí používat [interpolovaný řetězec](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> inicializovat `AdditionalFields`. Pro každé další pole, které přidáte do `[Remote]` atribut, je nutné přidat další argument na odpovídající metodu akce kontroleru.
