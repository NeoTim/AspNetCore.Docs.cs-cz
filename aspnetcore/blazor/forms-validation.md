---
title: ASP.NET Core Blazor formuláře a ověřování
author: guardrex
description: Naučte se používat scénáře ověřování formulářů a polí v Blazor .
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
ms.openlocfilehash: d7182594fbc22d056caff0864a053a0a92fa4e84
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2020
ms.locfileid: "83438886"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="e6929-103">ASP.NET Core formuláře a ověřování Blazor</span><span class="sxs-lookup"><span data-stu-id="e6929-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="e6929-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e6929-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e6929-105">Formuláře a ověřování se v Blazor podporují pomocí [datových poznámek](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="e6929-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="e6929-106">Následující `ExampleModel` typ definuje logiku ověřování pomocí datových poznámek:</span><span class="sxs-lookup"><span data-stu-id="e6929-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="e6929-107">Formulář je definován pomocí `EditForm` komponenty.</span><span class="sxs-lookup"><span data-stu-id="e6929-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="e6929-108">Následující formulář ukazuje typické prvky, komponenty a kód Razor:</span><span class="sxs-lookup"><span data-stu-id="e6929-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="e6929-109">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e6929-109">In the preceding example:</span></span>

* <span data-ttu-id="e6929-110">Formulář ověřuje vstup uživatele v `name` poli pomocí ověřování definovaného v `ExampleModel` typu.</span><span class="sxs-lookup"><span data-stu-id="e6929-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="e6929-111">Model se vytvoří v bloku komponenty a je `@code` uložený v soukromém poli ( `exampleModel` ).</span><span class="sxs-lookup"><span data-stu-id="e6929-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="e6929-112">Pole je přiřazeno `Model` atributu `<EditForm>` elementu.</span><span class="sxs-lookup"><span data-stu-id="e6929-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="e6929-113">`InputText` `@bind-Value` Vazby komponenty:</span><span class="sxs-lookup"><span data-stu-id="e6929-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="e6929-114">Vlastnost modelu ( `exampleModel.Name` ) na `InputText` `Value` vlastnost součásti.</span><span class="sxs-lookup"><span data-stu-id="e6929-114">The model property (`exampleModel.Name`) to the `InputText` component's `Value` property.</span></span> <span data-ttu-id="e6929-115">Další informace o vazbách vlastností naleznete v tématu <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters> .</span><span class="sxs-lookup"><span data-stu-id="e6929-115">For more information on property binding, see <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="e6929-116">Delegát události změny pro `InputText` `ValueChanged` vlastnost komponenty.</span><span class="sxs-lookup"><span data-stu-id="e6929-116">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="e6929-117">`DataAnnotationsValidator`Komponenta připojuje ověřování pomocí datových poznámek.</span><span class="sxs-lookup"><span data-stu-id="e6929-117">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="e6929-118">`ValidationSummary`Komponenta shrnuje ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="e6929-118">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="e6929-119">`HandleValidSubmit`aktivuje se, když formulář úspěšně odešle (projde ověřením).</span><span class="sxs-lookup"><span data-stu-id="e6929-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="e6929-120">K dispozici je sada předdefinovaných vstupních komponent pro příjem a ověření vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="e6929-120">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="e6929-121">Vstupy jsou ověřovány při jejich změně a při odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="e6929-121">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="e6929-122">Dostupné vstupní komponenty jsou uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="e6929-122">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="e6929-123">Vstupní komponenta</span><span class="sxs-lookup"><span data-stu-id="e6929-123">Input component</span></span> | <span data-ttu-id="e6929-124">Vykresleno jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="e6929-124">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="e6929-125">Všechny vstupní komponenty, včetně `EditForm` , podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="e6929-125">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="e6929-126">Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do vykresleného prvku HTML.</span><span class="sxs-lookup"><span data-stu-id="e6929-126">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="e6929-127">Vstupní komponenty poskytují výchozí chování pro ověřování při úpravách a změně jejich třídy CSS tak, aby odrážely stav pole.</span><span class="sxs-lookup"><span data-stu-id="e6929-127">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="e6929-128">Některé součásti obsahují užitečnou logiku analýzy.</span><span class="sxs-lookup"><span data-stu-id="e6929-128">Some components include useful parsing logic.</span></span> <span data-ttu-id="e6929-129">Například `InputDate` a `InputNumber` zpracujte neanalyzovatelné hodnoty tak, že je zaregistrujete jako chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="e6929-129">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="e6929-130">Typy, které mohou přijmout hodnoty null, podporují také hodnotu null cílového pole (například `int?` ).</span><span class="sxs-lookup"><span data-stu-id="e6929-130">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="e6929-131">Následující `Starship` typ definuje logiku ověřování pomocí větší sady vlastností a poznámek k datům než dříve `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="e6929-131">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="e6929-132">V předchozím příkladu `Description` je volitelná, protože nejsou k dispozici žádné anotace dat.</span><span class="sxs-lookup"><span data-stu-id="e6929-132">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="e6929-133">Následující formulář ověří uživatelský vstup pomocí ověřování definovaného v `Starship` modelu:</span><span class="sxs-lookup"><span data-stu-id="e6929-133">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
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
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
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
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="e6929-134">`EditForm`Vytvoří `EditContext` jako [kaskádovou hodnotu](xref:blazor/components#cascading-values-and-parameters) , která sleduje metadata procesu úprav, včetně toho, která pole jsou upravena a aktuální zprávy ověření.</span><span class="sxs-lookup"><span data-stu-id="e6929-134">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="e6929-135">`EditForm`Poskytuje taky praktické události pro platná a neplatná odeslání ( `OnValidSubmit` , `OnInvalidSubmit` ).</span><span class="sxs-lookup"><span data-stu-id="e6929-135">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="e6929-136">Alternativně můžete použít `OnSubmit` k aktivaci ověřování a hodnot polí pro kontrolu pomocí vlastního ověřovacího kódu.</span><span class="sxs-lookup"><span data-stu-id="e6929-136">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="e6929-137">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e6929-137">In the following example:</span></span>

* <span data-ttu-id="e6929-138">`HandleSubmit`Metoda se spustí, když je vybráno tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="e6929-138">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="e6929-139">Formulář se ověří pomocí formuláře `EditContext` .</span><span class="sxs-lookup"><span data-stu-id="e6929-139">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="e6929-140">Formulář se dále ověří předáním `EditContext` `ServerValidate` metody, která volá koncový bod webového rozhraní API na serveru (*není zobrazený*).</span><span class="sxs-lookup"><span data-stu-id="e6929-140">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="e6929-141">Další kód se spustí v závislosti na výsledku ověřování na straně klienta a serveru kontrolou `isValid` .</span><span class="sxs-lookup"><span data-stu-id="e6929-141">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="e6929-142">InputText na základě události vstupu</span><span class="sxs-lookup"><span data-stu-id="e6929-142">InputText based on the input event</span></span>

<span data-ttu-id="e6929-143">Pomocí `InputText` komponenty můžete vytvořit vlastní komponentu, která `input` místo události používá událost `change` .</span><span class="sxs-lookup"><span data-stu-id="e6929-143">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="e6929-144">Vytvořte komponentu s následujícím kódem a použijte komponentu stejně jako `InputText` je použit:</span><span class="sxs-lookup"><span data-stu-id="e6929-144">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="e6929-145">Práce s přepínači</span><span class="sxs-lookup"><span data-stu-id="e6929-145">Work with radio buttons</span></span>

<span data-ttu-id="e6929-146">Při práci s přepínači ve formuláři je datová vazba zpracovávána jinak než jiné prvky, protože přepínače jsou vyhodnocovány jako skupina.</span><span class="sxs-lookup"><span data-stu-id="e6929-146">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="e6929-147">Hodnota každého přepínacího tlačítka je pevná, ale hodnota skupiny přepínačů je hodnota vybraného přepínacího tlačítka.</span><span class="sxs-lookup"><span data-stu-id="e6929-147">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="e6929-148">Následující příklad ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="e6929-148">The following example shows how to:</span></span>

* <span data-ttu-id="e6929-149">Zpracuje datovou vazbu pro skupinu přepínačů.</span><span class="sxs-lookup"><span data-stu-id="e6929-149">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="e6929-150">Podpora ověřování pomocí vlastní `InputRadio` součásti.</span><span class="sxs-lookup"><span data-stu-id="e6929-150">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="e6929-151">V následujícím `EditForm` příkladu se `InputRadio` k získání a ověření hodnocení od uživatele používá předchozí komponenta:</span><span class="sxs-lookup"><span data-stu-id="e6929-151">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

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

## <a name="validation-support"></a><span data-ttu-id="e6929-152">Podpora ověřování</span><span class="sxs-lookup"><span data-stu-id="e6929-152">Validation support</span></span>

<span data-ttu-id="e6929-153">`DataAnnotationsValidator`Komponenta připojuje ověřování pomocí datových poznámek do kaskády `EditContext` .</span><span class="sxs-lookup"><span data-stu-id="e6929-153">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="e6929-154">Povolení podpory pro ověřování pomocí datových poznámek vyžaduje toto explicitní gesto.</span><span class="sxs-lookup"><span data-stu-id="e6929-154">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="e6929-155">Chcete-li použít jiný systém ověřování než datové poznámky, nahraďte `DataAnnotationsValidator` vlastní implementaci.</span><span class="sxs-lookup"><span data-stu-id="e6929-155">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="e6929-156">Implementace ASP.NET Core je k dispozici pro kontrolu ve zdroji odkazu: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="e6929-156">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="e6929-157">provádí dva typy ověřování:</span><span class="sxs-lookup"><span data-stu-id="e6929-157"> performs two types of validation:</span></span>

* <span data-ttu-id="e6929-158">*Ověřování polí* se provede, když se karty uživatele nacházejí v poli.</span><span class="sxs-lookup"><span data-stu-id="e6929-158">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="e6929-159">Při ověřování pole `DataAnnotationsValidator` přidruží součást všechny hlášené výsledky ověření k poli.</span><span class="sxs-lookup"><span data-stu-id="e6929-159">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="e6929-160">*Ověřování modelu* se provede, když uživatel formulář odešle.</span><span class="sxs-lookup"><span data-stu-id="e6929-160">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="e6929-161">Při ověřování modelu se `DataAnnotationsValidator` Komponenta pokusí určit pole na základě názvu člena, který sestavy výsledků ověření.</span><span class="sxs-lookup"><span data-stu-id="e6929-161">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="e6929-162">Výsledky ověření, které nejsou přidruženy k jednotlivým členům, jsou přidruženy k modelu, nikoli poli.</span><span class="sxs-lookup"><span data-stu-id="e6929-162">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="e6929-163">Komponenty zprávy pro Shrnutí a ověření</span><span class="sxs-lookup"><span data-stu-id="e6929-163">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="e6929-164">`ValidationSummary`Komponenta shrnuje všechny zprávy o ověřování, které se podobají [pomocníka značek pro Shrnutí ověření](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="e6929-164">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="e6929-165">Zprávy ověřování výstupu pro konkrétní model s `Model` parametrem:</span><span class="sxs-lookup"><span data-stu-id="e6929-165">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="e6929-166">`ValidationMessage`Komponenta zobrazí ověřovací zprávy pro konkrétní pole, které se podobá [pomocníka značek ověřovací zprávy](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e6929-166">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="e6929-167">Zadejte pole pro ověření s `For` atributem a výrazem lambda pojmenování vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="e6929-167">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="e6929-168">`ValidationMessage`Komponenty a `ValidationSummary` podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="e6929-168">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="e6929-169">Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do generovaného `<div>` `<ul>` prvku nebo.</span><span class="sxs-lookup"><span data-stu-id="e6929-169">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="e6929-170">Vlastní ověřovací atributy</span><span class="sxs-lookup"><span data-stu-id="e6929-170">Custom validation attributes</span></span>

<span data-ttu-id="e6929-171">Chcete-li zajistit, aby byl výsledek ověření správně přidružen k poli při použití [vlastního ověřovacího atributu](xref:mvc/models/validation#custom-attributes), předejte kontext ověření <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> při vytváření <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="e6929-171">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="blazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="e6929-172">balíček pro ověření datových poznámek</span><span class="sxs-lookup"><span data-stu-id="e6929-172"> data annotations validation package</span></span>

<span data-ttu-id="e6929-173">[Microsoft. AspNetCore. Components. Dataanotaces. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) je balíček, který vyplní mezery při ověřování pomocí `DataAnnotationsValidator` komponenty.</span><span class="sxs-lookup"><span data-stu-id="e6929-173">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="e6929-174">Balíček je momentálně *experimentální*.</span><span class="sxs-lookup"><span data-stu-id="e6929-174">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="e6929-175">[CompareProperty] – atribut</span><span class="sxs-lookup"><span data-stu-id="e6929-175">[CompareProperty] attribute</span></span>

<span data-ttu-id="e6929-176"><xref:System.ComponentModel.DataAnnotations.CompareAttribute>Nefunguje dobře s `DataAnnotationsValidator` komponentou, protože nepřiřazuje výsledek ověření k určitému členu.</span><span class="sxs-lookup"><span data-stu-id="e6929-176">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="e6929-177">To může vést k nekonzistentnímu chování mezi ověřováním na úrovni polí a při ověřování celého modelu při odeslání.</span><span class="sxs-lookup"><span data-stu-id="e6929-177">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="e6929-178">Balíček [Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experiment* zavádí další ověřovací atribut, `ComparePropertyAttribute` který tato omezení funguje.</span><span class="sxs-lookup"><span data-stu-id="e6929-178">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="e6929-179">V Blazor aplikaci `[CompareProperty]` je přímá náhrada za `[Compare]` atribut.</span><span class="sxs-lookup"><span data-stu-id="e6929-179">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="e6929-180">Vnořené modely, typy kolekcí a komplexní typy</span><span class="sxs-lookup"><span data-stu-id="e6929-180">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="e6929-181">poskytuje podporu pro ověřování vstupu formuláře pomocí datových poznámek s integrovaným `DataAnnotationsValidator` .</span><span class="sxs-lookup"><span data-stu-id="e6929-181"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="e6929-182">`DataAnnotationsValidator`Pouze ověří vlastnosti nejvyšší úrovně modelu svázaného s formulářem, který není vlastností kolekce nebo komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="e6929-182">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="e6929-183">Chcete-li ověřit celý graf objektu vázaného modelu, včetně vlastností kolekce a komplexního typu, použijte rozhraní, které `ObjectGraphDataAnnotationsValidator` poskytuje *experimentální* balíček [Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="e6929-183">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="e6929-184">Přidávejte do vlastností modelu poznámky `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="e6929-184">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="e6929-185">V následujících třídách modelu `ShipDescription` obsahuje Třída další datové poznámky pro ověření, když je model svázán s formulářem:</span><span class="sxs-lookup"><span data-stu-id="e6929-185">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="e6929-186">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="e6929-186">*Starship.cs*:</span></span>

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

<span data-ttu-id="e6929-187">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="e6929-187">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="e6929-188">Povolit tlačítko Odeslat na základě ověření formuláře</span><span class="sxs-lookup"><span data-stu-id="e6929-188">Enable the submit button based on form validation</span></span>

<span data-ttu-id="e6929-189">Povolení a zakázání tlačítka Odeslat na základě ověření formuláře:</span><span class="sxs-lookup"><span data-stu-id="e6929-189">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="e6929-190">`EditContext`K přiřazení modelu při inicializaci komponenty použijte formulář.</span><span class="sxs-lookup"><span data-stu-id="e6929-190">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="e6929-191">Ověřte formulář v `OnFieldChanged` zpětném volání kontextu a povolte a zakažte tlačítko Odeslat.</span><span class="sxs-lookup"><span data-stu-id="e6929-191">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="e6929-192">Odpojte obslužnou rutinu události v `Dispose` metodě.</span><span class="sxs-lookup"><span data-stu-id="e6929-192">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="e6929-193">Další informace naleznete v tématu <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="e6929-193">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="e6929-194">V předchozím příkladu nastavte `formInvalid` na `false` if:</span><span class="sxs-lookup"><span data-stu-id="e6929-194">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="e6929-195">Formulář je předem načten s platnými výchozími hodnotami.</span><span class="sxs-lookup"><span data-stu-id="e6929-195">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="e6929-196">Chcete povolit tlačítko Odeslat, když se formulář načte.</span><span class="sxs-lookup"><span data-stu-id="e6929-196">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="e6929-197">Vedlejším účinkem předcházejícího přístupu je, že `ValidationSummary` Komponenta je naplněná neplatnými poli poté, co uživatel komunikuje s jakýmkoli jedním polem.</span><span class="sxs-lookup"><span data-stu-id="e6929-197">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="e6929-198">Tento scénář je možné vyřešit jedním z následujících způsobů:</span><span class="sxs-lookup"><span data-stu-id="e6929-198">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="e6929-199">Nepoužívejte `ValidationSummary` komponentu na formuláři.</span><span class="sxs-lookup"><span data-stu-id="e6929-199">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="e6929-200">Zpřístupní `ValidationSummary` komponentu, když je vybráno tlačítko Odeslat (například v `HandleValidSubmit` metodě).</span><span class="sxs-lookup"><span data-stu-id="e6929-200">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```
