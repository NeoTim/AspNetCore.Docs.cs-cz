---
title: ASP.NET Core Blazor formuláře a ověřování
author: guardrex
description: Naučte se používat scénáře ověřování formulářů a polí v Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 9ffcacc404aa868d533196e5c1bb52d9acdeb337
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768978"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET Core formuláře a ověřování Blazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

Formuláře a ověřování se v Blazor podporují pomocí [datových poznámek](xref:mvc/models/validation).

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

Formulář je definován pomocí `EditForm` komponenty. Následující formulář ukazuje typické prvky, komponenty a kód Razor:

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

* Formulář ověřuje vstup uživatele v `name` poli pomocí ověřování definovaného v `ExampleModel` typu. Model se vytvoří v `@code` bloku komponenty a je uložený v soukromém poli (`_exampleModel`). Pole je přiřazeno `Model` atributu `<EditForm>` elementu.
* `@bind-Value` Vazby `InputText` komponenty:
  * Vlastnost modelu (`_exampleModel.Name`) na `InputText` `Value` vlastnost součásti.
  * Delegát události změny pro `InputText` `ValueChanged` vlastnost komponenty.
* `DataAnnotationsValidator` Komponenta připojuje ověřování pomocí datových poznámek.
* `ValidationSummary` Komponenta shrnuje ověřovací zprávy.
* `HandleValidSubmit`aktivuje se, když formulář úspěšně odešle (projde ověřením).

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

Vstupní komponenty poskytují výchozí chování pro ověřování při úpravách a změně jejich třídy CSS tak, aby odrážely stav pole. Některé součásti obsahují užitečnou logiku analýzy. Například a `InputNumber` zpracujte `InputDate` neanalyzovatelné hodnoty tak, že je zaregistrujete jako chyby ověřování. Typy, které mohou přijmout hodnoty null, podporují také hodnotu null cílového pole (například `int?`).

Následující `Starship` typ definuje logiku ověřování pomocí větší sady vlastností a poznámek k datům než dříve `ExampleModel`:

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

V předchozím příkladu je volitelná, `Description` protože nejsou k dispozici žádné anotace dat.

Následující formulář ověří uživatelský vstup pomocí ověřování definovaného v `Starship` modelu:

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

