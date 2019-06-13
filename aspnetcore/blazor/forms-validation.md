---
title: Blazor formulářů a ověřování
author: guardrex
description: Naučte se používat v Blazor formuláře a scénáře ověření pole.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
uid: blazor/forms-validation
ms.openlocfilehash: 52f53cbfbb335a4a0d681a378d383924c901ef57
ms.sourcegitcommit: 739a3d7ca4fd2908ea0984940eca589a96359482
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67040695"
---
# <a name="blazor-forms-and-validation"></a><span data-ttu-id="50268-103">Blazor formulářů a ověřování</span><span class="sxs-lookup"><span data-stu-id="50268-103">Blazor forms and validation</span></span>

<span data-ttu-id="50268-104">Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="50268-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="50268-105">Formuláře a ověřování jsou podporovány v Blazor pomocí [anotacemi dat](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="50268-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

> [!NOTE]
> <span data-ttu-id="50268-106">Formuláře a scénáře ověřování se pravděpodobně změní v každé verzi preview.</span><span class="sxs-lookup"><span data-stu-id="50268-106">Forms and validation scenarios are likely to change with each preview release.</span></span>

<span data-ttu-id="50268-107">Následující `ExampleModel` typ definuje logiku ověřování pomocí datových poznámek:</span><span class="sxs-lookup"><span data-stu-id="50268-107">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="50268-108">Formulář je definován pomocí `<EditForm>` komponenty.</span><span class="sxs-lookup"><span data-stu-id="50268-108">A form is defined using the `<EditForm>` component.</span></span> <span data-ttu-id="50268-109">Následující formulář demonstruje obvyklé prvky, komponenty a kód Razor:</span><span class="sxs-lookup"><span data-stu-id="50268-109">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

* <span data-ttu-id="50268-110">Ověří uživatelský vstup ve formuláři `name` pole pomocí ověřování definované v `ExampleModel` typu.</span><span class="sxs-lookup"><span data-stu-id="50268-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="50268-111">Model je vytvořen v součásti `@code` blokovat a uchovávat v soukromé pole (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="50268-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="50268-112">Poli je přiřazena k `Model` atribut `<EditForm>`.</span><span class="sxs-lookup"><span data-stu-id="50268-112">The field is assigned to the `Model` attribute of the `<EditForm>`.</span></span>
* <span data-ttu-id="50268-113">Komponenta validátoru anotací dat (`<DataAnnotationsValidator>`) připojí podporu ověřování pomocí datových poznámek.</span><span class="sxs-lookup"><span data-stu-id="50268-113">The Data Annotations Validator component (`<DataAnnotationsValidator>`) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="50268-114">Souhrn ověření součásti (`<ValidationSummary>`) obsahuje souhrn ověřovacích zpráv.</span><span class="sxs-lookup"><span data-stu-id="50268-114">The Validation Summary component (`<ValidationSummary>`) summarizes validation messages.</span></span>
* <span data-ttu-id="50268-115">`HandleValidSubmit` se aktivuje v případě formulář úspěšného odeslání (předá ověření).</span><span class="sxs-lookup"><span data-stu-id="50268-115">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="50268-116">Sada předdefinovaných vstupní komponent jsou k dispozici pro příjem a ověření vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="50268-116">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="50268-117">Vstupy se ověřují, když se změní, a když se odešle formulář.</span><span class="sxs-lookup"><span data-stu-id="50268-117">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="50268-118">V následující tabulce jsou uvedeny dostupné vstupní součásti.</span><span class="sxs-lookup"><span data-stu-id="50268-118">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="50268-119">Vstupní komponenty</span><span class="sxs-lookup"><span data-stu-id="50268-119">Input component</span></span>   | <span data-ttu-id="50268-120">Se vykresluje jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="50268-120">Rendered as&hellip;</span></span>       |
| ----------------- | ------------------------- |
| `<InputText>`     | `<input>`                 |
| `<InputTextArea>` | `<textarea>`              |
| `<InputSelect>`   | `<select>`                |
| `<InputNumber>`   | `<input type="number">`   |
| `<InputCheckbox>` | `<input type="checkbox">` |
| `<InputDate>`     | `<input type="date">`     |

<span data-ttu-id="50268-121">Všechny vstupní komponenty, včetně `<EditForm>`, podporují libovolnými atributy.</span><span class="sxs-lookup"><span data-stu-id="50268-121">All of the input components, including `<EditForm>`, support arbitrary attributes.</span></span> <span data-ttu-id="50268-122">Všechny atributy, které neodpovídá parametru se přidá do vytvořeného `<form>`, `<input>`, `<select>`, nebo `<textarea>` elementu.</span><span class="sxs-lookup"><span data-stu-id="50268-122">Any attribute that doesn't match a parameter is added to the generated `<form>`, `<input>`, `<select>`, or `<textarea>` element.</span></span>

