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
ms.openlocfilehash: 5aad5a4d4303151ef5be82481dfae7367abeffbc
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083235"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="47c72-103">ASP.NET Core formuláře a ověřování Blazor</span><span class="sxs-lookup"><span data-stu-id="47c72-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="47c72-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="47c72-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="47c72-105">Formuláře a ověřování se v Blazor podporují pomocí [datových poznámek](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="47c72-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="47c72-106">Následující `ExampleModel` typ definuje logiku ověřování pomocí datových poznámek:</span><span class="sxs-lookup"><span data-stu-id="47c72-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="47c72-107">Formulář je definován pomocí komponenty `EditForm`.</span><span class="sxs-lookup"><span data-stu-id="47c72-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="47c72-108">Následující formulář ukazuje typické prvky, komponenty a kód Razor:</span><span class="sxs-lookup"><span data-stu-id="47c72-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="47c72-109">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="47c72-109">In the preceding example:</span></span>

* <span data-ttu-id="47c72-110">Formulář ověřuje vstup uživatele v poli `name` pomocí ověřování definovaného v `ExampleModel` typu.</span><span class="sxs-lookup"><span data-stu-id="47c72-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="47c72-111">Model se vytvoří v bloku `@code` komponenty a je uložený v soukromém poli (`_exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="47c72-111">The model is created in the component's `@code` block and held in a private field (`_exampleModel`).</span></span> <span data-ttu-id="47c72-112">Pole je přiřazeno atributu `Model` elementu `<EditForm>`.</span><span class="sxs-lookup"><span data-stu-id="47c72-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="47c72-113">`@bind-Value` vazby `InputText` komponenty:</span><span class="sxs-lookup"><span data-stu-id="47c72-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="47c72-114">Vlastnost modelu (`_exampleModel.Name`) na vlastnost `Value` komponenty `InputText`</span><span class="sxs-lookup"><span data-stu-id="47c72-114">The model property (`_exampleModel.Name`) to the `InputText` component's `Value` property.</span></span>
  * <span data-ttu-id="47c72-115">Delegát události změny pro vlastnost `ValueChanged` `InputText` součásti.</span><span class="sxs-lookup"><span data-stu-id="47c72-115">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="47c72-116">Komponenta `DataAnnotationsValidator` připojí podporu ověřování pomocí datových poznámek.</span><span class="sxs-lookup"><span data-stu-id="47c72-116">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="47c72-117">Komponenta `ValidationSummary` shrnuje ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="47c72-117">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="47c72-118">`HandleValidSubmit` se spustí, když formulář úspěšně odešle (projde ověřením).</span><span class="sxs-lookup"><span data-stu-id="47c72-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="47c72-119">K dispozici je sada předdefinovaných vstupních komponent pro příjem a ověření vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="47c72-119">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="47c72-120">Vstupy jsou ověřovány při jejich změně a při odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="47c72-120">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="47c72-121">Dostupné vstupní komponenty jsou uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="47c72-121">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="47c72-122">Vstupní komponenta</span><span class="sxs-lookup"><span data-stu-id="47c72-122">Input component</span></span> | <span data-ttu-id="47c72-123">Vykresleno jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="47c72-123">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="47c72-124">Všechny vstupní komponenty, včetně `EditForm`, podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="47c72-124">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="47c72-125">Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do vykresleného prvku HTML.</span><span class="sxs-lookup"><span data-stu-id="47c72-125">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="47c72-126">Vstupní komponenty poskytují výchozí chování pro ověřování při úpravách a změně jejich třídy CSS tak, aby odrážely stav pole.</span><span class="sxs-lookup"><span data-stu-id="47c72-126">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="47c72-127">Některé součásti obsahují užitečnou logiku analýzy.</span><span class="sxs-lookup"><span data-stu-id="47c72-127">Some components include useful parsing logic.</span></span> <span data-ttu-id="47c72-128">Například `InputDate` a `InputNumber` zpracovávat neanalyzovatelné hodnoty tak, že je zaregistrujete jako chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="47c72-128">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="47c72-129">Typy, které mohou přijmout hodnoty null, podporují také hodnotu null cílového pole (například `int?`).</span><span class="sxs-lookup"><span data-stu-id="47c72-129">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="47c72-130">Následující `Starship` typ definuje logiku ověřování pomocí větší sady vlastností a poznámek k datům než předchozí `ExampleModel`:</span><span class="sxs-lookup"><span data-stu-id="47c72-130">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="47c72-131">V předchozím příkladu je `Description` nepovinný, protože nejsou k dispozici žádné anotace dat.</span><span class="sxs-lookup"><span data-stu-id="47c72-131">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="47c72-132">Následující formulář ověřuje vstup uživatele pomocí ověřování definovaného v modelu `Starship`:</span><span class="sxs-lookup"><span data-stu-id="47c72-132">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="47c72-133">`EditForm` vytvoří `EditContext` jako [kaskádovou hodnotu](xref:blazor/components#cascading-values-and-parameters) , která sleduje metadata procesu úprav, včetně polí, která byla změněna, a aktuálních zpráv ověření.</span><span class="sxs-lookup"><span data-stu-id="47c72-133">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="47c72-134">`EditForm` taky poskytuje praktické události pro platná a neplatná odeslání (`OnValidSubmit`, `OnInvalidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="47c72-134">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="47c72-135">Alternativně můžete použít `OnSubmit` k aktivaci ověřování a hodnot polí pro kontrolu pomocí vlastního ověřovacího kódu.</span><span class="sxs-lookup"><span data-stu-id="47c72-135">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="47c72-136">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="47c72-136">In the following example:</span></span>

* <span data-ttu-id="47c72-137">Metoda `HandleSubmit` se spustí, když je vybráno tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="47c72-137">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="47c72-138">Formulář se ověří pomocí `EditContext`formuláře.</span><span class="sxs-lookup"><span data-stu-id="47c72-138">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="47c72-139">Formulář se dále ověří předáním `EditContext` do metody `ServerValidate`, která volá koncový bod webového rozhraní API na serveru (*není zobrazený*).</span><span class="sxs-lookup"><span data-stu-id="47c72-139">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="47c72-140">Další kód se spustí v závislosti na výsledku ověřování na straně klienta a serveru kontrolou `isValid`.</span><span class="sxs-lookup"><span data-stu-id="47c72-140">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="47c72-141">InputText na základě události vstupu</span><span class="sxs-lookup"><span data-stu-id="47c72-141">InputText based on the input event</span></span>

<span data-ttu-id="47c72-142">Komponentu `InputText` použijte k vytvoření vlastní komponenty, která používá událost `input` namísto události `change`.</span><span class="sxs-lookup"><span data-stu-id="47c72-142">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="47c72-143">Vytvořte komponentu s následujícím kódem a použijte komponentu stejně jako `InputText` se používá:</span><span class="sxs-lookup"><span data-stu-id="47c72-143">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="47c72-144">Práce s přepínači</span><span class="sxs-lookup"><span data-stu-id="47c72-144">Work with radio buttons</span></span>

<span data-ttu-id="47c72-145">Při práci s přepínači ve formuláři je datová vazba zpracovávána jinak než jiné prvky, protože přepínače jsou vyhodnocovány jako skupina.</span><span class="sxs-lookup"><span data-stu-id="47c72-145">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="47c72-146">Hodnota každého přepínacího tlačítka je pevná, ale hodnota skupiny přepínačů je hodnota vybraného přepínacího tlačítka.</span><span class="sxs-lookup"><span data-stu-id="47c72-146">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="47c72-147">Následující příklad ukazuje postup:</span><span class="sxs-lookup"><span data-stu-id="47c72-147">The following example shows how to:</span></span>

* <span data-ttu-id="47c72-148">Zpracuje datovou vazbu pro skupinu přepínačů.</span><span class="sxs-lookup"><span data-stu-id="47c72-148">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="47c72-149">Podpora ověřování pomocí vlastní součásti `InputRadio`.</span><span class="sxs-lookup"><span data-stu-id="47c72-149">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="47c72-150">Následující `EditForm` používá předchozí součást `InputRadio` k získání a ověření hodnocení od uživatele:</span><span class="sxs-lookup"><span data-stu-id="47c72-150">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="validation-support"></a><span data-ttu-id="47c72-151">Podpora ověřování</span><span class="sxs-lookup"><span data-stu-id="47c72-151">Validation support</span></span>

<span data-ttu-id="47c72-152">Součást `DataAnnotationsValidator` připojuje ověřování pomocí datových poznámek k kaskádovým `EditContext`ům.</span><span class="sxs-lookup"><span data-stu-id="47c72-152">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="47c72-153">Povolení podpory pro ověřování pomocí datových poznámek vyžaduje toto explicitní gesto.</span><span class="sxs-lookup"><span data-stu-id="47c72-153">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="47c72-154">Chcete-li použít jiný systém ověřování než datové poznámky, nahraďte `DataAnnotationsValidator` vlastní implementací.</span><span class="sxs-lookup"><span data-stu-id="47c72-154">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="47c72-155">ASP.NET Core implementace je k dispozici pro kontrolu v referenčním zdroji: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="47c72-155">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="47c72-156"> provádí dva typy ověřování:</span><span class="sxs-lookup"><span data-stu-id="47c72-156"> performs two types of validation:</span></span>

* <span data-ttu-id="47c72-157">*Ověřování polí* se provede, když se karty uživatele nacházejí v poli.</span><span class="sxs-lookup"><span data-stu-id="47c72-157">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="47c72-158">Při ověřování pole přidruží komponenta `DataAnnotationsValidator` všechny hlášené výsledky ověření k poli.</span><span class="sxs-lookup"><span data-stu-id="47c72-158">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="47c72-159">*Ověřování modelu* se provede, když uživatel formulář odešle.</span><span class="sxs-lookup"><span data-stu-id="47c72-159">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="47c72-160">Při ověřování modelu se `DataAnnotationsValidator` komponenta pokusí určit pole na základě názvu člena, který sestavy výsledků ověření.</span><span class="sxs-lookup"><span data-stu-id="47c72-160">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="47c72-161">Výsledky ověření, které nejsou přidruženy k jednotlivým členům, jsou přidruženy k modelu, nikoli poli.</span><span class="sxs-lookup"><span data-stu-id="47c72-161">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="47c72-162">Komponenty zprávy pro Shrnutí a ověření</span><span class="sxs-lookup"><span data-stu-id="47c72-162">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="47c72-163">Komponenta `ValidationSummary` shrnuje všechny zprávy o ověřování, které se podobají [pomocníka značek Shrnutí ověřování](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="47c72-163">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="47c72-164">Zprávy ověřování výstupu pro určitý model s parametrem `Model`:</span><span class="sxs-lookup"><span data-stu-id="47c72-164">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@_starship" />
```

<span data-ttu-id="47c72-165">Komponenta `ValidationMessage` zobrazí ověřovací zprávy pro konkrétní pole, které je podobné [Pomocníkovi značky ověřovací zprávy](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="47c72-165">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="47c72-166">Zadejte pole pro ověřování pomocí atributu `For` a lambda výrazu pojmenování vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="47c72-166">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

<span data-ttu-id="47c72-167">Komponenty `ValidationMessage` a `ValidationSummary` podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="47c72-167">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="47c72-168">Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do generovaného `<div>` nebo `<ul>` elementu.</span><span class="sxs-lookup"><span data-stu-id="47c72-168">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="47c72-169">Vlastní ověřovací atributy</span><span class="sxs-lookup"><span data-stu-id="47c72-169">Custom validation attributes</span></span>

<span data-ttu-id="47c72-170">Chcete-li zajistit, aby byl výsledek ověření správně přidružen k poli při použití [vlastního ověřovacího atributu](xref:mvc/models/validation#custom-attributes), předejte <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> ověřovacího kontextu při vytváření <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span><span class="sxs-lookup"><span data-stu-id="47c72-170">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="47c72-171"> balíček pro ověření datových poznámek</span><span class="sxs-lookup"><span data-stu-id="47c72-171"> data annotations validation package</span></span>

<span data-ttu-id="47c72-172">[Microsoft. AspNetCore. Components. Dataanotaces. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) je balíček, který vyplní mezery v prostředí ověřování pomocí komponenty `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="47c72-172">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="47c72-173">Balíček je momentálně *experimentální*.</span><span class="sxs-lookup"><span data-stu-id="47c72-173">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="47c72-174">[CompareProperty] – atribut</span><span class="sxs-lookup"><span data-stu-id="47c72-174">[CompareProperty] attribute</span></span>

<span data-ttu-id="47c72-175"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> nepracuje dobře s komponentou `DataAnnotationsValidator`, protože nepřiřazuje výsledek ověření k určitému členovi.</span><span class="sxs-lookup"><span data-stu-id="47c72-175">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="47c72-176">To může vést k nekonzistentnímu chování mezi ověřováním na úrovni polí a při ověřování celého modelu při odeslání.</span><span class="sxs-lookup"><span data-stu-id="47c72-176">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="47c72-177">Balíček [Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experiment* zavádí další ověřovací atribut `ComparePropertyAttribute`, který tato omezení funguje.</span><span class="sxs-lookup"><span data-stu-id="47c72-177">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="47c72-178">V Blazor aplikaci `[CompareProperty]` je přímá náhrada pro atribut `[Compare]`.</span><span class="sxs-lookup"><span data-stu-id="47c72-178">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="47c72-179">Vnořené modely, typy kolekcí a komplexní typy</span><span class="sxs-lookup"><span data-stu-id="47c72-179">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="47c72-180"> poskytuje podporu pro ověřování vstupu formuláře pomocí datových poznámek s integrovaným `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="47c72-180"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="47c72-181">`DataAnnotationsValidator` však pouze ověřuje vlastnosti nejvyšší úrovně modelu vázaného na formulář, který není typu kolekce nebo komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="47c72-181">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="47c72-182">Chcete-li ověřit celý graf objektu vázaného modelu, včetně vlastností kolekce a komplexního typu, použijte `ObjectGraphDataAnnotationsValidator` od *experimentálního* balíčku [Microsoft. AspNetCore. Components. DataAnnotations. ověřovací](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) balíček:</span><span class="sxs-lookup"><span data-stu-id="47c72-182">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="47c72-183">Přidávejte do vlastností modelu poznámky pomocí `[ValidateComplexType]`.</span><span class="sxs-lookup"><span data-stu-id="47c72-183">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="47c72-184">V následujících třídách modelu obsahuje Třída `ShipDescription` další datové poznámky, které lze ověřit, je-li model svázán s formulářem:</span><span class="sxs-lookup"><span data-stu-id="47c72-184">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="47c72-185">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="47c72-185">*Starship.cs*:</span></span>

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

<span data-ttu-id="47c72-186">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="47c72-186">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="47c72-187">Povolit tlačítko Odeslat na základě ověření formuláře</span><span class="sxs-lookup"><span data-stu-id="47c72-187">Enable the submit button based on form validation</span></span>

<span data-ttu-id="47c72-188">Povolení a zakázání tlačítka Odeslat na základě ověření formuláře:</span><span class="sxs-lookup"><span data-stu-id="47c72-188">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="47c72-189">K přiřazení modelu při inicializaci komponenty použijte `EditContext` formuláře.</span><span class="sxs-lookup"><span data-stu-id="47c72-189">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="47c72-190">Pokud chcete povolit a zakázat tlačítko Odeslat, ověřte formulář v `OnFieldChanged`ovém zpětném volání kontextu.</span><span class="sxs-lookup"><span data-stu-id="47c72-190">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>

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

<span data-ttu-id="47c72-191">V předchozím příkladu nastavte `_formInvalid` na `false`, pokud:</span><span class="sxs-lookup"><span data-stu-id="47c72-191">In the preceding example, set `_formInvalid` to `false` if:</span></span>

* <span data-ttu-id="47c72-192">Formulář je předem načten s platnými výchozími hodnotami.</span><span class="sxs-lookup"><span data-stu-id="47c72-192">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="47c72-193">Chcete povolit tlačítko Odeslat, když se formulář načte.</span><span class="sxs-lookup"><span data-stu-id="47c72-193">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="47c72-194">Vedlejším účinkem předchozího přístupu je, že komponenta `ValidationSummary` se naplní neplatnými poli poté, co uživatel pracuje s libovolným polem.</span><span class="sxs-lookup"><span data-stu-id="47c72-194">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="47c72-195">Tento scénář je možné vyřešit jedním z následujících způsobů:</span><span class="sxs-lookup"><span data-stu-id="47c72-195">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="47c72-196">Ve formuláři nepoužívejte komponentu `ValidationSummary`.</span><span class="sxs-lookup"><span data-stu-id="47c72-196">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="47c72-197">Zpřístupní komponentu `ValidationSummary`, když je vybráno tlačítko Odeslat (například v metodě `HandleValidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="47c72-197">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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
