---
title: ASP.NET Blazor základní formuláře a validace
author: guardrex
description: Přečtěte si, jak používat Blazorformuláře a scénáře ověření polí v aplikacích .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 0359a9337860d9b8ce0b81d8833a034a898b05a5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218957"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET core blazor formuláře a validace

[Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

Formuláře a validace jsou podporovány v Blazoru pomocí [datových anotací](xref:mvc/models/validation).

Následující `ExampleModel` typ definuje logiku ověřování pomocí datových anotací:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Formulář je definován `EditForm` pomocí komponenty. Následující formulář ukazuje typické prvky, součásti a kód Razor:

```razor
<EditForm Model="@_exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="_exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel _exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

V předchozím příkladu:

* Formulář ověří vstup uživatele `name` v poli pomocí `ExampleModel` ověření definovaného v typu. Model je vytvořen v bloku `@code` komponenty a držen`_exampleModel`v soukromém poli ( ). Pole je přiřazeno `Model` atributu `<EditForm>` prvku.
* Složky `InputText` se `@bind-Value` váže:
  * Vlastnost modelu`_exampleModel.Name`( ) `InputText` k `Value` vlastnosti komponenty.
  * Delegát události změny `InputText` na vlastnost `ValueChanged` komponenty.
* Komponenta `DataAnnotationsValidator` připojuje podporu ověřování pomocí datových poznámk.
* Komponenta `ValidationSummary` shrnuje ověřovací zprávy.
* `HandleValidSubmit`se aktivuje, když se formulář úspěšně odešle (projde ověřením).

Sada vestavěných vstupních součástí je k dispozici pro příjem a ověřování vstupu uživatele. Vstupy jsou ověřeny při změně a při odeslání formuláře. Dostupné vstupní součásti jsou uvedeny v následující tabulce.

| Vstupní komponenta | Vykresleno jako&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

Všechny vstupní součásti, `EditForm`včetně , podporují libovolné atributy. Do vykresleného prvku HTML se přidá libovolný atribut, který neodpovídá parametru komponenty.

Vstupní součásti poskytují výchozí chování pro ověřování při úpravách a změně třídy CSS tak, aby odrážela stav pole. Některé součásti obsahují užitečnou logiku analýzy. Například `InputDate` a `InputNumber` řádně zpracovat neanalyzovatelné hodnoty jejich registrací jako chyby ověření. Typy, které mohou přijímat hodnoty null, také podporují `int?`nullability cílového pole (například).

Následující `Starship` typ definuje logiku ověření pomocí větší sady vlastností a `ExampleModel`datových anotací než předchozí :

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

V předchozím příkladu `Description` je volitelné, protože nejsou k dispozici žádné poznámky dat.

Následující formulář ověřuje vstup uživatele `Starship` pomocí ověření definovaného v modelu:

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@_starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="_starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="_starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="_starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="_starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="_starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="_starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship _starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

Vytvoří `EditForm` `EditContext` jako [kaskádovou hodnotu,](xref:blazor/components#cascading-values-and-parameters) která sleduje metadata o procesu úprav, včetně polí, která byla změněna, a aktuálních ověřovacích zpráv. Poskytuje `EditForm` také pohodlné události pro platné`OnValidSubmit`a `OnInvalidSubmit`neplatné odeslání ( , ). Případně můžete `OnSubmit` použít k aktivaci hodnoty ověřovacího a kontrolního pole s vlastním ověřovacím kódem.

V následujícím příkladu:

* Metoda `HandleSubmit` se spustí, když je vybráno tlačítko **Odeslat.**
* Formulář je ověřen pomocí formuláře `EditContext`.
* Formulář je dále ověřen předáním `EditContext` `ServerValidate` metody, která volá koncový bod webového rozhraní API na serveru *(není zobrazen).*
* Další kód je spuštěn v závislosti na výsledku ověření na `isValid`straně klienta a serveru kontrolou .

```razor
<EditForm EditContext="@_editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = _editContext.Validate() && 
            await ServerValidate(_editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a>InputText založený na vstupní události

Komponenta `InputText` slouží k vytvoření vlastní `input` součásti, `change` která používá událost namísto události.

Vytvořte komponentu s následujícími značkami a `InputText` použijte ji tak, jak se používá:

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>Práce s přepínacími tlačítky

Při práci s přepínači ve formuláři je datová vazba zpracována jinak než jiné prvky, protože přepínací tlačítka jsou vyhodnocována jako skupina. Hodnota každého přepínacího tlačítka je pevná, ale hodnota skupiny přepínacích tlačítek je hodnota vybraného přepínacího tlačítka. Následující příklad ukazuje, jak:

* Zpracování datové vazby pro skupinu přepínacích tlačítek.
* Podporujte ověřování `InputRadio` pomocí vlastní součásti.

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

Následující `EditForm` používá předchozí `InputRadio` součást získat a ověřit hodnocení od uživatele:

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="_model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="_model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @_model.Rating</p>

@code {
    private Model _model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a>Podpora pro ověření

Komponenta `DataAnnotationsValidator` připojuje podporu ověřování pomocí datových anotací ke kaskádovitě `EditContext`. Povolení podpory pro ověřování pomocí datových poznámk vyžaduje toto explicitní gesto. Chcete-li použít jiný ověřovací systém než poznámky dat, nahraďte `DataAnnotationsValidator` vlastní implementaci. ASP.NET Core implementace je k dispozici pro kontrolu v referenčním zdroji: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazorprovádí dva typy validace:

* *Ověření pole* se provádí, když uživatel karty z pole. Během ověřování pole `DataAnnotationsValidator` komponenta přidruží všechny hlášené výsledky ověření k poli.
* *Ověření modelu* se provádí, když uživatel odešle formulář. Během ověřování modelu `DataAnnotationsValidator` se komponenta pokusí určit pole na základě názvu člena, který hlásí výsledek ověření. Výsledky ověření, které nejsou přidruženy k jednotlivému členu, jsou přidruženy k modelu, nikoli k poli.

### <a name="validation-summary-and-validation-message-components"></a>Součásti souhrnu ověření a ověřovací zprávy

Komponenta `ValidationSummary` shrnuje všechny ověřovací zprávy, které jsou podobné [pomocníku pro souhrnnou značku ověření](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Výstupní ověřovací zprávy pro konkrétní `Model` model s parametrem:
  
```razor
<ValidationSummary Model="@_starship" />
```

Komponenta `ValidationMessage` zobrazuje ověřovací zprávy pro určité pole, které je podobné [pomocné](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)službě Validation Message Tag Er . Zadejte pole pro `For` ověření s atributem a výraz lambda pojmenování vlastnosti modelu:

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

`ValidationMessage` Komponenty `ValidationSummary` a podporují libovolné atributy. Všechny atributy, které neodpovídá parametru komponenty, jsou přidány do generovaného `<div>` prvku nebo `<ul>` prvku.

### <a name="custom-validation-attributes"></a>Vlastní atributy ověření

Chcete-li zajistit, aby byl výsledek ověření správně přidružen k poli při <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> použití vlastního <xref:System.ComponentModel.DataAnnotations.ValidationResult> [atributu ověření](xref:mvc/models/validation#custom-attributes), předajte při vytváření : kontextu ověření kontextu ověření .

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazorověřovací balíček anotací dat

[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) je balíček, který vyplňuje `DataAnnotationsValidator` mezery zkušeností ověření pomocí komponenty. Balíček je v současné době *experimentální*.

### <a name="compareproperty-attribute"></a>[CompareProperty] atribut

Nefunguje <xref:System.ComponentModel.DataAnnotations.CompareAttribute> dobře s komponentou, `DataAnnotationsValidator` protože nepřidružuje výsledek ověření s konkrétním členem. To může mít za následek nekonzistentní chování mezi ověřením na úrovni pole a při ověření celého modelu při odeslání. *Experimentální* balíček [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) zavádí další `ComparePropertyAttribute`atribut ověření , který funguje kolem těchto omezení. V Blazor aplikaci `[CompareProperty]` je přímou `[Compare]` náhradou atributu.

### <a name="nested-models-collection-types-and-complex-types"></a>Vnořené modely, typy kolekcí a složité typy

Blazorposkytuje podporu pro ověřování vstupu formuláře pomocí datových anotací s vestavěným `DataAnnotationsValidator`. `DataAnnotationsValidator` Však pouze ověří nejvyšší úrovně vlastnosti modelu vázané na formulář, které nejsou vlastnosti kolekce nebo komplexní typ.

Chcete-li ověřit celý objektový graf vázaného modelu, včetně vlastností typu kolekce a komplexního typu, použijte `ObjectGraphDataAnnotationsValidator` balíček poskytnutý *experimentálním* balíčkem [Microsoft.AspNetCore.Components.DataAnnotations.Validation:](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Oslnění vlastností modelu pomocí aplikace `[ValidateComplexType]`. V následujících třídách `ShipDescription` modelu třída obsahuje další datové poznámky k ověření, když je model vázán na formulář:

*Starship.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

*ShipDescription.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a>Povolení tlačítka odeslat na základě ověření formuláře

Povolení a zakázání tlačítka odeslat na základě ověření formuláře:

* Pomocí formuláře `EditContext` přiřaďte model při inicializování komponenty.
* Ověřte formulář v `OnFieldChanged` kontextu zpětného volání povolit a zakázat tlačítko odeslat.
* Unhook obslužné rutiny události v metodě. `Dispose` Další informace naleznete v tématu <xref:blazor/lifecycle#component-disposal-with-idisposable>.

```razor
@implements IDisposable

<EditForm EditContext="@_editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private bool _formInvalid = true;
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
        _editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        _formInvalid = !_editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        _editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

V předchozím příkladu `_formInvalid` nastavte `false` if:

* Formulář je předinstalován s platnými výchozími hodnotami.
* Chcete po načtení formuláře aktivovat tlačítko odeslat.

Vedlejším účinkem předchozího přístupu `ValidationSummary` je, že součást je naplněna neplatnými poli poté, co uživatel interaguje s jedním polem. Tento scénář lze řešit jedním z následujících způsobů:

* Nepoužívejte `ValidationSummary` součást ve formuláři.
* Po `ValidationSummary` výběru tlačítka odeslat zviditelněte komponentu `HandleValidSubmit` (například v metodě).

```razor
<EditForm EditContext="@_editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@_displaySummary" />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private string _displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        _displaySummary = "display:block";
    }
}
```
