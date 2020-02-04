---
title: ASP.NET Core Blazor formuláře a ověřování
author: guardrex
description: Naučte se používat scénáře ověřování formulářů a polí v Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 2758bcbbc76c8a59716fe224dd2deb4ca8c06929
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726892"
---
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a>ASP.NET Core [!OP.NO-LOC(Blazor)] formuláře a ověřování

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

Formuláře a ověřování se v [!OP.NO-LOC(Blazor)] podporují pomocí [datových poznámek](xref:mvc/models/validation).

Následující `ExampleModel` typ definuje logiku ověřování pomocí datových poznámek:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Formulář je definován pomocí komponenty `EditForm`. Následující formulář ukazuje typické prvky, komponenty a kód Razor:

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

* Formulář ověřuje vstup uživatele v poli `name` pomocí ověřování definovaného v `ExampleModel` typu. Model se vytvoří v bloku `@code` komponenty a je uložený v soukromém poli (`_exampleModel`). Pole je přiřazeno atributu `Model` elementu `<EditForm>`.
* `@bind-Value` vazby `InputText` komponenty:
  * Vlastnost modelu (`_exampleModel.Name`) na vlastnost `Value` komponenty `InputText`
  * Delegát události změny pro vlastnost `ValueChanged` `InputText` součásti.
* Komponenta `DataAnnotationsValidator` připojí podporu ověřování pomocí datových poznámek.
* Komponenta `ValidationSummary` shrnuje ověřovací zprávy.
* `HandleValidSubmit` se spustí, když formulář úspěšně odešle (projde ověřením).

K dispozici je sada předdefinovaných vstupních komponent pro příjem a ověření vstupu uživatele. Vstupy jsou ověřovány při jejich změně a při odeslání formuláře. Dostupné vstupní komponenty jsou uvedené v následující tabulce.

| Vstupní komponenta | Vykresleno jako&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

Všechny vstupní komponenty, včetně `EditForm`, podporují libovolné atributy. Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do vykresleného prvku HTML.

Vstupní komponenty poskytují výchozí chování pro ověřování při úpravách a změně jejich třídy CSS tak, aby odrážely stav pole. Některé součásti obsahují užitečnou logiku analýzy. Například `InputDate` a `InputNumber` zpracovávat neanalyzovatelné hodnoty tak, že je zaregistrujete jako chyby ověřování. Typy, které mohou přijmout hodnoty null, podporují také hodnotu null cílového pole (například `int?`).

Následující `Starship` typ definuje logiku ověřování pomocí větší sady vlastností a poznámek k datům než předchozí `ExampleModel`:

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

V předchozím příkladu je `Description` nepovinný, protože nejsou k dispozici žádné anotace dat.

Následující formulář ověřuje vstup uživatele pomocí ověřování definovaného v modelu `Starship`:

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

