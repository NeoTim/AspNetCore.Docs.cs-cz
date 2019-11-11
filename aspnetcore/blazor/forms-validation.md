---
title: ASP.NET Core formuláře a ověřování Blazor
author: guardrex
description: Naučte se používat scénáře ověřování formulářů a polí v Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/04/2019
uid: blazor/forms-validation
ms.openlocfilehash: 6dcc36c5133367493b476655dbdf73b75db9d168
ms.sourcegitcommit: a7bbe3890befead19440075b05b9674351f98872
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905737"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="23718-103">ASP.NET Core formuláře a ověřování Blazor</span><span class="sxs-lookup"><span data-stu-id="23718-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="23718-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="23718-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="23718-105">Formuláře a ověřování se v Blazor podporují pomocí [datových poznámek](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="23718-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="23718-106">Následující `ExampleModel` typ definuje logiku ověřování pomocí datových poznámek:</span><span class="sxs-lookup"><span data-stu-id="23718-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="23718-107">Formulář je definován pomocí komponenty `EditForm`.</span><span class="sxs-lookup"><span data-stu-id="23718-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="23718-108">Následující formulář ukazuje typické prvky, komponenty a kód Razor:</span><span class="sxs-lookup"><span data-stu-id="23718-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
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

* <span data-ttu-id="23718-109">Formulář ověřuje vstup uživatele v poli `name` pomocí ověřování definovaného v `ExampleModel` typu.</span><span class="sxs-lookup"><span data-stu-id="23718-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="23718-110">Model se vytvoří v bloku `@code` komponenty a je uložený v soukromém poli (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="23718-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="23718-111">Pole je přiřazeno atributu `Model` elementu `<EditForm>`.</span><span class="sxs-lookup"><span data-stu-id="23718-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="23718-112">Komponenta `DataAnnotationsValidator` připojí podporu ověřování pomocí datových poznámek.</span><span class="sxs-lookup"><span data-stu-id="23718-112">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="23718-113">Komponenta `ValidationSummary` shrnuje ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="23718-113">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="23718-114">`HandleValidSubmit` se spustí, když formulář úspěšně odešle (projde ověřením).</span><span class="sxs-lookup"><span data-stu-id="23718-114">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="23718-115">K dispozici je sada předdefinovaných vstupních komponent pro příjem a ověření vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="23718-115">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="23718-116">Vstupy jsou ověřovány při jejich změně a při odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="23718-116">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="23718-117">Dostupné vstupní komponenty jsou uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="23718-117">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="23718-118">Vstupní komponenta</span><span class="sxs-lookup"><span data-stu-id="23718-118">Input component</span></span> | <span data-ttu-id="23718-119">Vykresleno jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="23718-119">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="23718-120">Všechny vstupní komponenty, včetně `EditForm`, podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="23718-120">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="23718-121">Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do vykresleného prvku HTML.</span><span class="sxs-lookup"><span data-stu-id="23718-121">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="23718-122">Vstupní komponenty poskytují výchozí chování pro ověřování při úpravách a změně jejich třídy CSS tak, aby odrážely stav pole.</span><span class="sxs-lookup"><span data-stu-id="23718-122">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="23718-123">Některé součásti obsahují užitečnou logiku analýzy.</span><span class="sxs-lookup"><span data-stu-id="23718-123">Some components include useful parsing logic.</span></span> <span data-ttu-id="23718-124">Například `InputDate` a `InputNumber` zpracovávat neanalyzovatelné hodnoty tak, že je zaregistrujete jako chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="23718-124">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="23718-125">Typy, které mohou přijmout hodnoty null, podporují také hodnotu null cílového pole (například `int?`).</span><span class="sxs-lookup"><span data-stu-id="23718-125">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="23718-126">Následující `Starship` typ definuje logiku ověřování pomocí větší sady vlastností a poznámek k datům než předchozí `ExampleModel`:</span><span class="sxs-lookup"><span data-stu-id="23718-126">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="23718-127">V předchozím příkladu je `Description` nepovinný, protože nejsou k dispozici žádné anotace dat.</span><span class="sxs-lookup"><span data-stu-id="23718-127">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="23718-128">Následující formulář ověřuje vstup uživatele pomocí ověřování definovaného v modelu `Starship`:</span><span class="sxs-lookup"><span data-stu-id="23718-128">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="23718-129">`EditForm` vytvoří `EditContext` jako [kaskádovou hodnotu](xref:blazor/components#cascading-values-and-parameters) , která sleduje metadata procesu úprav, včetně polí, která byla změněna, a aktuálních zpráv ověření.</span><span class="sxs-lookup"><span data-stu-id="23718-129">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="23718-130">`EditForm` taky poskytuje praktické události pro platná a neplatná odeslání (`OnValidSubmit`, `OnInvalidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="23718-130">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="23718-131">Alternativně můžete použít `OnSubmit` k aktivaci ověřování a hodnot polí pro kontrolu pomocí vlastního ověřovacího kódu.</span><span class="sxs-lookup"><span data-stu-id="23718-131">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="23718-132">InputText na základě události vstupu</span><span class="sxs-lookup"><span data-stu-id="23718-132">InputText based on the input event</span></span>

<span data-ttu-id="23718-133">Komponentu `InputText` použijte k vytvoření vlastní komponenty, která používá událost `input` namísto události `change`.</span><span class="sxs-lookup"><span data-stu-id="23718-133">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="23718-134">Vytvořte komponentu s následujícím kódem a použijte komponentu stejně jako `InputText` se používá:</span><span class="sxs-lookup"><span data-stu-id="23718-134">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```cshtml
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a><span data-ttu-id="23718-135">Podpora ověřování</span><span class="sxs-lookup"><span data-stu-id="23718-135">Validation support</span></span>

<span data-ttu-id="23718-136">Součást `DataAnnotationsValidator` připojuje ověřování pomocí datových poznámek k kaskádovým `EditContext`ům.</span><span class="sxs-lookup"><span data-stu-id="23718-136">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="23718-137">Povolení podpory pro ověřování pomocí datových poznámek vyžaduje toto explicitní gesto.</span><span class="sxs-lookup"><span data-stu-id="23718-137">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="23718-138">Chcete-li použít jiný systém ověřování než datové poznámky, nahraďte `DataAnnotationsValidator` vlastní implementací.</span><span class="sxs-lookup"><span data-stu-id="23718-138">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="23718-139">ASP.NET Core implementace je k dispozici pro kontrolu v referenčním zdroji: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="23718-139">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

<span data-ttu-id="23718-140">Komponenta `ValidationSummary` shrnuje všechny zprávy o ověřování, které se podobají [pomocníka značek pro Shrnutí ověření](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="23718-140">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="23718-141">Komponenta `ValidationMessage` zobrazí ověřovací zprávy pro konkrétní pole, které je podobné [Pomocníkovi značky ověřovací zprávy](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="23718-141">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="23718-142">Zadejte pole pro ověřování pomocí atributu `For` a lambda výrazu pojmenování vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="23718-142">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="23718-143">Komponenty `ValidationMessage` a `ValidationSummary` podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="23718-143">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="23718-144">Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do generovaného `<div>` nebo `<ul>` elementu.</span><span class="sxs-lookup"><span data-stu-id="23718-144">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="23718-145">**Balíček Microsoft. AspNetCore. Blazor. DataAnnotations. Validation**</span><span class="sxs-lookup"><span data-stu-id="23718-145">**Microsoft.AspNetCore.Blazor.DataAnnotations.Validation package**</span></span>

<span data-ttu-id="23718-146">[Microsoft. AspNetCore. Blazor. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) je balíček, který vyplní mezery v prostředí ověřování pomocí komponenty `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="23718-146">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="23718-147">Balíček je momentálně *experimentální*a my plánujeme přidat tyto scénáře do ASP.NET Core Framework v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="23718-147">The package is currently *experimental*, and we plan to add these scenarios into the ASP.NET Core framework in a future release.</span></span>

<span data-ttu-id="23718-148">Komponenta `DataAnnotationsValidator` neověřuje podvlastnostem komplexních vlastností v modelu ověřování.</span><span class="sxs-lookup"><span data-stu-id="23718-148">The `DataAnnotationsValidator` component doesn't validate subproperties of complex properties on a validating model.</span></span> <span data-ttu-id="23718-149">Položky vlastností typu kolekce nejsou ověřeny.</span><span class="sxs-lookup"><span data-stu-id="23718-149">Items of collection-type properties aren't validated.</span></span> <span data-ttu-id="23718-150">Chcete-li ověřit tyto typy, balíček `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` zavádí atribut `ValidateComplexType` ověření, který pracuje společně s komponentou `ObjectGraphDataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="23718-150">To validate these types, the `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` package introduces the `ValidateComplexType` validation attribute that works in tandem with the `ObjectGraphDataAnnotationsValidator` component.</span></span> <span data-ttu-id="23718-151">Příklad těchto typů, které se používají, naleznete v [ukázce ověření Blazor v úložišti GitHub/Samples ](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span><span class="sxs-lookup"><span data-stu-id="23718-151">For an example of these types in use, see the [Blazor Validation sample in the aspnet/samples GitHub repository ](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>

<span data-ttu-id="23718-152"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> nefunguje dobře s komponentou `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="23718-152">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="23718-153">Balíček `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` zavádí další ověřovací atribut `ComparePropertyAttribute`, který tato omezení funguje.</span><span class="sxs-lookup"><span data-stu-id="23718-153">The `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="23718-154">V aplikaci Blazor je `ComparePropertyAttribute` přímou náhradou `CompareAttribute`.</span><span class="sxs-lookup"><span data-stu-id="23718-154">In a Blazor app, `ComparePropertyAttribute` is a direct replacement for the `CompareAttribute`.</span></span> <span data-ttu-id="23718-155">Další informace najdete v tématu [CompareAttribute se ignoruje s OnValidSubmit EditForm (ASPNET/AspNetCore \#10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).</span><span class="sxs-lookup"><span data-stu-id="23718-155">For more information, see [CompareAttribute ignored with OnValidSubmit EditForm (aspnet/AspNetCore \#10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.1"

### <a name="validation-of-complex-or-collection-type-properties"></a><span data-ttu-id="23718-156">Ověřování vlastností komplexního nebo typu kolekce</span><span class="sxs-lookup"><span data-stu-id="23718-156">Validation of complex or collection type properties</span></span>

<span data-ttu-id="23718-157">Atributy ověřování použité pro vlastnosti modelu jsou ověřovány při odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="23718-157">Validation attributes applied to the properties of a model validate when the form is submitted.</span></span> <span data-ttu-id="23718-158">Nicméně vlastnosti kolekcí nebo komplexních datových typů modelu nejsou ověřeny při odesílání formuláře komponentou `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="23718-158">However, the properties of collections or complex data types of a model aren't validated on form submission by the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="23718-159">Chcete-li v tomto scénáři akceptovat vnořené atributy ověřování, použijte vlastní ověřovací komponentu.</span><span class="sxs-lookup"><span data-stu-id="23718-159">To honor the nested validation attributes in this scenario, use a custom validation component.</span></span> <span data-ttu-id="23718-160">Příklad naleznete v tématu [Ukázka ověření Blazor v úložišti GitHub/Samples](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span><span class="sxs-lookup"><span data-stu-id="23718-160">For an example, see the [Blazor Validation sample in the aspnet/samples GitHub repository](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>

::: moniker-end