`EditForm` Vytvoří `EditContext` jako [kaskádovou hodnotu](xref:blazor/components#cascading-values-and-parameters) , která sleduje metadata procesu úprav, včetně toho, která pole jsou upravena a aktuální zprávy ověření. Poskytuje `EditForm` taky praktické události pro platná a neplatná odeslání (`OnValidSubmit`, `OnInvalidSubmit`). Alternativně můžete `OnSubmit` použít k aktivaci ověřování a hodnot polí pro kontrolu pomocí vlastního ověřovacího kódu.

V následujícím příkladu:

* `HandleSubmit` Metoda se spustí, když je vybráno tlačítko **Odeslat** .
* Formulář se ověří pomocí formuláře `EditContext`.
* Formulář se dále ověří předáním `EditContext` `ServerValidate` metody, která volá koncový bod webového rozhraní API na serveru (*není zobrazený*).
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

Pomocí `InputText` komponenty můžete vytvořit vlastní komponentu, která místo `input` `change` události používá událost.

Vytvořte komponentu s následujícím kódem a použijte komponentu stejně jako `InputText` je použit:

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

Při práci s přepínači ve formuláři je datová vazba zpracovávána jinak než jiné prvky, protože přepínače jsou vyhodnocovány jako skupina. Hodnota každého přepínacího tlačítka je pevná, ale hodnota skupiny přepínačů je hodnota vybraného přepínacího tlačítka. Následující příklad ukazuje, jak:

* Zpracuje datovou vazbu pro skupinu přepínačů.
* Podpora ověřování pomocí vlastní `InputRadio` součásti.

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

V následujícím `EditForm` příkladu se k `InputRadio` získání a ověření hodnocení od uživatele používá předchozí komponenta:

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

`DataAnnotationsValidator` Komponenta připojuje ověřování pomocí datových poznámek do kaskády `EditContext`. Povolení podpory pro ověřování pomocí datových poznámek vyžaduje toto explicitní gesto. Chcete-li použít jiný systém ověřování než datové poznámky, nahraďte `DataAnnotationsValidator` vlastní implementaci. Implementace ASP.NET Core je k dispozici pro kontrolu ve zdroji odkazu: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazorprovádí dva typy ověřování:

* *Ověřování polí* se provede, když se karty uživatele nacházejí v poli. Při ověřování pole přidruží `DataAnnotationsValidator` součást všechny hlášené výsledky ověření k poli.
* *Ověřování modelu* se provede, když uživatel formulář odešle. Při ověřování modelu se `DataAnnotationsValidator` komponenta pokusí určit pole na základě názvu člena, který sestavy výsledků ověření. Výsledky ověření, které nejsou přidruženy k jednotlivým členům, jsou přidruženy k modelu, nikoli poli.

### <a name="validation-summary-and-validation-message-components"></a>Komponenty zprávy pro Shrnutí a ověření

`ValidationSummary` Komponenta shrnuje všechny zprávy o ověřování, které se podobají [pomocníka značek pro Shrnutí ověření](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Zprávy ověřování výstupu pro konkrétní model s `Model` parametrem:
  
```razor
<ValidationSummary Model="@_starship" />
```

`ValidationMessage` Komponenta zobrazí ověřovací zprávy pro konkrétní pole, které se podobá [pomocníka značek ověřovací zprávy](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Zadejte pole pro ověření s `For` atributem a výrazem lambda pojmenování vlastnosti modelu:

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

Komponenty `ValidationMessage` a `ValidationSummary` podporují libovolné atributy. Všechny atributy, které se neshodují s parametrem komponenty, `<div>` jsou `<ul>` přidány do generovaného prvku nebo.

### <a name="custom-validation-attributes"></a>Vlastní ověřovací atributy

Chcete-li zajistit, aby byl výsledek ověření správně přidružen k poli při použití [vlastního ověřovacího atributu](xref:mvc/models/validation#custom-attributes), předejte kontext ověření <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> při vytváření <xref:System.ComponentModel.DataAnnotations.ValidationResult>:

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

### <a name="blazor-data-annotations-validation-package"></a>Blazorbalíček pro ověření datových poznámek

[Microsoft. AspNetCore. Components. Dataanotaces. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) je balíček, který vyplní mezery při ověřování pomocí `DataAnnotationsValidator` komponenty. Balíček je momentálně *experimentální*.

### <a name="compareproperty-attribute"></a>[CompareProperty] – atribut

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> Nefunguje dobře s `DataAnnotationsValidator` komponentou, protože nepřiřazuje výsledek ověření k určitému členu. To může vést k nekonzistentnímu chování mezi ověřováním na úrovni polí a při ověřování celého modelu při odeslání. Balíček [Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experiment* zavádí další ověřovací atribut `ComparePropertyAttribute`, který tato omezení funguje. V Blazor aplikaci `[CompareProperty]` je přímá náhrada za `[Compare]` atribut.

### <a name="nested-models-collection-types-and-complex-types"></a>Vnořené modely, typy kolekcí a komplexní typy

Blazorposkytuje podporu pro ověřování vstupu formuláře pomocí datových poznámek s integrovaným `DataAnnotationsValidator`. `DataAnnotationsValidator` Pouze ověří vlastnosti nejvyšší úrovně modelu svázaného s formulářem, který není vlastností kolekce nebo komplexního typu.

Chcete-li ověřit celý graf objektu vázaného modelu, včetně vlastností kolekce a komplexního typu, použijte rozhraní `ObjectGraphDataAnnotationsValidator` , které poskytuje *experimentální* balíček [Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Přidávejte do vlastností modelu `[ValidateComplexType]`poznámky. V následujících třídách modelu obsahuje `ShipDescription` třída další datové poznámky pro ověření, když je model svázán s formulářem:

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

* `EditContext` K přiřazení modelu při inicializaci komponenty použijte formulář.
* Ověřte formulář v `OnFieldChanged` zpětném volání kontextu a povolte a zakažte tlačítko Odeslat.
* Odpojte obslužnou rutinu události v `Dispose` metodě. Další informace naleznete v tématu <xref:blazor/lifecycle#component-disposal-with-idisposable>.

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

V předchozím příkladu nastavte `_formInvalid` na `false` IF:

* Formulář je předem načten s platnými výchozími hodnotami.
* Chcete povolit tlačítko Odeslat, když se formulář načte.

Vedlejším účinkem předcházejícího přístupu je, že `ValidationSummary` komponenta je naplněná neplatnými poli poté, co uživatel komunikuje s jakýmkoli jedním polem. Tento scénář je možné vyřešit jedním z následujících způsobů:

* Nepoužívejte `ValidationSummary` komponentu na formuláři.
* Zpřístupní komponentu `ValidationSummary` , když je vybráno tlačítko Odeslat (například v `HandleValidSubmit` metodě).

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