`EditForm` vytvoří `EditContext` jako [kaskádovou hodnotu](xref:blazor/components#cascading-values-and-parameters) , která sleduje metadata procesu úprav, včetně polí, která byla změněna, a aktuálních zpráv ověření. `EditForm` taky poskytuje praktické události pro platná a neplatná odeslání (`OnValidSubmit`, `OnInvalidSubmit`). Alternativně můžete použít `OnSubmit` k aktivaci ověřování a hodnot polí pro kontrolu pomocí vlastního ověřovacího kódu.

V následujícím příkladu:

* Metoda `HandleSubmit` se spustí, když je vybráno tlačítko **Odeslat** .
* Formulář se ověří pomocí `EditContext`formuláře.
* Formulář se dále ověří předáním `EditContext` do metody `ServerValidate`, která volá koncový bod webového rozhraní API na serveru (*není zobrazený*).
* Další kód se spustí v závislosti na výsledku ověřování na straně klienta a serveru kontrolou `isValid`.

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

## <a name="inputtext-based-on-the-input-event"></a>InputText na základě události vstupu

Komponentu `InputText` použijte k vytvoření vlastní komponenty, která používá událost `input` namísto události `change`.

Vytvořte komponentu s následujícím kódem a použijte komponentu stejně jako `InputText` se používá:

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>Práce s přepínači

Při práci s přepínači ve formuláři je datová vazba zpracovávána jinak než jiné prvky, protože přepínače jsou vyhodnocovány jako skupina. Hodnota každého přepínacího tlačítka je pevná, ale hodnota skupiny přepínačů je hodnota vybraného přepínacího tlačítka. Následující příklad ukazuje postup:

* Zpracuje datovou vazbu pro skupinu přepínačů.
* Podpora ověřování pomocí vlastní součásti `InputRadio`.

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

Následující `EditForm` používá předchozí součást `InputRadio` k získání a ověření hodnocení od uživatele:

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

## <a name="validation-support"></a>Podpora ověřování

Součást `DataAnnotationsValidator` připojuje ověřování pomocí datových poznámek k kaskádovým `EditContext`ům. Povolení podpory pro ověřování pomocí datových poznámek vyžaduje toto explicitní gesto. Chcete-li použít jiný systém ověřování než datové poznámky, nahraďte `DataAnnotationsValidator` vlastní implementací. ASP.NET Core implementace je k dispozici pro kontrolu v referenčním zdroji: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazor provádí dva typy ověřování:

* *Ověřování polí* se provede, když se karty uživatele nacházejí v poli. Při ověřování pole přidruží komponenta `DataAnnotationsValidator` všechny hlášené výsledky ověření k poli.
* *Ověřování modelu* se provede, když uživatel formulář odešle. Při ověřování modelu se `DataAnnotationsValidator` komponenta pokusí určit pole na základě názvu člena, který sestavy výsledků ověření. Výsledky ověření, které nejsou přidruženy k jednotlivým členům, jsou přidruženy k modelu, nikoli poli.

### <a name="validation-summary-and-validation-message-components"></a>Komponenty zprávy pro Shrnutí a ověření

Komponenta `ValidationSummary` shrnuje všechny zprávy o ověřování, které se podobají [pomocníka značek Shrnutí ověřování](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Zprávy ověřování výstupu pro určitý model s parametrem `Model`:
  
```razor
<ValidationSummary Model="@_starship" />
```

Komponenta `ValidationMessage` zobrazí ověřovací zprávy pro konkrétní pole, které je podobné [Pomocníkovi značky ověřovací zprávy](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Zadejte pole pro ověřování pomocí atributu `For` a lambda výrazu pojmenování vlastnosti modelu:

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

Komponenty `ValidationMessage` a `ValidationSummary` podporují libovolné atributy. Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do generovaného `<div>` nebo `<ul>` elementu.

### <a name="custom-validation-attributes"></a>Vlastní ověřovací atributy

Chcete-li zajistit, aby byl výsledek ověření správně přidružen k poli při použití [vlastního ověřovacího atributu](xref:mvc/models/validation#custom-attributes), předejte <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> ověřovacího kontextu při vytváření <xref:System.ComponentModel.DataAnnotations.ValidationResult>:

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor balíček pro ověření datových poznámek

[Microsoft.AspNetCore.Blazor.Dataanotace.ověřování](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) je balíček, který vyplní mezery v prostředí ověřování pomocí komponenty `DataAnnotationsValidator`. Balíček je momentálně *experimentální*.

### <a name="compareproperty-attribute"></a>[CompareProperty] – atribut

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> nepracuje dobře s komponentou `DataAnnotationsValidator`, protože nepřiřazuje výsledek ověření k určitému členovi. To může vést k nekonzistentnímu chování mezi ověřováním na úrovni polí a při ověřování celého modelu při odeslání. [Microsoft.AspNetCore.Blazor.DataAnnotations.](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *experimentální* balíček pro ověření zavádí další atribut ověření `ComparePropertyAttribute`, který tato omezení působí. V Blazor aplikaci `[CompareProperty]` je přímá náhrada pro atribut `[Compare]`.

### <a name="nested-models-collection-types-and-complex-types"></a>Vnořené modely, typy kolekcí a komplexní typy

Blazor poskytuje podporu pro ověřování vstupu formuláře pomocí datových poznámek s integrovaným `DataAnnotationsValidator`. `DataAnnotationsValidator` však pouze ověřuje vlastnosti nejvyšší úrovně modelu vázaného na formulář, který není typu kolekce nebo komplexního typu.

Chcete-li ověřit celý graf objektu vázaného modelu, včetně vlastností kolekce a komplexního typu, použijte `ObjectGraphDataAnnotationsValidator` od *experimentálního* [Microsoft. AspNetCore.Blazor.Dataanotace.ověřovací](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) balíček:

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Přidávejte do vlastností modelu poznámky pomocí `[ValidateComplexType]`. V následujících třídách modelu obsahuje Třída `ShipDescription` další datové poznámky, které lze ověřit, je-li model svázán s formulářem:

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

### <a name="enable-the-submit-button-based-on-form-validation"></a>Povolit tlačítko Odeslat na základě ověření formuláře

Povolení a zakázání tlačítka Odeslat na základě ověření formuláře:

* K přiřazení modelu při inicializaci komponenty použijte `EditContext` formuláře.
* Pokud chcete povolit a zakázat tlačítko Odeslat, ověřte formulář v `OnFieldChanged`ovém zpětném volání kontextu.

```razor
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

        _editContext.OnFieldChanged += (_, __) =>
        {
            _formInvalid = !_editContext.Validate();
            StateHasChanged();
        };
    }
}
```

V předchozím příkladu nastavte `_formInvalid` na `false`, pokud:

* Formulář je předem načten s platnými výchozími hodnotami.
* Chcete povolit tlačítko Odeslat, když se formulář načte.

Vedlejším účinkem předchozího přístupu je, že komponenta `ValidationSummary` se naplní neplatnými poli poté, co uživatel pracuje s libovolným polem. Tento scénář je možné vyřešit jedním z následujících způsobů:

* Ve formuláři nepoužívejte komponentu `ValidationSummary`.
* Zpřístupní komponentu `ValidationSummary`, když je vybráno tlačítko Odeslat (například v metodě `HandleValidSubmit`).

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
