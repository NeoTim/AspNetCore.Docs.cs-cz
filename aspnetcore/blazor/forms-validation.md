---
title: ASP.NET Core Blazor formuláře a ověřování
author: guardrex
description: Naučte se používat scénáře ověřování formulářů a polí v Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 925051d7426470aebfddbdb5ff83d7dab9f82726
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944429"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="e17d6-103">ASP.NET Core Blazor formuláře a ověřování</span><span class="sxs-lookup"><span data-stu-id="e17d6-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="e17d6-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e17d6-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e17d6-105">Formuláře a ověřování jsou podporovány v Blazor použití [datových poznámek](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="e17d6-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="e17d6-106">Následující `ExampleModel` typ definuje logiku ověřování pomocí datových poznámek:</span><span class="sxs-lookup"><span data-stu-id="e17d6-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="e17d6-107">Formulář je definován pomocí <xref:Microsoft.AspNetCore.Components.Forms.EditForm> komponenty.</span><span class="sxs-lookup"><span data-stu-id="e17d6-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="e17d6-108">Následující formulář ukazuje typické prvky, komponenty a Razor kód:</span><span class="sxs-lookup"><span data-stu-id="e17d6-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="e17d6-109">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e17d6-109">In the preceding example:</span></span>

* <span data-ttu-id="e17d6-110">Formulář ověřuje vstup uživatele v `name` poli pomocí ověřování definovaného v `ExampleModel` typu.</span><span class="sxs-lookup"><span data-stu-id="e17d6-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="e17d6-111">Model se vytvoří v bloku komponenty a je `@code` uložený v soukromém poli ( `exampleModel` ).</span><span class="sxs-lookup"><span data-stu-id="e17d6-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="e17d6-112">Pole je přiřazeno `Model` atributu `<EditForm>` elementu.</span><span class="sxs-lookup"><span data-stu-id="e17d6-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="e17d6-113"><xref:Microsoft.AspNetCore.Components.Forms.InputText> `@bind-Value` Vazby komponenty:</span><span class="sxs-lookup"><span data-stu-id="e17d6-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="e17d6-114">Vlastnost modelu ( `exampleModel.Name` ) na <xref:Microsoft.AspNetCore.Components.Forms.InputText> `Value` vlastnost součásti.</span><span class="sxs-lookup"><span data-stu-id="e17d6-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="e17d6-115">Další informace o vazbách vlastností naleznete v tématu <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters> .</span><span class="sxs-lookup"><span data-stu-id="e17d6-115">For more information on property binding, see <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="e17d6-116">Delegát události změny pro <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` vlastnost komponenty.</span><span class="sxs-lookup"><span data-stu-id="e17d6-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="e17d6-117"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Komponenta připojuje ověřování pomocí datových poznámek.</span><span class="sxs-lookup"><span data-stu-id="e17d6-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="e17d6-118"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Komponenta shrnuje ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="e17d6-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="e17d6-119">`HandleValidSubmit`aktivuje se, když formulář úspěšně odešle (projde ověřením).</span><span class="sxs-lookup"><span data-stu-id="e17d6-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="e17d6-120">K dispozici je sada předdefinovaných vstupních komponent pro příjem a ověření vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="e17d6-120">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="e17d6-121">Vstupy jsou ověřovány při jejich změně a při odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="e17d6-121">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="e17d6-122">Dostupné vstupní komponenty jsou uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="e17d6-122">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="e17d6-123">Vstupní komponenta</span><span class="sxs-lookup"><span data-stu-id="e17d6-123">Input component</span></span> | <span data-ttu-id="e17d6-124">Vykresleno jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="e17d6-124">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |

<span data-ttu-id="e17d6-125">Všechny vstupní komponenty, včetně <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="e17d6-125">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="e17d6-126">Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do vykresleného prvku HTML.</span><span class="sxs-lookup"><span data-stu-id="e17d6-126">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="e17d6-127">Vstupní komponenty poskytují výchozí chování pro ověřování při úpravách a změně jejich třídy CSS tak, aby odrážely stav pole.</span><span class="sxs-lookup"><span data-stu-id="e17d6-127">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="e17d6-128">Některé součásti obsahují užitečnou logiku analýzy.</span><span class="sxs-lookup"><span data-stu-id="e17d6-128">Some components include useful parsing logic.</span></span> <span data-ttu-id="e17d6-129">Například <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> a <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> zpracujte neanalyzovatelné hodnoty tak, že je zaregistrujete jako chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="e17d6-129">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="e17d6-130">Typy, které mohou přijmout hodnoty null, podporují také hodnotu null cílového pole (například `int?` ).</span><span class="sxs-lookup"><span data-stu-id="e17d6-130">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="e17d6-131">Následující `Starship` typ definuje logiku ověřování pomocí větší sady vlastností a poznámek k datům než dříve `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="e17d6-131">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="e17d6-132">V předchozím příkladu `Description` je volitelná, protože nejsou k dispozici žádné anotace dat.</span><span class="sxs-lookup"><span data-stu-id="e17d6-132">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="e17d6-133">Následující formulář ověří uživatelský vstup pomocí ověřování definovaného v `Starship` modelu:</span><span class="sxs-lookup"><span data-stu-id="e17d6-133">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="e17d6-134"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Vytvoří <xref:Microsoft.AspNetCore.Components.Forms.EditContext> jako [kaskádovou hodnotu](xref:blazor/components/cascading-values-and-parameters) , která sleduje metadata procesu úprav, včetně toho, která pole jsou upravena a aktuální zprávy ověření.</span><span class="sxs-lookup"><span data-stu-id="e17d6-134">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="e17d6-135"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Poskytuje taky praktické události pro platná a neplatná odeslání ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> , <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit> ).</span><span class="sxs-lookup"><span data-stu-id="e17d6-135">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="e17d6-136">Alternativně můžete použít <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> k aktivaci ověřování a hodnot polí pro kontrolu pomocí vlastního ověřovacího kódu.</span><span class="sxs-lookup"><span data-stu-id="e17d6-136">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="e17d6-137">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e17d6-137">In the following example:</span></span>

* <span data-ttu-id="e17d6-138">`HandleSubmit`Metoda se spustí, když **`Submit`** je vybráno tlačítko.</span><span class="sxs-lookup"><span data-stu-id="e17d6-138">The `HandleSubmit` method runs when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="e17d6-139">Formulář se ověří pomocí formuláře <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="e17d6-139">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="e17d6-140">Formulář se dále ověří předáním <xref:Microsoft.AspNetCore.Components.Forms.EditContext> `ServerValidate` metody, která volá koncový bod webového rozhraní API na serveru (*není zobrazený*).</span><span class="sxs-lookup"><span data-stu-id="e17d6-140">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="e17d6-141">Další kód se spustí v závislosti na výsledku ověřování na straně klienta a serveru kontrolou `isValid` .</span><span class="sxs-lookup"><span data-stu-id="e17d6-141">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="e17d6-142">InputText na základě události vstupu</span><span class="sxs-lookup"><span data-stu-id="e17d6-142">InputText based on the input event</span></span>

<span data-ttu-id="e17d6-143">Pomocí <xref:Microsoft.AspNetCore.Components.Forms.InputText> komponenty můžete vytvořit vlastní komponentu, která `input` místo události používá událost `change` .</span><span class="sxs-lookup"><span data-stu-id="e17d6-143">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="e17d6-144">V následujícím příkladu `CustomInputText` Komponenta dědí `InputText` komponentu rozhraní a nastaví vazbu na událost ( <xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A> ) na `oninput` událost.</span><span class="sxs-lookup"><span data-stu-id="e17d6-144">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="e17d6-145">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="e17d6-145">`Shared/CustomInputText.razor`:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue"
    @oninput="EventCallback.Factory.CreateBinder<string>(
         this, __value => CurrentValueAsString = __value, 
         CurrentValueAsString)" />
```

<span data-ttu-id="e17d6-146">`CustomInputText`Komponentu lze použít kdekoli <xref:Microsoft.AspNetCore.Components.Forms.InputText> :</span><span class="sxs-lookup"><span data-stu-id="e17d6-146">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="e17d6-147">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="e17d6-147">`Pages/TestForm.razor`:</span></span>

```razor
@page  "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="e17d6-148">Přepínače</span><span class="sxs-lookup"><span data-stu-id="e17d6-148">Radio buttons</span></span>

<span data-ttu-id="e17d6-149">Při práci s přepínači ve formuláři je datová vazba zpracovávána jinak než jiné prvky, protože přepínače jsou vyhodnocovány jako skupina.</span><span class="sxs-lookup"><span data-stu-id="e17d6-149">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="e17d6-150">Hodnota každého přepínacího tlačítka je pevná, ale hodnota skupiny přepínačů je hodnota vybraného přepínacího tlačítka.</span><span class="sxs-lookup"><span data-stu-id="e17d6-150">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="e17d6-151">Následující příklad ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="e17d6-151">The following example shows how to:</span></span>

* <span data-ttu-id="e17d6-152">Zpracuje datovou vazbu pro skupinu přepínačů.</span><span class="sxs-lookup"><span data-stu-id="e17d6-152">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="e17d6-153">Podpora ověřování pomocí vlastní `InputRadio` součásti.</span><span class="sxs-lookup"><span data-stu-id="e17d6-153">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="e17d6-154">V následujícím <xref:Microsoft.AspNetCore.Components.Forms.EditForm> příkladu se `InputRadio` k získání a ověření hodnocení od uživatele používá předchozí komponenta:</span><span class="sxs-lookup"><span data-stu-id="e17d6-154">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="e17d6-155">Vazba `<select>` možností elementu na hodnoty objektu C# `null`</span><span class="sxs-lookup"><span data-stu-id="e17d6-155">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="e17d6-156">Neexistuje žádný rozumné způsob, jak vyjádřit `<select>` hodnotu možnosti prvku jako hodnotu objektu C# `null` , protože:</span><span class="sxs-lookup"><span data-stu-id="e17d6-156">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="e17d6-157">Atributy HTML nemohou mít `null` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="e17d6-157">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="e17d6-158">Nejbližší ekvivalent k `null` v jazyce HTML je absence `value` atributu HTML z `<option>` elementu.</span><span class="sxs-lookup"><span data-stu-id="e17d6-158">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="e17d6-159">Při výběru možnosti bez `<option>` `value` atributu prohlížeč považuje hodnotu za *textový obsah* `<option>` elementu.</span><span class="sxs-lookup"><span data-stu-id="e17d6-159">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="e17d6-160">BlazorRozhraní se nepokouší potlačit výchozí chování, protože by zahrnovalo:</span><span class="sxs-lookup"><span data-stu-id="e17d6-160">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="e17d6-161">Vytvoření řetězce alternativních řešení zvláštních případů v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e17d6-161">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="e17d6-162">Zásadní změny v aktuálním chování architektury.</span><span class="sxs-lookup"><span data-stu-id="e17d6-162">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e17d6-163">Plausible `null` ekvivalent v HTML je *prázdný řetězec* `value` .</span><span class="sxs-lookup"><span data-stu-id="e17d6-163">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="e17d6-164">BlazorArchitektura zpracovává `null` prázdné převody řetězce pro oboustrannou vazbu na `<select>` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="e17d6-164">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="e17d6-165">Blazor `null` Při pokusu o obousměrnou vazbu na hodnotu se rozhraní nezpracovává automaticky k prázdným převodům řetězců `<select>` .</span><span class="sxs-lookup"><span data-stu-id="e17d6-165">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="e17d6-166">Další informace naleznete v tématu [Oprava vazby `<select>` na hodnotu null (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span><span class="sxs-lookup"><span data-stu-id="e17d6-166">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="e17d6-167">Podpora ověřování</span><span class="sxs-lookup"><span data-stu-id="e17d6-167">Validation support</span></span>

<span data-ttu-id="e17d6-168"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Komponenta připojuje ověřování pomocí datových poznámek do kaskády <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="e17d6-168">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="e17d6-169">Povolení podpory pro ověřování pomocí datových poznámek vyžaduje toto explicitní gesto.</span><span class="sxs-lookup"><span data-stu-id="e17d6-169">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="e17d6-170">Chcete-li použít jiný systém ověřování než datové poznámky, nahraďte <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> vlastní implementaci.</span><span class="sxs-lookup"><span data-stu-id="e17d6-170">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="e17d6-171">Implementace ASP.NET Core je k dispozici pro kontrolu v referenčním zdroji: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) .</span><span class="sxs-lookup"><span data-stu-id="e17d6-171">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="e17d6-172">Předchozí odkazy na zdroj referencí poskytují kód z `master` větve úložiště, která představuje aktuální vývoj jednotky produktu pro příští vydání ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e17d6-172">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="e17d6-173">Pokud chcete vybrat větev pro jinou verzi, použijte selektor větve GitHubu (například `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="e17d6-173">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

Blazor<span data-ttu-id="e17d6-174">provádí dva typy ověřování:</span><span class="sxs-lookup"><span data-stu-id="e17d6-174"> performs two types of validation:</span></span>

* <span data-ttu-id="e17d6-175">*Ověřování polí* se provede, když se karty uživatele nacházejí v poli.</span><span class="sxs-lookup"><span data-stu-id="e17d6-175">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="e17d6-176">Při ověřování pole <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> přidruží součást všechny hlášené výsledky ověření k poli.</span><span class="sxs-lookup"><span data-stu-id="e17d6-176">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="e17d6-177">*Ověřování modelu* se provede, když uživatel formulář odešle.</span><span class="sxs-lookup"><span data-stu-id="e17d6-177">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="e17d6-178">Při ověřování modelu se <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Komponenta pokusí určit pole na základě názvu člena, který sestavy výsledků ověření.</span><span class="sxs-lookup"><span data-stu-id="e17d6-178">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="e17d6-179">Výsledky ověření, které nejsou přidruženy k jednotlivým členům, jsou přidruženy k modelu, nikoli poli.</span><span class="sxs-lookup"><span data-stu-id="e17d6-179">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="e17d6-180">Komponenty zprávy pro Shrnutí a ověření</span><span class="sxs-lookup"><span data-stu-id="e17d6-180">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="e17d6-181"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Komponenta shrnuje všechny zprávy o ověřování, které se podobají [pomocníka značek pro Shrnutí ověření](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="e17d6-181">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="e17d6-182">Zprávy ověřování výstupu pro konkrétní model s `Model` parametrem:</span><span class="sxs-lookup"><span data-stu-id="e17d6-182">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="e17d6-183"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Komponenta zobrazí ověřovací zprávy pro konkrétní pole, které se podobá [pomocníka značek ověřovací zprávy](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e17d6-183">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="e17d6-184">Zadejte pole pro ověření s `For` atributem a výrazem lambda pojmenování vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="e17d6-184">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="e17d6-185"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Komponenty a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="e17d6-185">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="e17d6-186">Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do generovaného `<div>` `<ul>` prvku nebo.</span><span class="sxs-lookup"><span data-stu-id="e17d6-186">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="e17d6-187">Vlastní ověřovací atributy</span><span class="sxs-lookup"><span data-stu-id="e17d6-187">Custom validation attributes</span></span>

<span data-ttu-id="e17d6-188">Chcete-li zajistit, aby byl výsledek ověření správně přidružen k poli při použití [vlastního ověřovacího atributu](xref:mvc/models/validation#custom-attributes), předejte kontext ověření <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> při vytváření <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="e17d6-188">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
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

> [!NOTE]
> <span data-ttu-id="e17d6-189"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> je `null`.</span><span class="sxs-lookup"><span data-stu-id="e17d6-189"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="e17d6-190">Vložené služby pro ověřování v metodě se `IsValid` nepodporují.</span><span class="sxs-lookup"><span data-stu-id="e17d6-190">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

### <a name="blazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="e17d6-191">balíček pro ověření datových poznámek</span><span class="sxs-lookup"><span data-stu-id="e17d6-191"> data annotations validation package</span></span>

<span data-ttu-id="e17d6-192">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)Je balíček, který vyplní prodlevy při ověřování pomocí <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponenty.</span><span class="sxs-lookup"><span data-stu-id="e17d6-192">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="e17d6-193">Balíček je momentálně *experimentální*.</span><span class="sxs-lookup"><span data-stu-id="e17d6-193">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="e17d6-194">[CompareProperty] – atribut</span><span class="sxs-lookup"><span data-stu-id="e17d6-194">[CompareProperty] attribute</span></span>

<span data-ttu-id="e17d6-195"><xref:System.ComponentModel.DataAnnotations.CompareAttribute>Nefunguje dobře s <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponentou, protože nepřiřazuje výsledek ověření k určitému členu.</span><span class="sxs-lookup"><span data-stu-id="e17d6-195">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="e17d6-196">To může vést k nekonzistentnímu chování mezi ověřováním na úrovni polí a při ověřování celého modelu při odeslání.</span><span class="sxs-lookup"><span data-stu-id="e17d6-196">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="e17d6-197">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *Experimentální* balíček zavádí další ověřovací atribut, který tato `ComparePropertyAttribute` omezení funguje.</span><span class="sxs-lookup"><span data-stu-id="e17d6-197">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="e17d6-198">V Blazor aplikaci `[CompareProperty]` je přímá náhrada za [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="e17d6-198">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="e17d6-199">Vnořené modely, typy kolekcí a komplexní typy</span><span class="sxs-lookup"><span data-stu-id="e17d6-199">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="e17d6-200">poskytuje podporu pro ověřování vstupu formuláře pomocí datových poznámek s integrovaným <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="e17d6-200"> provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="e17d6-201"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Pouze ověří vlastnosti nejvyšší úrovně modelu svázaného s formulářem, který není vlastností kolekce nebo komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="e17d6-201">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="e17d6-202">Chcete-li ověřit celý graf objektu vázaného modelu, včetně vlastností kolekce a komplexního typu, použijte příkaz `ObjectGraphDataAnnotationsValidator` poskytnutý *experimentálním* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) balíčkem:</span><span class="sxs-lookup"><span data-stu-id="e17d6-202">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="e17d6-203">Přidávejte do vlastností modelu poznámky `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="e17d6-203">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="e17d6-204">V následujících třídách modelu `ShipDescription` obsahuje Třída další datové poznámky pro ověření, když je model svázán s formulářem:</span><span class="sxs-lookup"><span data-stu-id="e17d6-204">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="e17d6-205">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="e17d6-205">`Starship.cs`:</span></span>

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

<span data-ttu-id="e17d6-206">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="e17d6-206">`ShipDescription.cs`:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="e17d6-207">Povolit tlačítko Odeslat na základě ověření formuláře</span><span class="sxs-lookup"><span data-stu-id="e17d6-207">Enable the submit button based on form validation</span></span>

<span data-ttu-id="e17d6-208">Povolení a zakázání tlačítka Odeslat na základě ověření formuláře:</span><span class="sxs-lookup"><span data-stu-id="e17d6-208">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="e17d6-209"><xref:Microsoft.AspNetCore.Components.Forms.EditContext>K přiřazení modelu při inicializaci komponenty použijte formulář.</span><span class="sxs-lookup"><span data-stu-id="e17d6-209">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="e17d6-210">Ověřte formulář v <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> zpětném volání kontextu a povolte a zakažte tlačítko Odeslat.</span><span class="sxs-lookup"><span data-stu-id="e17d6-210">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="e17d6-211">Odpojte obslužnou rutinu události v `Dispose` metodě.</span><span class="sxs-lookup"><span data-stu-id="e17d6-211">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="e17d6-212">Další informace naleznete v tématu <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="e17d6-212">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

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

<span data-ttu-id="e17d6-213">V předchozím příkladu nastavte `formInvalid` na `false` if:</span><span class="sxs-lookup"><span data-stu-id="e17d6-213">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="e17d6-214">Formulář je předem načten s platnými výchozími hodnotami.</span><span class="sxs-lookup"><span data-stu-id="e17d6-214">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="e17d6-215">Chcete povolit tlačítko Odeslat, když se formulář načte.</span><span class="sxs-lookup"><span data-stu-id="e17d6-215">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="e17d6-216">Vedlejším účinkem předcházejícího přístupu je, že <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> Komponenta je naplněná neplatnými poli poté, co uživatel komunikuje s jakýmkoli jedním polem.</span><span class="sxs-lookup"><span data-stu-id="e17d6-216">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="e17d6-217">Tento scénář je možné vyřešit jedním z následujících způsobů:</span><span class="sxs-lookup"><span data-stu-id="e17d6-217">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="e17d6-218">Nepoužívejte <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> komponentu na formuláři.</span><span class="sxs-lookup"><span data-stu-id="e17d6-218">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="e17d6-219">Zpřístupní <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> komponentu, když je vybráno tlačítko Odeslat (například v `HandleValidSubmit` metodě).</span><span class="sxs-lookup"><span data-stu-id="e17d6-219">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="e17d6-220">Odstranit potíže</span><span class="sxs-lookup"><span data-stu-id="e17d6-220">Troubleshoot</span></span>

> <span data-ttu-id="e17d6-221">InvalidOperationException: EditForm vyžaduje parametr modelu nebo parametr EditContext, ale ne obojí.</span><span class="sxs-lookup"><span data-stu-id="e17d6-221">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="e17d6-222">Potvrďte, že <xref:Microsoft.AspNetCore.Components.Forms.EditForm> má <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> nebo <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="e17d6-222">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> or <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="e17d6-223">Při přiřazování <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> do formuláře potvrďte, že je vytvořen typ modelu, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="e17d6-223">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
