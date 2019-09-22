---
title: ASP.NET Core formuláře a ověřování Blazor
author: guardrex
description: Naučte se používat scénáře ověřování formulářů a polí v Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/15/2019
uid: blazor/forms-validation
ms.openlocfilehash: c68ebf7f7bf07b6c243ab16307716cea13870446
ms.sourcegitcommit: 04ce94b3c1b01d167f30eed60c1c95446dfe759d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/21/2019
ms.locfileid: "71176341"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="4ac4f-103">ASP.NET Core formuláře a ověřování Blazor</span><span class="sxs-lookup"><span data-stu-id="4ac4f-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="4ac4f-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4ac4f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4ac4f-105">Formuláře a ověřování se v Blazor podporují pomocí [datových poznámek](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="4ac4f-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="4ac4f-106">Následující `ExampleModel` typ definuje logiku ověřování pomocí datových poznámek:</span><span class="sxs-lookup"><span data-stu-id="4ac4f-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="4ac4f-107">Formulář je definován pomocí `EditForm` komponenty.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="4ac4f-108">Následující formulář ukazuje typické prvky, komponenty a kód Razor:</span><span class="sxs-lookup"><span data-stu-id="4ac4f-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="@exampleModel.Name" />

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

* <span data-ttu-id="4ac4f-109">Formulář ověřuje vstup uživatele v `name` poli pomocí ověřování definovaného `ExampleModel` v typu.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="4ac4f-110">Model se vytvoří v `@code` bloku komponenty a je uložený v soukromém poli (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="4ac4f-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="4ac4f-111">Pole je přiřazeno `Model` atributu `<EditForm>` elementu.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="4ac4f-112">`DataAnnotationsValidator` Komponenta připojuje ověřování pomocí datových poznámek.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-112">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="4ac4f-113">`ValidationSummary` Komponenta shrnuje ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-113">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="4ac4f-114">`HandleValidSubmit`aktivuje se, když formulář úspěšně odešle (projde ověřením).</span><span class="sxs-lookup"><span data-stu-id="4ac4f-114">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="4ac4f-115">K dispozici je sada předdefinovaných vstupních komponent pro příjem a ověření vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-115">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="4ac4f-116">Vstupy jsou ověřovány při jejich změně a při odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-116">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="4ac4f-117">Dostupné vstupní komponenty jsou uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-117">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="4ac4f-118">Vstupní komponenta</span><span class="sxs-lookup"><span data-stu-id="4ac4f-118">Input component</span></span> | <span data-ttu-id="4ac4f-119">Vykresleno jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="4ac4f-119">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="4ac4f-120">Všechny vstupní komponenty, včetně `EditForm`, podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-120">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="4ac4f-121">Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do vykresleného prvku HTML.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-121">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="4ac4f-122">Vstupní komponenty poskytují výchozí chování pro ověřování při úpravách a změně jejich třídy CSS tak, aby odrážely stav pole.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-122">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="4ac4f-123">Některé součásti obsahují užitečnou logiku analýzy.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-123">Some components include useful parsing logic.</span></span> <span data-ttu-id="4ac4f-124">Například a `InputDate` `InputNumber` zpracujte neanalyzovatelné hodnoty tak, že je zaregistrujete jako chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-124">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="4ac4f-125">Typy, které mohou přijmout hodnoty null, podporují také hodnotu null cílového pole (například `int?`).</span><span class="sxs-lookup"><span data-stu-id="4ac4f-125">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="4ac4f-126">Následující `Starship` typ definuje logiku ověřování pomocí větší sady vlastností a poznámek k datům než dříve `ExampleModel`:</span><span class="sxs-lookup"><span data-stu-id="4ac4f-126">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="4ac4f-127">V předchozím příkladu je volitelná, `Description` protože nejsou k dispozici žádné anotace dat.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-127">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="4ac4f-128">Následující formulář ověří uživatelský vstup pomocí ověřování definovaného v `Starship` modelu:</span><span class="sxs-lookup"><span data-stu-id="4ac4f-128">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```cshtml
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label for="identifier">Identifier: </label>
        <InputText id="identifier" @bind-Value="starship.Identifier" />
    </p>
    <p>
        <label for="description">Description (optional): </label>
        <InputTextArea id="description" @bind-Value="starship.Description" />
    </p>
    <p>
        <label for="classification">Primary Classification: </label>
        <InputSelect id="classification" @bind-Value="starship.Classification">
            <option value="">Select classification ...</option>
            <option value="Exploration">Exploration</option>
            <option value="Diplomacy">Diplomacy</option>
            <option value="Defense">Defense</option>
        </InputSelect>
    </p>
    <p>
        <label for="accommodation">Maximum Accommodation: </label>
        <InputNumber id="accommodation" 
            @bind-Value="starship.MaximumAccommodation" />
    </p>
    <p>
        <label for="valid">Engineering Approval: </label>
        <InputCheckbox id="valid" @bind-Value="starship.IsValidatedDesign" />
    </p>
    <p>
        <label for="productionDate">Production Date: </label>
        <InputDate id="productionDate" @bind-Value="starship.ProductionDate" />
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

<span data-ttu-id="4ac4f-129">Vytvoří jako kaskádovou hodnotu, která sleduje metadata procesu úprav, včetně toho, která pole jsou upravena a aktuální zprávy ověření. [](xref:blazor/components#cascading-values-and-parameters) `EditForm` `EditContext`</span><span class="sxs-lookup"><span data-stu-id="4ac4f-129">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="4ac4f-130">Poskytuje taky praktické události pro platná a neplatná odeslání (`OnValidSubmit`, `OnInvalidSubmit`). `EditForm`</span><span class="sxs-lookup"><span data-stu-id="4ac4f-130">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="4ac4f-131">Alternativně můžete `OnSubmit` použít k aktivaci ověřování a hodnot polí pro kontrolu pomocí vlastního ověřovacího kódu.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-131">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="4ac4f-132">InputText na základě události vstupu</span><span class="sxs-lookup"><span data-stu-id="4ac4f-132">InputText based on the input event</span></span>

<span data-ttu-id="4ac4f-133">Pomocí komponenty můžete vytvořit vlastní komponentu, která místo `change` události `input` používá událost. `InputText`</span><span class="sxs-lookup"><span data-stu-id="4ac4f-133">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="4ac4f-134">Vytvořte komponentu s následujícím kódem a použijte komponentu stejně jako `InputText` je použit:</span><span class="sxs-lookup"><span data-stu-id="4ac4f-134">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```cshtml
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a><span data-ttu-id="4ac4f-135">Podpora ověřování</span><span class="sxs-lookup"><span data-stu-id="4ac4f-135">Validation support</span></span>

<span data-ttu-id="4ac4f-136">Komponenta připojuje ověřování pomocí datových poznámek do `EditContext`kaskády. `DataAnnotationsValidator`</span><span class="sxs-lookup"><span data-stu-id="4ac4f-136">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="4ac4f-137">Povolení podpory pro ověřování pomocí datových poznámek vyžaduje toto explicitní gesto.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-137">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="4ac4f-138">Chcete-li použít jiný systém ověřování než datové poznámky, nahraďte `DataAnnotationsValidator` vlastní implementaci.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-138">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="4ac4f-139">ASP.NET Core implementace je k dispozici pro kontrolu v referenčním zdroji: [](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)DataAnnotationsValidator/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="4ac4f-139">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

<span data-ttu-id="4ac4f-140">Komponenta shrnuje všechny zprávy o ověřování, které jsou podobné [pomocníka značek pro ověřování](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper). `ValidationSummary`</span><span class="sxs-lookup"><span data-stu-id="4ac4f-140">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="4ac4f-141">Komponenta zobrazí ověřovací zprávy pro konkrétní pole, které se podobá [pomocníka značek ověřovací zprávy.](xref:mvc/views/working-with-forms#the-validation-message-tag-helper) `ValidationMessage`</span><span class="sxs-lookup"><span data-stu-id="4ac4f-141">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="4ac4f-142">Zadejte pole pro ověření s `For` atributem a výrazem lambda pojmenování vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="4ac4f-142">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="4ac4f-143">Komponenty `ValidationMessage` a`ValidationSummary` podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-143">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="4ac4f-144">Všechny atributy, které se neshodují s parametrem komponenty, `<div>` jsou `<ul>` přidány do generovaného prvku nebo.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-144">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="validation-of-complex-or-collection-type-properties"></a><span data-ttu-id="4ac4f-145">Ověřování vlastností komplexního nebo typu kolekce</span><span class="sxs-lookup"><span data-stu-id="4ac4f-145">Validation of complex or collection type properties</span></span>

<span data-ttu-id="4ac4f-146">Atributy ověřování použité pro vlastnosti modelu jsou ověřovány při odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-146">Validation attributes applied to the properties of a model validate when the form is submitted.</span></span> <span data-ttu-id="4ac4f-147">Nicméně vlastnosti kolekcí nebo komplexních datových typů modelu nejsou ověřeny při odesílání formuláře.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-147">However, the properties of collections or complex data types of a model aren't validated on form submission.</span></span> <span data-ttu-id="4ac4f-148">Chcete-li v tomto scénáři akceptovat vnořené atributy ověřování, použijte vlastní ověřovací komponentu.</span><span class="sxs-lookup"><span data-stu-id="4ac4f-148">To honor the nested validation attributes in this scenario, use a custom validation component.</span></span> <span data-ttu-id="4ac4f-149">Příklad naleznete v tématu [Ukázka ověření Blazor v úložišti GitHub/Samples](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span><span class="sxs-lookup"><span data-stu-id="4ac4f-149">For an example, see the [Blazor Validation sample in the aspnet/samples GitHub repository](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>
