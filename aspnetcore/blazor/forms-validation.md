---
title: ASP.NET Core Blazor formuláře a ověřování
author: guardrex
description: Naučte se používat scénáře ověřování formulářů a polí v Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/forms-validation
ms.openlocfilehash: f4c1845ee4b6ff9274b7117167367ccdd9f36c12
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943690"
---
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a><span data-ttu-id="b69ef-103">ASP.NET Core Blazor formuláře a ověřování</span><span class="sxs-lookup"><span data-stu-id="b69ef-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="b69ef-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b69ef-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b69ef-105">Formuláře a ověřování se v Blazor podporují pomocí [datových poznámek](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="b69ef-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="b69ef-106">Následující `ExampleModel` typ definuje logiku ověřování pomocí datových poznámek:</span><span class="sxs-lookup"><span data-stu-id="b69ef-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="b69ef-107">Formulář je definován pomocí komponenty `EditForm`.</span><span class="sxs-lookup"><span data-stu-id="b69ef-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="b69ef-108">Následující formulář ukazuje typické prvky, komponenty a kód Razor:</span><span class="sxs-lookup"><span data-stu-id="b69ef-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

* <span data-ttu-id="b69ef-109">Formulář ověřuje vstup uživatele v poli `name` pomocí ověřování definovaného v `ExampleModel` typu.</span><span class="sxs-lookup"><span data-stu-id="b69ef-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="b69ef-110">Model se vytvoří v bloku `@code` komponenty a je uložený v soukromém poli (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="b69ef-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="b69ef-111">Pole je přiřazeno atributu `Model` elementu `<EditForm>`.</span><span class="sxs-lookup"><span data-stu-id="b69ef-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="b69ef-112">Komponenta `DataAnnotationsValidator` připojí podporu ověřování pomocí datových poznámek.</span><span class="sxs-lookup"><span data-stu-id="b69ef-112">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="b69ef-113">Komponenta `ValidationSummary` shrnuje ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="b69ef-113">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="b69ef-114">`HandleValidSubmit` se spustí, když formulář úspěšně odešle (projde ověřením).</span><span class="sxs-lookup"><span data-stu-id="b69ef-114">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="b69ef-115">K dispozici je sada předdefinovaných vstupních komponent pro příjem a ověření vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="b69ef-115">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="b69ef-116">Vstupy jsou ověřovány při jejich změně a při odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="b69ef-116">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="b69ef-117">Dostupné vstupní komponenty jsou uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="b69ef-117">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="b69ef-118">Vstupní komponenta</span><span class="sxs-lookup"><span data-stu-id="b69ef-118">Input component</span></span> | <span data-ttu-id="b69ef-119">Vykresleno jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="b69ef-119">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="b69ef-120">Všechny vstupní komponenty, včetně `EditForm`, podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="b69ef-120">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="b69ef-121">Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do vykresleného prvku HTML.</span><span class="sxs-lookup"><span data-stu-id="b69ef-121">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="b69ef-122">Vstupní komponenty poskytují výchozí chování pro ověřování při úpravách a změně jejich třídy CSS tak, aby odrážely stav pole.</span><span class="sxs-lookup"><span data-stu-id="b69ef-122">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="b69ef-123">Některé součásti obsahují užitečnou logiku analýzy.</span><span class="sxs-lookup"><span data-stu-id="b69ef-123">Some components include useful parsing logic.</span></span> <span data-ttu-id="b69ef-124">Například `InputDate` a `InputNumber` zpracovávat neanalyzovatelné hodnoty tak, že je zaregistrujete jako chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="b69ef-124">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="b69ef-125">Typy, které mohou přijmout hodnoty null, podporují také hodnotu null cílového pole (například `int?`).</span><span class="sxs-lookup"><span data-stu-id="b69ef-125">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="b69ef-126">Následující `Starship` typ definuje logiku ověřování pomocí větší sady vlastností a poznámek k datům než předchozí `ExampleModel`:</span><span class="sxs-lookup"><span data-stu-id="b69ef-126">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="b69ef-127">V předchozím příkladu je `Description` nepovinný, protože nejsou k dispozici žádné anotace dat.</span><span class="sxs-lookup"><span data-stu-id="b69ef-127">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="b69ef-128">Následující formulář ověřuje vstup uživatele pomocí ověřování definovaného v modelu `Starship`:</span><span class="sxs-lookup"><span data-stu-id="b69ef-128">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
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

