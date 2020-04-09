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
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="7508f-103">ASP.NET core blazor formuláře a validace</span><span class="sxs-lookup"><span data-stu-id="7508f-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="7508f-104">[Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7508f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7508f-105">Formuláře a validace jsou podporovány v Blazoru pomocí [datových anotací](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="7508f-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="7508f-106">Následující `ExampleModel` typ definuje logiku ověřování pomocí datových anotací:</span><span class="sxs-lookup"><span data-stu-id="7508f-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="7508f-107">Formulář je definován `EditForm` pomocí komponenty.</span><span class="sxs-lookup"><span data-stu-id="7508f-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="7508f-108">Následující formulář ukazuje typické prvky, součásti a kód Razor:</span><span class="sxs-lookup"><span data-stu-id="7508f-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="7508f-109">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="7508f-109">In the preceding example:</span></span>

* <span data-ttu-id="7508f-110">Formulář ověří vstup uživatele `name` v poli pomocí `ExampleModel` ověření definovaného v typu.</span><span class="sxs-lookup"><span data-stu-id="7508f-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="7508f-111">Model je vytvořen v bloku `@code` komponenty a držen`_exampleModel`v soukromém poli ( ).</span><span class="sxs-lookup"><span data-stu-id="7508f-111">The model is created in the component's `@code` block and held in a private field (`_exampleModel`).</span></span> <span data-ttu-id="7508f-112">Pole je přiřazeno `Model` atributu `<EditForm>` prvku.</span><span class="sxs-lookup"><span data-stu-id="7508f-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="7508f-113">Složky `InputText` se `@bind-Value` váže:</span><span class="sxs-lookup"><span data-stu-id="7508f-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="7508f-114">Vlastnost modelu`_exampleModel.Name`( ) `InputText` k `Value` vlastnosti komponenty.</span><span class="sxs-lookup"><span data-stu-id="7508f-114">The model property (`_exampleModel.Name`) to the `InputText` component's `Value` property.</span></span>
  * <span data-ttu-id="7508f-115">Delegát události změny `InputText` na vlastnost `ValueChanged` komponenty.</span><span class="sxs-lookup"><span data-stu-id="7508f-115">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="7508f-116">Komponenta `DataAnnotationsValidator` připojuje podporu ověřování pomocí datových poznámk.</span><span class="sxs-lookup"><span data-stu-id="7508f-116">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="7508f-117">Komponenta `ValidationSummary` shrnuje ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="7508f-117">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="7508f-118">`HandleValidSubmit`se aktivuje, když se formulář úspěšně odešle (projde ověřením).</span><span class="sxs-lookup"><span data-stu-id="7508f-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="7508f-119">Sada vestavěných vstupních součástí je k dispozici pro příjem a ověřování vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="7508f-119">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="7508f-120">Vstupy jsou ověřeny při změně a při odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="7508f-120">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="7508f-121">Dostupné vstupní součásti jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="7508f-121">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="7508f-122">Vstupní komponenta</span><span class="sxs-lookup"><span data-stu-id="7508f-122">Input component</span></span> | <span data-ttu-id="7508f-123">Vykresleno jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="7508f-123">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="7508f-124">Všechny vstupní součásti, `EditForm`včetně , podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="7508f-124">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="7508f-125">Do vykresleného prvku HTML se přidá libovolný atribut, který neodpovídá parametru komponenty.</span><span class="sxs-lookup"><span data-stu-id="7508f-125">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="7508f-126">Vstupní součásti poskytují výchozí chování pro ověřování při úpravách a změně třídy CSS tak, aby odrážela stav pole.</span><span class="sxs-lookup"><span data-stu-id="7508f-126">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="7508f-127">Některé součásti obsahují užitečnou logiku analýzy.</span><span class="sxs-lookup"><span data-stu-id="7508f-127">Some components include useful parsing logic.</span></span> <span data-ttu-id="7508f-128">Například `InputDate` a `InputNumber` řádně zpracovat neanalyzovatelné hodnoty jejich registrací jako chyby ověření.</span><span class="sxs-lookup"><span data-stu-id="7508f-128">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="7508f-129">Typy, které mohou přijímat hodnoty null, také podporují `int?`nullability cílového pole (například).</span><span class="sxs-lookup"><span data-stu-id="7508f-129">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="7508f-130">Následující `Starship` typ definuje logiku ověření pomocí větší sady vlastností a `ExampleModel`datových anotací než předchozí :</span><span class="sxs-lookup"><span data-stu-id="7508f-130">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="7508f-131">V předchozím příkladu `Description` je volitelné, protože nejsou k dispozici žádné poznámky dat.</span><span class="sxs-lookup"><span data-stu-id="7508f-131">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="7508f-132">Následující formulář ověřuje vstup uživatele `Starship` pomocí ověření definovaného v modelu:</span><span class="sxs-lookup"><span data-stu-id="7508f-132">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="7508f-133">Vytvoří `EditForm` `EditContext` jako [kaskádovou hodnotu,](xref:blazor/components#cascading-values-and-parameters) která sleduje metadata o procesu úprav, včetně polí, která byla změněna, a aktuálních ověřovacích zpráv.</span><span class="sxs-lookup"><span data-stu-id="7508f-133">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="7508f-134">Poskytuje `EditForm` také pohodlné události pro platné`OnValidSubmit`a `OnInvalidSubmit`neplatné odeslání ( , ).</span><span class="sxs-lookup"><span data-stu-id="7508f-134">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="7508f-135">Případně můžete `OnSubmit` použít k aktivaci hodnoty ověřovacího a kontrolního pole s vlastním ověřovacím kódem.</span><span class="sxs-lookup"><span data-stu-id="7508f-135">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="7508f-136">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="7508f-136">In the following example:</span></span>

* <span data-ttu-id="7508f-137">Metoda `HandleSubmit` se spustí, když je vybráno tlačítko **Odeslat.**</span><span class="sxs-lookup"><span data-stu-id="7508f-137">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="7508f-138">Formulář je ověřen pomocí formuláře `EditContext`.</span><span class="sxs-lookup"><span data-stu-id="7508f-138">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="7508f-139">Formulář je dále ověřen předáním `EditContext` `ServerValidate` metody, která volá koncový bod webového rozhraní API na serveru *(není zobrazen).*</span><span class="sxs-lookup"><span data-stu-id="7508f-139">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="7508f-140">Další kód je spuštěn v závislosti na výsledku ověření na `isValid`straně klienta a serveru kontrolou .</span><span class="sxs-lookup"><span data-stu-id="7508f-140">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="7508f-141">InputText založený na vstupní události</span><span class="sxs-lookup"><span data-stu-id="7508f-141">InputText based on the input event</span></span>

<span data-ttu-id="7508f-142">Komponenta `InputText` slouží k vytvoření vlastní `input` součásti, `change` která používá událost namísto události.</span><span class="sxs-lookup"><span data-stu-id="7508f-142">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="7508f-143">Vytvořte komponentu s následujícími značkami a `InputText` použijte ji tak, jak se používá:</span><span class="sxs-lookup"><span data-stu-id="7508f-143">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="7508f-144">Práce s přepínacími tlačítky</span><span class="sxs-lookup"><span data-stu-id="7508f-144">Work with radio buttons</span></span>

<span data-ttu-id="7508f-145">Při práci s přepínači ve formuláři je datová vazba zpracována jinak než jiné prvky, protože přepínací tlačítka jsou vyhodnocována jako skupina.</span><span class="sxs-lookup"><span data-stu-id="7508f-145">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="7508f-146">Hodnota každého přepínacího tlačítka je pevná, ale hodnota skupiny přepínacích tlačítek je hodnota vybraného přepínacího tlačítka.</span><span class="sxs-lookup"><span data-stu-id="7508f-146">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="7508f-147">Následující příklad ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="7508f-147">The following example shows how to:</span></span>

* <span data-ttu-id="7508f-148">Zpracování datové vazby pro skupinu přepínacích tlačítek.</span><span class="sxs-lookup"><span data-stu-id="7508f-148">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="7508f-149">Podporujte ověřování `InputRadio` pomocí vlastní součásti.</span><span class="sxs-lookup"><span data-stu-id="7508f-149">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="7508f-150">Následující `EditForm` používá předchozí `InputRadio` součást získat a ověřit hodnocení od uživatele:</span><span class="sxs-lookup"><span data-stu-id="7508f-150">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="validation-support"></a><span data-ttu-id="7508f-151">Podpora pro ověření</span><span class="sxs-lookup"><span data-stu-id="7508f-151">Validation support</span></span>

<span data-ttu-id="7508f-152">Komponenta `DataAnnotationsValidator` připojuje podporu ověřování pomocí datových anotací ke kaskádovitě `EditContext`.</span><span class="sxs-lookup"><span data-stu-id="7508f-152">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="7508f-153">Povolení podpory pro ověřování pomocí datových poznámk vyžaduje toto explicitní gesto.</span><span class="sxs-lookup"><span data-stu-id="7508f-153">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="7508f-154">Chcete-li použít jiný ověřovací systém než poznámky dat, nahraďte `DataAnnotationsValidator` vlastní implementaci.</span><span class="sxs-lookup"><span data-stu-id="7508f-154">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="7508f-155">ASP.NET Core implementace je k dispozici pro kontrolu v referenčním zdroji: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="7508f-155">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="7508f-156">provádí dva typy validace:</span><span class="sxs-lookup"><span data-stu-id="7508f-156"> performs two types of validation:</span></span>

* <span data-ttu-id="7508f-157">*Ověření pole* se provádí, když uživatel karty z pole.</span><span class="sxs-lookup"><span data-stu-id="7508f-157">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="7508f-158">Během ověřování pole `DataAnnotationsValidator` komponenta přidruží všechny hlášené výsledky ověření k poli.</span><span class="sxs-lookup"><span data-stu-id="7508f-158">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="7508f-159">*Ověření modelu* se provádí, když uživatel odešle formulář.</span><span class="sxs-lookup"><span data-stu-id="7508f-159">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="7508f-160">Během ověřování modelu `DataAnnotationsValidator` se komponenta pokusí určit pole na základě názvu člena, který hlásí výsledek ověření.</span><span class="sxs-lookup"><span data-stu-id="7508f-160">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="7508f-161">Výsledky ověření, které nejsou přidruženy k jednotlivému členu, jsou přidruženy k modelu, nikoli k poli.</span><span class="sxs-lookup"><span data-stu-id="7508f-161">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="7508f-162">Součásti souhrnu ověření a ověřovací zprávy</span><span class="sxs-lookup"><span data-stu-id="7508f-162">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="7508f-163">Komponenta `ValidationSummary` shrnuje všechny ověřovací zprávy, které jsou podobné [pomocníku pro souhrnnou značku ověření](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="7508f-163">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="7508f-164">Výstupní ověřovací zprávy pro konkrétní `Model` model s parametrem:</span><span class="sxs-lookup"><span data-stu-id="7508f-164">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@_starship" />
```

<span data-ttu-id="7508f-165">Komponenta `ValidationMessage` zobrazuje ověřovací zprávy pro určité pole, které je podobné [pomocné](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)službě Validation Message Tag Er .</span><span class="sxs-lookup"><span data-stu-id="7508f-165">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="7508f-166">Zadejte pole pro `For` ověření s atributem a výraz lambda pojmenování vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="7508f-166">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

<span data-ttu-id="7508f-167">`ValidationMessage` Komponenty `ValidationSummary` a podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="7508f-167">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="7508f-168">Všechny atributy, které neodpovídá parametru komponenty, jsou přidány do generovaného `<div>` prvku nebo `<ul>` prvku.</span><span class="sxs-lookup"><span data-stu-id="7508f-168">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="7508f-169">Vlastní atributy ověření</span><span class="sxs-lookup"><span data-stu-id="7508f-169">Custom validation attributes</span></span>

<span data-ttu-id="7508f-170">Chcete-li zajistit, aby byl výsledek ověření správně přidružen k poli při <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> použití vlastního <xref:System.ComponentModel.DataAnnotations.ValidationResult> [atributu ověření](xref:mvc/models/validation#custom-attributes), předajte při vytváření : kontextu ověření kontextu ověření .</span><span class="sxs-lookup"><span data-stu-id="7508f-170">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="7508f-171">ověřovací balíček anotací dat</span><span class="sxs-lookup"><span data-stu-id="7508f-171"> data annotations validation package</span></span>

<span data-ttu-id="7508f-172">[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) je balíček, který vyplňuje `DataAnnotationsValidator` mezery zkušeností ověření pomocí komponenty.</span><span class="sxs-lookup"><span data-stu-id="7508f-172">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="7508f-173">Balíček je v současné době *experimentální*.</span><span class="sxs-lookup"><span data-stu-id="7508f-173">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="7508f-174">[CompareProperty] atribut</span><span class="sxs-lookup"><span data-stu-id="7508f-174">[CompareProperty] attribute</span></span>

<span data-ttu-id="7508f-175">Nefunguje <xref:System.ComponentModel.DataAnnotations.CompareAttribute> dobře s komponentou, `DataAnnotationsValidator` protože nepřidružuje výsledek ověření s konkrétním členem.</span><span class="sxs-lookup"><span data-stu-id="7508f-175">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="7508f-176">To může mít za následek nekonzistentní chování mezi ověřením na úrovni pole a při ověření celého modelu při odeslání.</span><span class="sxs-lookup"><span data-stu-id="7508f-176">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="7508f-177">*Experimentální* balíček [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) zavádí další `ComparePropertyAttribute`atribut ověření , který funguje kolem těchto omezení.</span><span class="sxs-lookup"><span data-stu-id="7508f-177">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="7508f-178">V Blazor aplikaci `[CompareProperty]` je přímou `[Compare]` náhradou atributu.</span><span class="sxs-lookup"><span data-stu-id="7508f-178">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="7508f-179">Vnořené modely, typy kolekcí a složité typy</span><span class="sxs-lookup"><span data-stu-id="7508f-179">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="7508f-180">poskytuje podporu pro ověřování vstupu formuláře pomocí datových anotací s vestavěným `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="7508f-180"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="7508f-181">`DataAnnotationsValidator` Však pouze ověří nejvyšší úrovně vlastnosti modelu vázané na formulář, které nejsou vlastnosti kolekce nebo komplexní typ.</span><span class="sxs-lookup"><span data-stu-id="7508f-181">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="7508f-182">Chcete-li ověřit celý objektový graf vázaného modelu, včetně vlastností typu kolekce a komplexního typu, použijte `ObjectGraphDataAnnotationsValidator` balíček poskytnutý *experimentálním* balíčkem [Microsoft.AspNetCore.Components.DataAnnotations.Validation:](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)</span><span class="sxs-lookup"><span data-stu-id="7508f-182">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="7508f-183">Oslnění vlastností modelu pomocí aplikace `[ValidateComplexType]`.</span><span class="sxs-lookup"><span data-stu-id="7508f-183">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="7508f-184">V následujících třídách `ShipDescription` modelu třída obsahuje další datové poznámky k ověření, když je model vázán na formulář:</span><span class="sxs-lookup"><span data-stu-id="7508f-184">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="7508f-185">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="7508f-185">*Starship.cs*:</span></span>

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

<span data-ttu-id="7508f-186">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="7508f-186">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="7508f-187">Povolení tlačítka odeslat na základě ověření formuláře</span><span class="sxs-lookup"><span data-stu-id="7508f-187">Enable the submit button based on form validation</span></span>

<span data-ttu-id="7508f-188">Povolení a zakázání tlačítka odeslat na základě ověření formuláře:</span><span class="sxs-lookup"><span data-stu-id="7508f-188">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="7508f-189">Pomocí formuláře `EditContext` přiřaďte model při inicializování komponenty.</span><span class="sxs-lookup"><span data-stu-id="7508f-189">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="7508f-190">Ověřte formulář v `OnFieldChanged` kontextu zpětného volání povolit a zakázat tlačítko odeslat.</span><span class="sxs-lookup"><span data-stu-id="7508f-190">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="7508f-191">Unhook obslužné rutiny události v metodě. `Dispose`</span><span class="sxs-lookup"><span data-stu-id="7508f-191">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="7508f-192">Další informace naleznete v tématu <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="7508f-192">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

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

<span data-ttu-id="7508f-193">V předchozím příkladu `_formInvalid` nastavte `false` if:</span><span class="sxs-lookup"><span data-stu-id="7508f-193">In the preceding example, set `_formInvalid` to `false` if:</span></span>

* <span data-ttu-id="7508f-194">Formulář je předinstalován s platnými výchozími hodnotami.</span><span class="sxs-lookup"><span data-stu-id="7508f-194">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="7508f-195">Chcete po načtení formuláře aktivovat tlačítko odeslat.</span><span class="sxs-lookup"><span data-stu-id="7508f-195">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="7508f-196">Vedlejším účinkem předchozího přístupu `ValidationSummary` je, že součást je naplněna neplatnými poli poté, co uživatel interaguje s jedním polem.</span><span class="sxs-lookup"><span data-stu-id="7508f-196">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="7508f-197">Tento scénář lze řešit jedním z následujících způsobů:</span><span class="sxs-lookup"><span data-stu-id="7508f-197">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="7508f-198">Nepoužívejte `ValidationSummary` součást ve formuláři.</span><span class="sxs-lookup"><span data-stu-id="7508f-198">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="7508f-199">Po `ValidationSummary` výběru tlačítka odeslat zviditelněte komponentu `HandleValidSubmit` (například v metodě).</span><span class="sxs-lookup"><span data-stu-id="7508f-199">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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