<span data-ttu-id="50268-123">Vstupní komponenty poskytují výchozí chování pro ověřování na Upravit a změna jejich třídu šablony stylů CSS tak, aby odrážely stavu pole.</span><span class="sxs-lookup"><span data-stu-id="50268-123">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="50268-124">Některé součásti zahrnují užitečné analýzy logiku.</span><span class="sxs-lookup"><span data-stu-id="50268-124">Some components include useful parsing logic.</span></span> <span data-ttu-id="50268-125">Například `<InputDate>` a `<InputNumber>` elegantně zpracovat Neanalyzovatelný hodnoty tak, že je zaregistrujete jako chyby ověření.</span><span class="sxs-lookup"><span data-stu-id="50268-125">For example, `<InputDate>` and `<InputNumber>` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="50268-126">Typy, které může přijmout hodnoty null také podporují možnost použití hodnoty Null z cílového pole (například `int?`).</span><span class="sxs-lookup"><span data-stu-id="50268-126">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="50268-127">Následující `Starship` typ definuje logiku ověřování pomocí větší sadu vlastností a [anotacemi dat](xref:mvc/models/validation) než dříve `ExampleModel`:</span><span class="sxs-lookup"><span data-stu-id="50268-127">The following `Starship` type defines validation logic using a larger set of properties and [data annotations](xref:mvc/models/validation) than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, 
        ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    // Optional (no data annotations)
    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "Form disallowed for unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="50268-128">Následující formulář ověřuje vstup uživatele s použitím ověřování definované v `Starship` modelu:</span><span class="sxs-lookup"><span data-stu-id="50268-128">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```cshtml
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label for="identifier">Identifier: </label>
        <InputText id="identifier" @bind-Value="@starship.Identifier" />
    </p>
    <p>
        <label for="description">Description (optional): </label>
        <InputTextArea Id="description" @bind-Value="@starship.Description" />
    </p>
    <p>
        <label for="classification">Primary Classification: </label>
        <InputSelect id="classification" @bind-Value="@starship.Classification">
            <option value="">Select classification ...</option>
            <option value="Defense">Defense</option>
            <option value="Exploration">Exploration</option>
            <option value="Diplomacy">Diplomacy</option>
        </InputSelect>
    </p>
    <p>
        <label for="accommodation">Maximum Accommodation: </label>
        <InputNumber id="accommodation" 
            @bind-Value="@starship.MaximumAccommodation" />
    </p>
    <p>
        <label for="valid">Engineering Approval: </label>
        <InputCheckbox id="valid" @bind-Value="@starship.IsValidatedDesign" />
    </p>
    <p>
        <label for="productionDate">Production Date: </label>
        <InputDate Id="productionDate" @bind-Value="@starship.ProductionDate" />
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="http://www.paramount.com">Paramount Pictures</a>
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

<span data-ttu-id="50268-129">`<EditForm>` Vytvoří `EditContext` jako [kaskádové hodnotu](xref:blazor/components#cascading-values-and-parameters) metadata o procesu úprav, včetně polí byly změněny a aktuální ověřovacích zpráv, který sleduje.</span><span class="sxs-lookup"><span data-stu-id="50268-129">The `<EditForm>` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including what fields have been modified and the current validation messages.</span></span> <span data-ttu-id="50268-130">`<EditForm>` Také poskytuje pohodlné události pro platné a neplatné odešle (`OnValidSubmit`, `OnInvalidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="50268-130">The `<EditForm>` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="50268-131">Můžete taky použít `OnSubmit` aktivovat ověřování a zkontrolovat hodnoty pole s vlastního ověřovacího kódu.</span><span class="sxs-lookup"><span data-stu-id="50268-131">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="50268-132">Komponenta validátoru anotací dat (`<DataAnnotationsValidator>`) připojí podporu ověřování pomocí datových poznámek k kaskádovým příkazem `EditContext`.</span><span class="sxs-lookup"><span data-stu-id="50268-132">The Data Annotations Validator component (`<DataAnnotationsValidator>`) attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="50268-133">Povolení podpory pro ověření pomocí anotací dat při aktuálně vyžaduje tento explicitní gesta, ale můžeme uvažujete, takže jde o výchozí chování, které můžete přepsat.</span><span class="sxs-lookup"><span data-stu-id="50268-133">Enabling support for validation using data annotations currently requires this explicit gesture, but we're considering making this the default behavior that you can then override.</span></span> <span data-ttu-id="50268-134">Používat ověření jiný systém než anotacemi dat, nahraďte vlastní implementaci validátoru poznámky Data.</span><span class="sxs-lookup"><span data-stu-id="50268-134">To use a different validation system than data annotations, replace the Data Annotations Validator with a custom implementation.</span></span> <span data-ttu-id="50268-135">Implementace ASP.NET Core je k dispozici pro kontrolu v nástroji zdroj odkazu: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="50268-135">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="50268-136">*Implementace ASP.NET Core je v souladu s rychlé aktualizace během období verze preview.*</span><span class="sxs-lookup"><span data-stu-id="50268-136">*The ASP.NET Core implementation is subject to rapid updates during the preview release period.*</span></span>

<span data-ttu-id="50268-137">Souhrn ověření součásti (`<ValidationSummary>`) shrnuje všech ověřovacích zpráv, které se podobá [pomocná rutina značek v souhrnu ověření](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="50268-137">The Validation Summary component (`<ValidationSummary>`) summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="50268-138">Komponenta ověřovací zprávu (`<ValidationMessage>`) zobrazuje zprávy o ověřování pro konkrétní pole, které se podobá [pomocné rutiny značky zpráva ověření](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="50268-138">The Validation Message component (`<ValidationMessage>`) displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="50268-139">Zadejte pole, které pro ověření pomocí `For` atribut a výraz lambda pojmenování vlastnost modelu:</span><span class="sxs-lookup"><span data-stu-id="50268-139">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="50268-140">`<ValidationMessage>` a `<ValidationSummary>` komponenty podporují libovolnými atributy.</span><span class="sxs-lookup"><span data-stu-id="50268-140">The `<ValidationMessage>` and `<ValidationSummary>` components support arbitrary attributes.</span></span> <span data-ttu-id="50268-141">Všechny atributy, které neodpovídá parametru se přidá do vytvořeného `<div>` nebo `<ul>` elementu.</span><span class="sxs-lookup"><span data-stu-id="50268-141">Any attribute that doesn't match a parameter is added to the generated `<div>` or `<ul>` element.</span></span>