<span data-ttu-id="b69ef-129">`EditForm` vytvoří `EditContext` jako [kaskádovou hodnotu](xref:blazor/components#cascading-values-and-parameters) , která sleduje metadata procesu úprav, včetně polí, která byla změněna, a aktuálních zpráv ověření.</span><span class="sxs-lookup"><span data-stu-id="b69ef-129">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="b69ef-130">`EditForm` taky poskytuje praktické události pro platná a neplatná odeslání (`OnValidSubmit`, `OnInvalidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="b69ef-130">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="b69ef-131">Alternativně můžete použít `OnSubmit` k aktivaci ověřování a hodnot polí pro kontrolu pomocí vlastního ověřovacího kódu.</span><span class="sxs-lookup"><span data-stu-id="b69ef-131">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="b69ef-132">InputText na základě události vstupu</span><span class="sxs-lookup"><span data-stu-id="b69ef-132">InputText based on the input event</span></span>

<span data-ttu-id="b69ef-133">Komponentu `InputText` použijte k vytvoření vlastní komponenty, která používá událost `input` namísto události `change`.</span><span class="sxs-lookup"><span data-stu-id="b69ef-133">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="b69ef-134">Vytvořte komponentu s následujícím kódem a použijte komponentu stejně jako `InputText` se používá:</span><span class="sxs-lookup"><span data-stu-id="b69ef-134">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a><span data-ttu-id="b69ef-135">Podpora ověřování</span><span class="sxs-lookup"><span data-stu-id="b69ef-135">Validation support</span></span>

<span data-ttu-id="b69ef-136">Součást `DataAnnotationsValidator` připojuje ověřování pomocí datových poznámek k kaskádovým `EditContext`ům.</span><span class="sxs-lookup"><span data-stu-id="b69ef-136">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="b69ef-137">Povolení podpory pro ověřování pomocí datových poznámek vyžaduje toto explicitní gesto.</span><span class="sxs-lookup"><span data-stu-id="b69ef-137">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="b69ef-138">Chcete-li použít jiný systém ověřování než datové poznámky, nahraďte `DataAnnotationsValidator` vlastní implementací.</span><span class="sxs-lookup"><span data-stu-id="b69ef-138">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="b69ef-139">ASP.NET Core implementace je k dispozici pro kontrolu v referenčním zdroji: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="b69ef-139">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="b69ef-140"> provádí dva typy ověřování:</span><span class="sxs-lookup"><span data-stu-id="b69ef-140"> performs two types of validation:</span></span>

* <span data-ttu-id="b69ef-141">*Ověřování polí* se provede, když se karty uživatele nacházejí v poli.</span><span class="sxs-lookup"><span data-stu-id="b69ef-141">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="b69ef-142">Při ověřování pole přidruží komponenta `DataAnnotationsValidator` všechny hlášené výsledky ověření k poli.</span><span class="sxs-lookup"><span data-stu-id="b69ef-142">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="b69ef-143">*Ověřování modelu* se provede, když uživatel formulář odešle.</span><span class="sxs-lookup"><span data-stu-id="b69ef-143">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="b69ef-144">Při ověřování modelu se `DataAnnotationsValidator` komponenta pokusí určit pole na základě názvu člena, který sestavy výsledků ověření.</span><span class="sxs-lookup"><span data-stu-id="b69ef-144">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="b69ef-145">Výsledky ověření, které nejsou přidruženy k jednotlivým členům, jsou přidruženy k modelu, nikoli poli.</span><span class="sxs-lookup"><span data-stu-id="b69ef-145">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="b69ef-146">Komponenty zprávy pro Shrnutí a ověření</span><span class="sxs-lookup"><span data-stu-id="b69ef-146">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="b69ef-147">Komponenta `ValidationSummary` shrnuje všechny zprávy o ověřování, které se podobají [pomocníka značek Shrnutí ověřování](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="b69ef-147">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="b69ef-148">Zprávy ověřování výstupu pro určitý model s parametrem `Model`:</span><span class="sxs-lookup"><span data-stu-id="b69ef-148">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="b69ef-149">Komponenta `ValidationMessage` zobrazí ověřovací zprávy pro konkrétní pole, které je podobné [Pomocníkovi značky ověřovací zprávy](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="b69ef-149">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="b69ef-150">Zadejte pole pro ověřování pomocí atributu `For` a lambda výrazu pojmenování vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="b69ef-150">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="b69ef-151">Komponenty `ValidationMessage` a `ValidationSummary` podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="b69ef-151">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="b69ef-152">Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do generovaného `<div>` nebo `<ul>` elementu.</span><span class="sxs-lookup"><span data-stu-id="b69ef-152">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="b69ef-153">Vlastní ověřovací atributy</span><span class="sxs-lookup"><span data-stu-id="b69ef-153">Custom validation attributes</span></span>

<span data-ttu-id="b69ef-154">Chcete-li zajistit, aby byl výsledek ověření správně přidružen k poli při použití [vlastního ověřovacího atributu](xref:mvc/models/validation#custom-attributes), předejte <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> ověřovacího kontextu při vytváření <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span><span class="sxs-lookup"><span data-stu-id="b69ef-154">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

::: moniker range=">= aspnetcore-3.1"

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="b69ef-155"> balíček pro ověření datových poznámek</span><span class="sxs-lookup"><span data-stu-id="b69ef-155"> data annotations validation package</span></span>

<span data-ttu-id="b69ef-156">[Microsoft. AspNetCore.Blazor. Dataanotace. ověřování](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) je balíček, který vyplní mezery v prostředí ověřování pomocí komponenty `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="b69ef-156">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="b69ef-157">Balíček je momentálně *experimentální*.</span><span class="sxs-lookup"><span data-stu-id="b69ef-157">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="b69ef-158">[CompareProperty] – atribut</span><span class="sxs-lookup"><span data-stu-id="b69ef-158">[CompareProperty] attribute</span></span>

<span data-ttu-id="b69ef-159"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> nefunguje dobře s komponentou `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="b69ef-159">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="b69ef-160">[Microsoft. AspNetCore.Blazor. DataAnnotations.](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *experimentální* balíček pro ověření zavádí další atribut ověření `ComparePropertyAttribute`, který tato omezení působí.</span><span class="sxs-lookup"><span data-stu-id="b69ef-160">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="b69ef-161">V Blazor aplikaci `[CompareProperty]` je přímá náhrada pro atribut `[Compare]`.</span><span class="sxs-lookup"><span data-stu-id="b69ef-161">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span> <span data-ttu-id="b69ef-162">Další informace najdete v tématu [CompareAttribute se ignoruje s OnValidSubmit EditForm (ASPNET/AspNetCore #10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).</span><span class="sxs-lookup"><span data-stu-id="b69ef-162">For more information, see [CompareAttribute ignored with OnValidSubmit EditForm (aspnet/AspNetCore #10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="b69ef-163">Vnořené modely, typy kolekcí a komplexní typy</span><span class="sxs-lookup"><span data-stu-id="b69ef-163">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="b69ef-164"> poskytuje podporu pro ověřování vstupu formuláře pomocí datových poznámek s integrovaným `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="b69ef-164"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="b69ef-165">`DataAnnotationsValidator` však pouze ověřuje vlastnosti nejvyšší úrovně modelu vázaného na formulář, který není typu kolekce nebo komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="b69ef-165">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="b69ef-166">Chcete-li ověřit celý graf objektu vázaného modelu, včetně vlastností kolekce a komplexního typu, použijte `ObjectGraphDataAnnotationsValidator` od *experimentálního* [Microsoft. AspNetCore.Blazor. Dataanotace. ověřovací](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) balíček:</span><span class="sxs-lookup"><span data-stu-id="b69ef-166">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="b69ef-167">Přidávejte do vlastností modelu poznámky pomocí `[ValidateComplexType]`.</span><span class="sxs-lookup"><span data-stu-id="b69ef-167">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="b69ef-168">V následujících třídách modelu obsahuje Třída `ShipDescription` další datové poznámky, které lze ověřit, je-li model svázán s formulářem:</span><span class="sxs-lookup"><span data-stu-id="b69ef-168">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="b69ef-169">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="b69ef-169">*Starship.cs*:</span></span>

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

<span data-ttu-id="b69ef-170">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="b69ef-170">*ShipDescription.cs*:</span></span>

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

::: moniker-end

::: moniker range="< aspnetcore-3.1"

### <a name="validation-of-complex-or-collection-type-properties"></a><span data-ttu-id="b69ef-171">Ověřování vlastností komplexního nebo typu kolekce</span><span class="sxs-lookup"><span data-stu-id="b69ef-171">Validation of complex or collection type properties</span></span>

<span data-ttu-id="b69ef-172">Atributy ověřování použité pro vlastnosti modelu jsou ověřovány při odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="b69ef-172">Validation attributes applied to the properties of a model validate when the form is submitted.</span></span> <span data-ttu-id="b69ef-173">Nicméně vlastnosti kolekcí nebo komplexních datových typů modelu nejsou ověřeny při odesílání formuláře komponentou `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="b69ef-173">However, the properties of collections or complex data types of a model aren't validated on form submission by the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="b69ef-174">Chcete-li v tomto scénáři akceptovat vnořené atributy ověřování, použijte vlastní ověřovací komponentu.</span><span class="sxs-lookup"><span data-stu-id="b69ef-174">To honor the nested validation attributes in this scenario, use a custom validation component.</span></span> <span data-ttu-id="b69ef-175">Příklad naleznete v části [Ukázka ověřeníBlazor (ASPNET/Samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span><span class="sxs-lookup"><span data-stu-id="b69ef-175">For an example, see the [Blazor Validation sample (aspnet/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>

::: moniker-end
