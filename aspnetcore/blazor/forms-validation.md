---
title: ASP.NET Core Blazor formuláře a ověřování
author: guardrex
description: Naučte se používat scénáře ověřování formulářů a polí v Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 88c3ded79db65557d9426fde6f43aace4d9d8ae2
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606671"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a><span data-ttu-id="2b8b6-103">ASP.NET Core Blazor formuláře a ověřování</span><span class="sxs-lookup"><span data-stu-id="2b8b6-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="2b8b6-104">Od [Daniel Skořepa](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2b8b6-104">By [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2b8b6-105">Formuláře a ověřování jsou podporovány v Blazor použití [datových poznámek](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="2b8b6-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="2b8b6-106">Následující `ExampleModel` typ definuje logiku ověřování pomocí datových poznámek:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="2b8b6-107">Formulář je definován pomocí <xref:Microsoft.AspNetCore.Components.Forms.EditForm> komponenty.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="2b8b6-108">Následující formulář ukazuje typické prvky, komponenty a Razor kód:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
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
        ...
    }
}
```

<span data-ttu-id="2b8b6-109">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-109">In the preceding example:</span></span>

* <span data-ttu-id="2b8b6-110">Formulář ověřuje vstup uživatele v `name` poli pomocí ověřování definovaného v `ExampleModel` typu.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="2b8b6-111">Model se vytvoří v bloku komponenty a je `@code` uložený v soukromém poli ( `exampleModel` ).</span><span class="sxs-lookup"><span data-stu-id="2b8b6-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="2b8b6-112">Pole je přiřazeno `Model` atributu `<EditForm>` elementu.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="2b8b6-113"><xref:Microsoft.AspNetCore.Components.Forms.InputText> `@bind-Value` Vazby komponenty:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="2b8b6-114">Vlastnost modelu ( `exampleModel.Name` ) na <xref:Microsoft.AspNetCore.Components.Forms.InputText> `Value` vlastnost součásti.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="2b8b6-115">Další informace o vazbách vlastností naleznete v tématu <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters> .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-115">For more information on property binding, see <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="2b8b6-116">Delegát události změny pro <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` vlastnost komponenty.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="2b8b6-117"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [Komponenta validátoru](#validator-components) připojí podporu ověřování pomocí datových poznámek.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [validator component](#validator-components) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="2b8b6-118"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Komponenta shrnuje ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="2b8b6-119">`HandleValidSubmit` aktivuje se, když formulář úspěšně odešle (projde ověřením).</span><span class="sxs-lookup"><span data-stu-id="2b8b6-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="2b8b6-120">Předdefinované součásti formulářů</span><span class="sxs-lookup"><span data-stu-id="2b8b6-120">Built-in forms components</span></span>

<span data-ttu-id="2b8b6-121">K dispozici je sada předdefinovaných komponent pro příjem a ověření vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-121">A set of built-in components are available to receive and validate user input.</span></span> <span data-ttu-id="2b8b6-122">Vstupy jsou ověřovány při jejich změně a při odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-122">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="2b8b6-123">Dostupné vstupní komponenty jsou uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-123">Available input components are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="2b8b6-124">Vstupní komponenta</span><span class="sxs-lookup"><span data-stu-id="2b8b6-124">Input component</span></span> | <span data-ttu-id="2b8b6-125">Vykresleno jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="2b8b6-125">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| [`InputFile`](xref:blazor/file-uploads) | `<input type="file">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| [`InputRadio`](#radio-buttons) | `<input type="radio">` |
| [`InputRadioGroup`](#radio-buttons) | `<input type="radio">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="2b8b6-126">Vstupní komponenta</span><span class="sxs-lookup"><span data-stu-id="2b8b6-126">Input component</span></span> | <span data-ttu-id="2b8b6-127">Vykresleno jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="2b8b6-127">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> <span data-ttu-id="2b8b6-128">`InputRadio`Komponenty a `InputRadioGroup` jsou k dispozici v ASP.NET Core 5,0 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-128">The `InputRadio` and `InputRadioGroup` components are available in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="2b8b6-129">Další informace získáte výběrem verze 5,0 nebo novější v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-129">For more information, select a 5.0 or later version of this article.</span></span>

::: moniker-end

<span data-ttu-id="2b8b6-130">Všechny vstupní komponenty, včetně <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-130">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="2b8b6-131">Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do vykresleného prvku HTML.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-131">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="2b8b6-132">Vstupní komponenty poskytují výchozí chování při ověřování při změně pole, včetně aktualizace třídy CSS pole tak, aby odrážely stav pole.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-132">Input components provide default behavior for validating when a field is changed, including updating the field CSS class to reflect the field state.</span></span> <span data-ttu-id="2b8b6-133">Některé součásti obsahují užitečnou logiku analýzy.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-133">Some components include useful parsing logic.</span></span> <span data-ttu-id="2b8b6-134">Například <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> a <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> zpracujte neanalyzovatelné hodnoty tak, že zaregistrujete neanalyzovatelné hodnoty jako chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-134">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering unparseable values as validation errors.</span></span> <span data-ttu-id="2b8b6-135">Typy, které mohou přijmout hodnoty null, podporují také hodnotu null cílového pole (například `int?` ).</span><span class="sxs-lookup"><span data-stu-id="2b8b6-135">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="2b8b6-136">Následující `Starship` typ definuje logiku ověřování pomocí větší sady vlastností a poznámek k datům než dříve `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="2b8b6-136">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="2b8b6-137">V předchozím příkladu `Description` je volitelná, protože nejsou k dispozici žádné anotace dat.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-137">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="2b8b6-138">Následující formulář ověří uživatelský vstup pomocí ověřování definovaného v `Starship` modelu:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-138">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
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
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="2b8b6-139"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Vytvoří <xref:Microsoft.AspNetCore.Components.Forms.EditContext> jako [kaskádovou hodnotu](xref:blazor/components/cascading-values-and-parameters) , která sleduje metadata procesu úprav, včetně toho, která pole jsou upravena a aktuální zprávy ověření.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-139">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span>

<span data-ttu-id="2b8b6-140">Přiřaďte **buď** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **nebo** <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> k <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-140">Assign **either** an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **or** an <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> to an <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span> <span data-ttu-id="2b8b6-141">Přiřazení obou není podporované a generuje **chybu za běhu**.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-141">Assignment of both isn't supported and generates a **runtime error**.</span></span>

<span data-ttu-id="2b8b6-142"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Poskytuje užitečné události pro platné a neplatné odeslání formuláře:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-142">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> provides convenient events for valid and invalid form submission:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

<span data-ttu-id="2b8b6-143">Slouží <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> k použití vlastního kódu pro aktivaci ověřování a hodnot pole check.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-143">Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to use custom code to trigger validation and check field values.</span></span>

<span data-ttu-id="2b8b6-144">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-144">In the following example:</span></span>

* <span data-ttu-id="2b8b6-145">`HandleSubmit`Metoda se spustí, když **`Submit`** je vybráno tlačítko.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-145">The `HandleSubmit` method executes when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="2b8b6-146">Formulář se ověří voláním metody <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-146">The form is validated by calling <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="2b8b6-147">Další kód se spustí v závislosti na výsledku ověřování.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-147">Additional code is executed depending on the validation result.</span></span> <span data-ttu-id="2b8b6-148">Umístěte obchodní logiku do metody přiřazené k <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-148">Place business logic in the method assigned to <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate();

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="2b8b6-149">Rozhraní API architektury neexistuje, aby se vymazaly ověřovací zprávy přímo z <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-149">Framework API doesn't exist to clear validation messages directly from an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="2b8b6-150">Proto obecně Nedoporučujeme přidávat ověřovací zprávy do nového <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> formuláře ve formuláři.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-150">Therefore, we don't generally recommend adding validation messages to a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> in a form.</span></span> <span data-ttu-id="2b8b6-151">Chcete-li spravovat ověřovací zprávy, použijte [komponentu validátoru](#validator-components) s [ověřovacím kódem obchodní logiky](#business-logic-validation), jak je popsáno v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-151">To manage validation messages, use a [validator component](#validator-components) with your [business logic validation code](#business-logic-validation), as described in this article.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a><span data-ttu-id="2b8b6-152">Podpora zobrazovaného názvu</span><span class="sxs-lookup"><span data-stu-id="2b8b6-152">Display name support</span></span>

<span data-ttu-id="2b8b6-153">*Tato část se týká ASP.NET Core v rozhraní .NET 5 Release Candidate 1 (RC1) nebo novějším.*</span><span class="sxs-lookup"><span data-stu-id="2b8b6-153">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="2b8b6-154">Následující integrované součásti podporují zobrazované názvy s `DisplayName` parametrem:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-154">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

<span data-ttu-id="2b8b6-155">V následujícím `InputDate` příkladu komponenty:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-155">In the following `InputDate` component example:</span></span>

* <span data-ttu-id="2b8b6-156">Zobrazované jméno ( `DisplayName` ) je nastavené na `birthday` .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-156">The display name (`DisplayName`) is set to `birthday`.</span></span>
* <span data-ttu-id="2b8b6-157">Komponenta je svázána s `BirthDate` vlastností jako `DateTime` typ.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-157">The component is bound to the `BirthDate` property as a `DateTime` type.</span></span>

```razor
<InputDate @bind-Value="@BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

<span data-ttu-id="2b8b6-158">Pokud uživatel neposkytne hodnotu data, zobrazí se chyba ověření jako:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-158">If the user doesn't provide a date value, the validation error appears as:</span></span>

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a><span data-ttu-id="2b8b6-159">Komponenty validátoru</span><span class="sxs-lookup"><span data-stu-id="2b8b6-159">Validator components</span></span>

<span data-ttu-id="2b8b6-160">Komponenty validátoru podporují ověřování formuláře, a to tak, že spravují <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> pro formulář <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-160">Validator components support form validation by managing a <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> for a form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="2b8b6-161">BlazorRozhraní poskytuje <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponentu pro připojení podpory ověřování k formulářům na základě [ověřovacích atributů (datové poznámky)](xref:mvc/models/validation#validation-attributes).</span><span class="sxs-lookup"><span data-stu-id="2b8b6-161">The Blazor framework provides the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component to attach validation support to forms based on [validation attributes (data annotations)](xref:mvc/models/validation#validation-attributes).</span></span> <span data-ttu-id="2b8b6-162">Vytvořte vlastní komponenty validátoru pro zpracování ověřovacích zpráv pro různé formuláře na stejné stránce nebo ve stejném formuláři v různých krocích zpracování formuláře, například ověřování na straně klienta následovaný ověřováním na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-162">Create custom validator components to process validation messages for different forms on the same page or the same form at different steps of form processing, for example client-side validation followed by server-side validation.</span></span> <span data-ttu-id="2b8b6-163">Příklad komponenty validátoru zobrazený v této části se `CustomValidator` používá v následujících částech tohoto článku:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-163">The validator component example shown in this section, `CustomValidator`, is used in the following sections of this article:</span></span>

* [<span data-ttu-id="2b8b6-164">Ověřování obchodní logiky</span><span class="sxs-lookup"><span data-stu-id="2b8b6-164">Business logic validation</span></span>](#business-logic-validation)
* [<span data-ttu-id="2b8b6-165">Ověření serveru</span><span class="sxs-lookup"><span data-stu-id="2b8b6-165">Server validation</span></span>](#server-validation)

> [!NOTE]
> <span data-ttu-id="2b8b6-166">Vlastní atributy ověřování poznámky k datům se dají použít místo vlastních komponent validátoru v mnoha případech.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-166">Custom data annotation validation attributes can be used instead of custom validator components in many cases.</span></span> <span data-ttu-id="2b8b6-167">Vlastní atributy použité pro model formuláře se aktivují s použitím <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponenty.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-167">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="2b8b6-168">Při použití s ověřováním na straně serveru musí být všechny vlastní atributy použité pro model spustitelné na serveru.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-168">When used with server-side validation, any custom attributes applied to the model must be executable on the server.</span></span> <span data-ttu-id="2b8b6-169">Další informace naleznete v tématu <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-169">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

<span data-ttu-id="2b8b6-170">Vytvořit komponentu validátoru z <xref:Microsoft.AspNetCore.Components.ComponentBase> :</span><span class="sxs-lookup"><span data-stu-id="2b8b6-170">Create a validator component from <xref:Microsoft.AspNetCore.Components.ComponentBase>:</span></span>

* <span data-ttu-id="2b8b6-171">Formulář <xref:Microsoft.AspNetCore.Components.Forms.EditContext> je [kaskádový parametr](xref:blazor/components/cascading-values-and-parameters) součásti.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-171">The form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> is a [cascading parameter](xref:blazor/components/cascading-values-and-parameters) of the component.</span></span>
* <span data-ttu-id="2b8b6-172">Při inicializaci komponenty validátoru se vytvoří nový, <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> který udržuje aktuální seznam chyb formuláře.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-172">When the validator component is initialized, a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> is created to maintain a current list of form errors.</span></span>
* <span data-ttu-id="2b8b6-173">Úložiště zpráv přijímá chyby, když kód vývojáře v součásti formuláře volá `DisplayErrors` metodu.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-173">The message store receives errors when developer code in the form's component calls the `DisplayErrors` method.</span></span> <span data-ttu-id="2b8b6-174">Chyby jsou předány `DisplayErrors` metodě v [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-174">The errors are passed to the `DisplayErrors` method in a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2).</span></span> <span data-ttu-id="2b8b6-175">Ve slovníku je klíč názvem pole formuláře, které obsahuje jednu nebo více chyb.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-175">In the dictionary, the key is the name of the form field that has one or more errors.</span></span> <span data-ttu-id="2b8b6-176">Hodnota je seznam chyb.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-176">The value is the error list.</span></span>
* <span data-ttu-id="2b8b6-177">Zprávy jsou vymazány, pokud došlo k některé z následujících:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-177">Messages are cleared when any of the following have occurred:</span></span>
  * <span data-ttu-id="2b8b6-178">Ověřování je požadováno <xref:Microsoft.AspNetCore.Components.Forms.EditContext> při <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> vyvolání události.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-178">Validation is requested on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> event is raised.</span></span> <span data-ttu-id="2b8b6-179">Všechny chyby jsou vymazány.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-179">All of the errors are cleared.</span></span>
  * <span data-ttu-id="2b8b6-180">Při vyvolání události se změní pole ve formuláři <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-180">A field changes in the form when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> event is raised.</span></span> <span data-ttu-id="2b8b6-181">Vymažou se jenom chyby pro toto pole.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-181">Only the errors for the field are cleared.</span></span>
  * <span data-ttu-id="2b8b6-182">`ClearErrors`Metoda je volána kódem pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-182">The `ClearErrors` method is called by developer code.</span></span> <span data-ttu-id="2b8b6-183">Všechny chyby jsou vymazány.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-183">All of the errors are cleared.</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

namespace BlazorSample.Client
{
    public class CustomValidator : ComponentBase
    {
        private ValidationMessageStore messageStore;

        [CascadingParameter]
        private EditContext CurrentEditContext { get; set; }

        protected override void OnInitialized()
        {
            if (CurrentEditContext == null)
            {
                throw new InvalidOperationException(
                    $"{nameof(CustomValidator)} requires a cascading " +
                    $"parameter of type {nameof(EditContext)}. " +
                    $"For example, you can use {nameof(CustomValidator)} " +
                    $"inside an {nameof(EditForm)}.");
            }

            messageStore = new ValidationMessageStore(CurrentEditContext);

            CurrentEditContext.OnValidationRequested += (s, e) => 
                messageStore.Clear();
            CurrentEditContext.OnFieldChanged += (s, e) => 
                messageStore.Clear(e.FieldIdentifier);
        }

        public void DisplayErrors(Dictionary<string, List<string>> errors)
        {
            foreach (var err in errors)
            {
                messageStore.Add(CurrentEditContext.Field(err.Key), err.Value);
            }

            CurrentEditContext.NotifyValidationStateChanged();
        }

        public void ClearErrors()
        {
            messageStore.Clear();
            CurrentEditContext.NotifyValidationStateChanged();
        }
    }
}
```

## <a name="business-logic-validation"></a><span data-ttu-id="2b8b6-184">Ověřování obchodní logiky</span><span class="sxs-lookup"><span data-stu-id="2b8b6-184">Business logic validation</span></span>

<span data-ttu-id="2b8b6-185">Ověřování obchodní logiky lze provést pomocí [komponenty validátoru](#validator-components) , která přijímá chyby formuláře ve slovníku.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-185">Business logic validation can be accomplished with a [validator component](#validator-components) that receives form errors in a dictionary.</span></span>

<span data-ttu-id="2b8b6-186">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-186">In the following example:</span></span>

* <span data-ttu-id="2b8b6-187">`CustomValidator`Součást z části [komponenty validátoru](#validator-components) tohoto článku se používá.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-187">The `CustomValidator` component from the [Validator components](#validator-components) section of this article is used.</span></span>
* <span data-ttu-id="2b8b6-188">`Description`Pokud uživatel vybere `Defense` klasifikaci expedice (), vyžaduje ověření hodnotu pro popis příjemce () `Classification` .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-188">The validation requires a value for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="2b8b6-189">Když jsou ověřovací zprávy nastaveny v komponentě, přidají se do validátoru <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> a zobrazí se v <xref:Microsoft.AspNetCore.Components.Forms.EditForm> :</span><span class="sxs-lookup"><span data-stu-id="2b8b6-189">When validation messages are set in the component, they're added to the validator's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> and shown in the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    ...

</EditForm>

@code {
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        customValidator.ClearErrors();

        var errors = new Dictionary<string, List<string>>();

        if (starship.Classification == "Defense" &&
                string.IsNullOrEmpty(starship.Description))
        {
            errors.Add(nameof(starship.Description),
                new List<string>() { "For a 'Defense' ship classification, " +
                "'Description' is required." });
        }

        if (errors.Count() > 0)
        {
            customValidator.DisplayErrors(errors);
        }
        else
        {
            // Process the form
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="2b8b6-190">Jako alternativu k používání [ověřovacích komponent](#validator-components)lze použít ověřovací atributy datových poznámek.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-190">As an alternative to using [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="2b8b6-191">Vlastní atributy použité pro model formuláře se aktivují s použitím <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponenty.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-191">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="2b8b6-192">Při použití s ověřováním na straně serveru musí být atributy spustitelné na serveru.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-192">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="2b8b6-193">Další informace naleznete v tématu <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-193">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

## <a name="server-validation"></a><span data-ttu-id="2b8b6-194">Ověření serveru</span><span class="sxs-lookup"><span data-stu-id="2b8b6-194">Server validation</span></span>

<span data-ttu-id="2b8b6-195">Ověření serveru se dá provést pomocí [komponenty validátoru](#validator-components)serveru:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-195">Server validation can be accomplished with a server [validator component](#validator-components):</span></span>

* <span data-ttu-id="2b8b6-196">Zpracujte ověřování na straně klienta ve formuláři s <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponentou.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-196">Process client-side validation in the form with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="2b8b6-197">Když formulář projde ověřováním na straně klienta ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> je volán), odešlete <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> rozhraní API back-end serveru pro zpracování formuláře.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-197">When the form passes client-side validation (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> is called), send the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> to a backend server API for form processing.</span></span>
* <span data-ttu-id="2b8b6-198">Ověřování modelu procesu na serveru.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-198">Process model validation on the server.</span></span>
* <span data-ttu-id="2b8b6-199">Rozhraní API serveru zahrnuje integrované ověřování poznámek k datům architektury a vlastní logiku ověřování poskytovanou vývojářem.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-199">The server API includes both the built-in framework data annotations validation and custom validation logic supplied by the developer.</span></span> <span data-ttu-id="2b8b6-200">Pokud ověření projde na serveru, zpracujte formulář a odešlete zpět stavový kód úspěchu (*200 – OK*).</span><span class="sxs-lookup"><span data-stu-id="2b8b6-200">If validation passes on the server, process the form and send back a success status code (*200 - OK*).</span></span> <span data-ttu-id="2b8b6-201">Pokud se ověření nezdaří, vrátí stavový kód chyby (*400 – Chybný požadavek*) a chyby ověření pole.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-201">If validation fails, return a failure status code (*400 - Bad Request*) and the field validation errors.</span></span>
* <span data-ttu-id="2b8b6-202">Buď zakažte formulář při úspěchu nebo zobrazte chyby.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-202">Either disable the form on success or display the errors.</span></span>

<span data-ttu-id="2b8b6-203">Následující příklad je založen na:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-203">The following example is based on:</span></span>

* <span data-ttu-id="2b8b6-204">Hostované Blazor řešení vytvořené [ Blazor šablonou hostovaného projektu](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="2b8b6-204">A hosted Blazor solution created by the [Blazor Hosted project template](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="2b8b6-205">Příklad lze použít se všemi zabezpečenými hostovanými Blazor řešeními popsanými v [dokumentaci zabezpečení a Identity dokumentace](xref:blazor/security/webassembly/index#implementation-guidance).</span><span class="sxs-lookup"><span data-stu-id="2b8b6-205">The example can be used with any of the secure hosted Blazor solutions described in the [Security and Identity documentation](xref:blazor/security/webassembly/index#implementation-guidance).</span></span>
* <span data-ttu-id="2b8b6-206">Příklad formuláře *databáze Starfleet Starship* v předchozích [vestavěných součástech Forms](#built-in-forms-components) .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-206">The *Starfleet Starship Database* form example in the preceding [Built-in forms components](#built-in-forms-components) section.</span></span>
* <span data-ttu-id="2b8b6-207">Blazor <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Komponenta rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-207">The Blazor framework's <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="2b8b6-208">`CustomValidator`Komponenta zobrazená v části [komponenty validátoru](#validator-components) .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-208">The `CustomValidator` component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="2b8b6-209">V následujícím příkladu rozhraní API serveru ověřuje, zda je zadána hodnota pro popis příjemce ( `Description` ), pokud uživatel vybere `Defense` klasifikaci expedice ( `Classification` ).</span><span class="sxs-lookup"><span data-stu-id="2b8b6-209">In the following example, the server API validates that a value is provided for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="2b8b6-210">Model umístěte `Starship` do `Shared` projektu řešení tak, aby aplikace klienta i serveru mohly model používat.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-210">Place the `Starship` model into the solution's `Shared` project so that both the client and server apps can use the model.</span></span> <span data-ttu-id="2b8b6-211">Vzhledem k tomu, že model vyžaduje datové poznámky, přidejte odkaz na balíček pro [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) do `Shared` souboru projektu projektu:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-211">Since the model requires data annotations, add a package reference for [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) to the `Shared` project's project file:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

<span data-ttu-id="2b8b6-212">Pokud chcete zjistit nejnovější verzi balíčku, který není ve verzi Preview, podívejte se na **historii verzí** balíčku na adrese [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span><span class="sxs-lookup"><span data-stu-id="2b8b6-212">To determine the latest non-preview version of the package, see the package **Version History** at [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span></span>

<span data-ttu-id="2b8b6-213">V projektu rozhraní API serveru přidejte kontroler pro zpracování požadavků na ověření Starship ( `Controllers/StarshipValidation.cs` ) a vraťte se k neúspěšným ověřovacím zprávám:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-213">In the server API project, add a controller to process starship validation requests (`Controllers/StarshipValidation.cs`) and return failed validation messages:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace BlazorSample.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class StarshipValidationController : ControllerBase
    {
        private readonly ILogger<StarshipValidationController> logger;

        public StarshipValidationController(
            ILogger<StarshipValidationController> logger)
        {
            this.logger = logger;
        }

        [HttpPost]
        public async Task<IActionResult> Post(Starship starship)
        {
            try
            {
                if (starship.Classification == "Defense" && 
                    string.IsNullOrEmpty(starship.Description))
                {
                    ModelState.AddModelError(nameof(starship.Description),
                        "For a 'Defense' ship " +
                        "classification, 'Description' is required.");
                }
                else
                {
                    // Process the form asynchronously
                    // async ...

                    return Ok(ModelState);
                }
            }
            catch (Exception ex)
            {
                logger.LogError("Validation Error: {MESSAGE}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

<span data-ttu-id="2b8b6-214">Pokud dojde k chybě ověřování vazby modelu na serveru, [`ApiController`](xref:web-api/index) <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> vrátí () obvykle [výchozí chybnou odpověď na požadavek](xref:web-api/index#default-badrequest-response) s hodnotou <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-214">When a model binding validation error occurs on the server, an [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) normally returns a [default bad request response](xref:web-api/index#default-badrequest-response) with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="2b8b6-215">Odpověď obsahuje více dat než pouze chyby ověřování, jak je znázorněno v následujícím příkladu, pokud nejsou odeslána všechna pole formuláře *databáze Starfleet Starship* a formulář se nezdařil. ověření se nezdařilo:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-215">The response contains more data than just the validation errors, as shown in the following example when all of the fields of the *Starfleet Starship Database* form aren't submitted and the form fails validation:</span></span>

```json
{
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Identifier": ["The Identifier field is required."],
    "Classification": ["The Classification field is required."],
    "IsValidatedDesign": ["This form disallows unapproved ships."],
    "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
  }
}
```

<span data-ttu-id="2b8b6-216">Pokud rozhraní API serveru vrátí předchozí výchozí odpověď JSON, může klient analyzovat odpověď pro získání podřízených objektů `errors` uzlu.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-216">If the server API returns the preceding default JSON response, it's possible for the client to parse the response to obtain the children of the `errors` node.</span></span> <span data-ttu-id="2b8b6-217">Není ale vhodné analyzovat soubor.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-217">However, it's inconvenient to parse the file.</span></span> <span data-ttu-id="2b8b6-218">Při analýze JSON se vyžaduje další kód po volání <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> , aby se vytvořily [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) chyby pro zpracování chyb ověřování pomocí formulářů.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-218">Parsing the JSON requires additional code after calling <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> in order to produce a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) of errors for forms validation error processing.</span></span> <span data-ttu-id="2b8b6-219">V ideálním případě by mělo serverové rozhraní API vracet pouze chyby ověřování:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-219">Ideally, the server API should only return the validation errors:</span></span>

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

<span data-ttu-id="2b8b6-220">Chcete-li změnit odpověď rozhraní API serveru tak, aby vracela pouze chyby ověřování, změňte delegáta, který je vyvolán na akcích, které jsou v aplikaci opatřeny poznámkami <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-220">To modify the server API's response to make it only return the validation errors, change the delegate that's invoked on actions that are annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2b8b6-221">Pro koncový bod rozhraní API ( `/StarshipValidation` ), vraťte a <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> s <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-221">For the API endpoint (`/StarshipValidation`), return a <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> with the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>.</span></span> <span data-ttu-id="2b8b6-222">Pro jakékoli jiné koncové body rozhraní API zachovejte výchozí chování vrácením výsledku objektu s novým <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> :</span><span class="sxs-lookup"><span data-stu-id="2b8b6-222">For any other API endpoints, preserve the default behavior by returning the object result with a new <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>:</span></span>

```csharp
using Microsoft.AspNetCore.Mvc;

...

services.AddControllersWithViews()
    .ConfigureApiBehaviorOptions(options =>
    {
        options.InvalidModelStateResponseFactory = context =>
        {
            if (context.HttpContext.Request.Path == "/StarshipValidation")
            {
                return new BadRequestObjectResult(context.ModelState);
            }
            else
            {
                return new BadRequestObjectResult(
                    new ValidationProblemDetails(context.ModelState));
            }
        };
    });
```

<span data-ttu-id="2b8b6-223">Další informace naleznete v tématu <xref:web-api/handle-errors#validation-failure-error-response>.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-223">For more information, see <xref:web-api/handle-errors#validation-failure-error-response>.</span></span>

<span data-ttu-id="2b8b6-224">V projektu klienta přidejte komponentu validátoru zobrazenou v části [komponenty validátoru](#validator-components) .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-224">In the client project, add the validator component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="2b8b6-225">V projektu klienta se aktualizuje formulář *databáze Starfleet Starship* , aby se zobrazily chyby ověření serveru s použitím `CustomValidator` komponenty.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-225">In the client project, the *Starfleet Starship Database* form is updated to show server validation errors with help of the `CustomValidator` component.</span></span> <span data-ttu-id="2b8b6-226">Když rozhraní API serveru vrátí ověřovací zprávy, přidají se do `CustomValidator` komponenty <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-226">When the server API returns validation messages, they're added to the `CustomValidator` component's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>.</span></span> <span data-ttu-id="2b8b6-227">Chyby jsou k dispozici ve formuláři <xref:Microsoft.AspNetCore.Components.Forms.EditContext> pro zobrazení pomocí formuláře <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> :</span><span class="sxs-lookup"><span data-stu-id="2b8b6-227">The errors are available in the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> for display by the form's <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>:</span></span>

```razor
@page "/FormValidation"
@using System.Net
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@using BlazorSample.Shared
@attribute [Authorize]
@inject HttpClient Http
@inject ILogger<FormValidation> Logger

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification" disabled="@disabled">
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
            <InputNumber @bind-Value="starship.MaximumAccommodation" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" disabled="@disabled" />
        </label>
    </p>

    <button type="submit" disabled="@disabled">Submit</button>

    <p style="@messageStyles">
        @message
    </p>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>,
        &copy;1966-2019 CBS Studios, Inc. and
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private bool disabled;
    private string message;
    private string messageStyles = "visibility:hidden";
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private async Task HandleValidSubmit(EditContext editContext)
    {
        customValidator.ClearErrors();

        try
        {
            var response = await Http.PostAsJsonAsync<Starship>(
                "StarshipValidation", (Starship)editContext.Model);

            var errors = await response.Content
                .ReadFromJsonAsync<Dictionary<string, List<string>>>();

            if (response.StatusCode == HttpStatusCode.BadRequest && 
                errors.Count() > 0)
            {
                customValidator.DisplayErrors(errors);
            }
            else if (!response.IsSuccessStatusCode)
            {
                throw new HttpRequestException(
                    $"Validation failed. Status Code: {response.StatusCode}");
            }
            else
            {
                disabled = true;
                messageStyles = "color:green";
                message = "The form has been processed.";
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            ex.Redirect();
        }
        catch (Exception ex)
        {
            Logger.LogError("Form processing error: {MESSAGE}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="2b8b6-228">Jako alternativu k [součástem ověřování](#validator-components)lze použít atributy ověření poznámky k datům.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-228">As an alternative to [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="2b8b6-229">Vlastní atributy použité pro model formuláře se aktivují s použitím <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponenty.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-229">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="2b8b6-230">Při použití s ověřováním na straně serveru musí být atributy spustitelné na serveru.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-230">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="2b8b6-231">Další informace naleznete v tématu <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-231">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

> [!NOTE]
> <span data-ttu-id="2b8b6-232">Přístup k ověřování na straně serveru v této části je vhodný pro jakékoli Blazor WebAssembly Příklady hostovaných řešení v této sadě dokumentace:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-232">The server-side validation approach in this section is suitable for any of the Blazor WebAssembly hosted solution examples in this documentation set:</span></span>
>
> * [<span data-ttu-id="2b8b6-233">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="2b8b6-233">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [<span data-ttu-id="2b8b6-234">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="2b8b6-234">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [<span data-ttu-id="2b8b6-235">Identity WebServer</span><span class="sxs-lookup"><span data-stu-id="2b8b6-235">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="2b8b6-236">InputText na základě události vstupu</span><span class="sxs-lookup"><span data-stu-id="2b8b6-236">InputText based on the input event</span></span>

<span data-ttu-id="2b8b6-237">Pomocí <xref:Microsoft.AspNetCore.Components.Forms.InputText> komponenty můžete vytvořit vlastní komponentu, která `input` místo události používá událost `change` .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-237">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="2b8b6-238">V následujícím příkladu `CustomInputText` Komponenta dědí `InputText` komponentu rozhraní a nastaví vazbu na událost ( <xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A> ) na `oninput` událost.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-238">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="2b8b6-239">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-239">`Shared/CustomInputText.razor`:</span></span>

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

<span data-ttu-id="2b8b6-240">`CustomInputText`Komponentu lze použít kdekoli <xref:Microsoft.AspNetCore.Components.Forms.InputText> :</span><span class="sxs-lookup"><span data-stu-id="2b8b6-240">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="2b8b6-241">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-241">`Pages/TestForm.razor`:</span></span>

```razor
@page "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
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
        ...
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="2b8b6-242">Přepínače</span><span class="sxs-lookup"><span data-stu-id="2b8b6-242">Radio buttons</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2b8b6-243">Pomocí `InputRadio` komponent s `InputRadioGroup` komponentou vytvořte skupinu přepínačů.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-243">Use `InputRadio` components with the `InputRadioGroup` component to create a radio button group.</span></span> <span data-ttu-id="2b8b6-244">V následujícím příkladu jsou přidány vlastnosti do `Starship` modelu popsaného v části [předdefinované součásti formulářů](#built-in-forms-components) :</span><span class="sxs-lookup"><span data-stu-id="2b8b6-244">In the following example, properties are added to the `Starship` model described in the [Built-in forms components](#built-in-forms-components) section:</span></span>

```csharp
[Required]
[Range(typeof(Manufacturer), nameof(Manufacturer.SpaceX), 
    nameof(Manufacturer.VirginGalactic), ErrorMessage = "Pick a manufacturer.")]
public Manufacturer Manufacturer { get; set; } = Manufacturer.Unknown;

[Required, EnumDataType(typeof(Color))]
public Color? Color { get; set; } = null;

[Required, EnumDataType(typeof(Engine))]
public Engine? Engine { get; set; } = null;
```

<span data-ttu-id="2b8b6-245">`enums`Do aplikace přidejte následující.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-245">Add the following `enums` to the app.</span></span> <span data-ttu-id="2b8b6-246">Vytvořte nový soubor, který bude obsahovat `enums` nebo přidejte do `enums` `Starship.cs` souboru.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-246">Create a new file to hold the `enums` or add the `enums` to the `Starship.cs` file.</span></span> <span data-ttu-id="2b8b6-247">Zpřístupněte `enums` přístup k `Starship` modelu a formuláři *databáze Starfleet Starship* :</span><span class="sxs-lookup"><span data-stu-id="2b8b6-247">Make the `enums` accessible to the `Starship` model and the *Starfleet Starship Database* form:</span></span>

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, Virgin, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

<span data-ttu-id="2b8b6-248">Aktualizujte formulář *databáze Starfleet Starship* , který je popsaný v části [předdefinované komponenty formulářů](#built-in-forms-components) .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-248">Update the *Starfleet Starship Database* form described in the [Built-in forms components](#built-in-forms-components) section.</span></span> <span data-ttu-id="2b8b6-249">Přidejte součásti, které chcete vytvořit:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-249">Add the components to produce:</span></span>

* <span data-ttu-id="2b8b6-250">Skupina přepínacích tlačítek pro výrobce lodi.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-250">A radio button group for the ship manufacturer.</span></span>
* <span data-ttu-id="2b8b6-251">Vnořená skupina přepínacích tlačítek pro expedici barva a modul.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-251">A nested radio button group for ship color and engine.</span></span>

```razor
<p>
    <InputRadioGroup @bind-Value="starship.Manufacturer">
        Manufacturer:
        <br>
        @foreach (var manufacturer in (Manufacturer[])Enum
            .GetValues(typeof(Manufacturer)))
        {
            <InputRadio Value="manufacturer" />
            @manufacturer
            <br>
        }
    </InputRadioGroup>
</p>

<p>
    Pick one color and one engine:
    <InputRadioGroup Name="engine" @bind-Value="starship.Engine">
        <InputRadioGroup Name="color" @bind-Value="starship.Color">
            <InputRadio Name="color" Value="Color.ImperialRed" />Imperial Red<br>
            <InputRadio Name="engine" Value="Engine.Ion" />Ion<br>
            <InputRadio Name="color" Value="Color.SpacecruiserGreen" />
                Spacecruiser Green<br>
            <InputRadio Name="engine" Value="Engine.Plasma" />Plasma<br>
            <InputRadio Name="color" Value="Color.StarshipBlue" />Starship Blue<br>
            <InputRadio Name="engine" Value="Engine.Fusion" />Fusion<br>
            <InputRadio Name="color" Value="Color.VoyagerOrange" />
                Voyager Orange<br>
            <InputRadio Name="engine" Value="Engine.Warp" />Warp<br>
        </InputRadioGroup>
    </InputRadioGroup>
</p>
```

> [!NOTE]
> <span data-ttu-id="2b8b6-252">Pokud `Name` je tento parametr vynechán, `InputRadio` komponenty jsou seskupeny podle jejich posledního předchůdce.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-252">If `Name` is omitted, `InputRadio` components are grouped by their most recent ancestor.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="2b8b6-253">Při práci s přepínači ve formuláři je datová vazba zpracovávána jinak než jiné prvky, protože přepínače jsou vyhodnocovány jako skupina.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-253">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="2b8b6-254">Hodnota každého přepínacího tlačítka je pevná, ale hodnota skupiny přepínačů je hodnota vybraného přepínacího tlačítka.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-254">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="2b8b6-255">Následující příklad ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-255">The following example shows how to:</span></span>

* <span data-ttu-id="2b8b6-256">Zpracuje datovou vazbu pro skupinu přepínačů.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-256">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="2b8b6-257">Podpora ověřování pomocí vlastní `InputRadio` součásti.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-257">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="2b8b6-258">V následujícím <xref:Microsoft.AspNetCore.Components.Forms.EditForm> příkladu se `InputRadio` k získání a ověření hodnocení od uživatele používá předchozí komponenta:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-258">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
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
        ...
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

::: moniker-end

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="2b8b6-259">Vazba `<select>` možností elementu na hodnoty objektu C# `null`</span><span class="sxs-lookup"><span data-stu-id="2b8b6-259">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="2b8b6-260">Neexistuje žádný rozumné způsob, jak vyjádřit `<select>` hodnotu možnosti prvku jako hodnotu objektu C# `null` , protože:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-260">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="2b8b6-261">Atributy HTML nemohou mít `null` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-261">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="2b8b6-262">Nejbližší ekvivalent k `null` v jazyce HTML je absence `value` atributu HTML z `<option>` elementu.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-262">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="2b8b6-263">Při výběru možnosti bez `<option>` `value` atributu prohlížeč považuje hodnotu za *textový obsah* `<option>` elementu.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-263">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="2b8b6-264">BlazorRozhraní se nepokouší potlačit výchozí chování, protože by zahrnovalo:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-264">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="2b8b6-265">Vytvoření řetězce alternativních řešení zvláštních případů v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-265">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="2b8b6-266">Zásadní změny v aktuálním chování architektury.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-266">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2b8b6-267">Plausible `null` ekvivalent v HTML je *prázdný řetězec* `value` .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-267">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="2b8b6-268">BlazorArchitektura zpracovává `null` prázdné převody řetězce pro oboustrannou vazbu na `<select>` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-268">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="2b8b6-269">Blazor `null` Při pokusu o obousměrnou vazbu na hodnotu se rozhraní nezpracovává automaticky k prázdným převodům řetězců `<select>` .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-269">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="2b8b6-270">Další informace naleznete v tématu [Oprava vazby `<select>` na hodnotu null (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span><span class="sxs-lookup"><span data-stu-id="2b8b6-270">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="2b8b6-271">Podpora ověřování</span><span class="sxs-lookup"><span data-stu-id="2b8b6-271">Validation support</span></span>

<span data-ttu-id="2b8b6-272"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Komponenta připojuje ověřování pomocí datových poznámek do kaskády <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-272">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="2b8b6-273">Povolení podpory pro ověřování pomocí datových poznámek vyžaduje toto explicitní gesto.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-273">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="2b8b6-274">Chcete-li použít jiný systém ověřování než datové poznámky, nahraďte <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> vlastní implementaci.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-274">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="2b8b6-275">Implementace ASP.NET Core je k dispozici pro kontrolu v referenčním zdroji: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-275">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="2b8b6-276">Předchozí odkazy na zdroj referencí poskytují kód z `master` větve úložiště, která představuje aktuální vývoj jednotky produktu pro příští vydání ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-276">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="2b8b6-277">Pokud chcete vybrat větev pro jinou verzi, použijte selektor větve GitHubu (například `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="2b8b6-277">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

<span data-ttu-id="2b8b6-278">Blazor provádí dva typy ověřování:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-278">Blazor performs two types of validation:</span></span>

* <span data-ttu-id="2b8b6-279">*Ověřování polí* se provede, když se karty uživatele nacházejí v poli.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-279">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="2b8b6-280">Při ověřování pole <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> přidruží součást všechny hlášené výsledky ověření k poli.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-280">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="2b8b6-281">*Ověřování modelu* se provede, když uživatel formulář odešle.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-281">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="2b8b6-282">Při ověřování modelu se <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Komponenta pokusí určit pole na základě názvu člena, který sestavy výsledků ověření.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-282">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="2b8b6-283">Výsledky ověření, které nejsou přidruženy k jednotlivým členům, jsou přidruženy k modelu, nikoli poli.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-283">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="2b8b6-284">Komponenty zprávy pro Shrnutí a ověření</span><span class="sxs-lookup"><span data-stu-id="2b8b6-284">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="2b8b6-285"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Komponenta shrnuje všechny zprávy o ověřování, které se podobají [pomocníka značek pro Shrnutí ověření](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="2b8b6-285">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="2b8b6-286">Zprávy ověřování výstupu pro konkrétní model s `Model` parametrem:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-286">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="2b8b6-287"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Komponenta zobrazí ověřovací zprávy pro konkrétní pole, které se podobá [pomocníka značek ověřovací zprávy](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="2b8b6-287">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="2b8b6-288">Zadejte pole pro ověření s `For` atributem a výrazem lambda pojmenování vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-288">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="2b8b6-289"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Komponenty a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-289">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="2b8b6-290">Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do generovaného `<div>` `<ul>` prvku nebo.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-290">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="2b8b6-291">Řízení stylu ověřovacích zpráv v šabloně stylů aplikace ( `wwwroot/css/app.css` nebo `wwwroot/css/site.css` ).</span><span class="sxs-lookup"><span data-stu-id="2b8b6-291">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="2b8b6-292">Výchozí `validation-message` třída nastaví barvu textu pro zprávy ověření na červenou:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-292">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="2b8b6-293">Vlastní ověřovací atributy</span><span class="sxs-lookup"><span data-stu-id="2b8b6-293">Custom validation attributes</span></span>

<span data-ttu-id="2b8b6-294">Chcete-li zajistit, aby byl výsledek ověření správně přidružen k poli při použití [vlastního ověřovacího atributu](xref:mvc/models/validation#custom-attributes), předejte kontext ověření <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> při vytváření <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="2b8b6-294">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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
> <span data-ttu-id="2b8b6-295"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> je `null`.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-295"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="2b8b6-296">Vložené služby pro ověřování v metodě se `IsValid` nepodporují.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-296">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a><span data-ttu-id="2b8b6-297">Vlastní atributy třídy ověřování</span><span class="sxs-lookup"><span data-stu-id="2b8b6-297">Custom validation class attributes</span></span>

<span data-ttu-id="2b8b6-298">Vlastní názvy tříd ověřování jsou užitečné při integraci s rozhraními CSS, jako je například [bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="2b8b6-298">Custom validation class names are useful when integrating with CSS frameworks, such as [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="2b8b6-299">Chcete-li zadat vlastní názvy tříd ověřování, vytvořte třídu odvozenou z `FieldCssClassProvider` a nastavte třídu v <xref:Microsoft.AspNetCore.Components.Forms.EditContext> instanci:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-299">To specify custom validation class names, create a class derived from `FieldCssClassProvider` and set the class on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> instance:</span></span>

```csharp
var editContext = new EditContext(model);
editContext.SetFieldCssClassProvider(new MyFieldClassProvider());

...

private class MyFieldClassProvider : FieldCssClassProvider
{
    public override string GetFieldCssClass(EditContext editContext, 
        in FieldIdentifier fieldIdentifier)
    {
        var isValid = !editContext.GetValidationMessages(fieldIdentifier).Any();

        return isValid ? "good field" : "bad field";
    }
}
```

::: moniker-end

### <a name="no-locblazor-data-annotations-validation-package"></a><span data-ttu-id="2b8b6-300">Blazor balíček pro ověření datových poznámek</span><span class="sxs-lookup"><span data-stu-id="2b8b6-300">Blazor data annotations validation package</span></span>

<span data-ttu-id="2b8b6-301">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)Je balíček, který vyplní prodlevy při ověřování pomocí <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponenty.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-301">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="2b8b6-302">Balíček je momentálně *experimentální*.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-302">The package is currently *experimental*.</span></span>

> [!NOTE]
> <span data-ttu-id="2b8b6-303">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)Balíček má nejnovější verzi *Release Candidate* na adrese [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). V tuto chvíli pokračujte v používání *experimentálního* balíčku Release Candidate.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-303">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package has a latest version of *release candidate* at [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continue to use the *experimental* release candidate package at this time.</span></span> <span data-ttu-id="2b8b6-304">Sestavení balíčku může být přesunuto do rozhraní nebo modulu runtime v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-304">The package's assembly might be moved to either the framework or the runtime in a future release.</span></span> <span data-ttu-id="2b8b6-305">Podívejte se na [úložiště GitHubu oznámení](https://github.com/aspnet/Announcements), [úložiště GitHub/aspnetcore](https://github.com/dotnet/aspnetcore), nebo v části tohoto tématu, kde najdete další aktualizace.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-305">Watch the [Announcements GitHub repository](https://github.com/aspnet/Announcements), the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore), or this topic section for further updates.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="2b8b6-306">[CompareProperty] – atribut</span><span class="sxs-lookup"><span data-stu-id="2b8b6-306">[CompareProperty] attribute</span></span>

<span data-ttu-id="2b8b6-307"><xref:System.ComponentModel.DataAnnotations.CompareAttribute>Nefunguje dobře s <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponentou, protože nepřiřazuje výsledek ověření k určitému členu.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-307">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="2b8b6-308">To může vést k nekonzistentnímu chování mezi ověřováním na úrovni polí a při ověřování celého modelu při odeslání.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-308">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="2b8b6-309">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *Experimentální* balíček zavádí další ověřovací atribut, který tato `ComparePropertyAttribute` omezení funguje.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-309">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="2b8b6-310">V Blazor aplikaci `[CompareProperty]` je přímá náhrada za [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-310">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="2b8b6-311">Vnořené modely, typy kolekcí a komplexní typy</span><span class="sxs-lookup"><span data-stu-id="2b8b6-311">Nested models, collection types, and complex types</span></span>

<span data-ttu-id="2b8b6-312">Blazor poskytuje podporu pro ověřování vstupu formuláře pomocí datových poznámek s integrovaným <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-312">Blazor provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="2b8b6-313"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Pouze ověří vlastnosti nejvyšší úrovně modelu svázaného s formulářem, který není vlastností kolekce nebo komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-313">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="2b8b6-314">Chcete-li ověřit celý graf objektu vázaného modelu, včetně vlastností kolekce a komplexního typu, použijte příkaz `ObjectGraphDataAnnotationsValidator` poskytnutý *experimentálním* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) balíčkem:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-314">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="2b8b6-315">Přidávejte do vlastností modelu poznámky `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-315">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="2b8b6-316">V následujících třídách modelu `ShipDescription` obsahuje Třída další datové poznámky pro ověření, když je model svázán s formulářem:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-316">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="2b8b6-317">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-317">`Starship.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; } = 
        new ShipDescription();

    ...
}
```

<span data-ttu-id="2b8b6-318">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-318">`ShipDescription.cs`:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="2b8b6-319">Povolit tlačítko Odeslat na základě ověření formuláře</span><span class="sxs-lookup"><span data-stu-id="2b8b6-319">Enable the submit button based on form validation</span></span>

<span data-ttu-id="2b8b6-320">Povolení a zakázání tlačítka Odeslat na základě ověření formuláře:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-320">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="2b8b6-321"><xref:Microsoft.AspNetCore.Components.Forms.EditContext>K přiřazení modelu při inicializaci komponenty použijte formulář.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-321">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="2b8b6-322">Ověřte formulář v <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> zpětném volání kontextu a povolte a zakažte tlačítko Odeslat.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-322">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="2b8b6-323">Odpojte obslužnou rutinu události v `Dispose` metodě.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-323">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="2b8b6-324">Další informace naleznete v tématu <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-324">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

> [!NOTE]
> <span data-ttu-id="2b8b6-325">Při použití <xref:Microsoft.AspNetCore.Components.Forms.EditContext> , nepřiřazujte také <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> k <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-325">When using an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, don't also assign a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
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

<span data-ttu-id="2b8b6-326">V předchozím příkladu nastavte `formInvalid` na `false` if:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-326">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="2b8b6-327">Formulář je předem načten s platnými výchozími hodnotami.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-327">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="2b8b6-328">Chcete povolit tlačítko Odeslat, když se formulář načte.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-328">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="2b8b6-329">Vedlejším účinkem předcházejícího přístupu je, že <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> Komponenta je naplněná neplatnými poli poté, co uživatel komunikuje s jakýmkoli jedním polem.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-329">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="2b8b6-330">Tento scénář je možné vyřešit jedním z následujících způsobů:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-330">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="2b8b6-331">Nepoužívejte <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> komponentu na formuláři.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-331">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="2b8b6-332">Zpřístupní <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> komponentu, když je vybráno tlačítko Odeslat (například v `HandleValidSubmit` metodě).</span><span class="sxs-lookup"><span data-stu-id="2b8b6-332">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="@HandleValidSubmit">
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

## <a name="troubleshoot"></a><span data-ttu-id="2b8b6-333">Odstraňování potíží</span><span class="sxs-lookup"><span data-stu-id="2b8b6-333">Troubleshoot</span></span>

> <span data-ttu-id="2b8b6-334">InvalidOperationException: EditForm vyžaduje parametr modelu nebo parametr EditContext, ale ne obojí.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-334">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="2b8b6-335">Potvrďte, že <xref:Microsoft.AspNetCore.Components.Forms.EditForm> má <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **nebo** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="2b8b6-335">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="2b8b6-336">U stejného formuláře nepoužívejte obojí.</span><span class="sxs-lookup"><span data-stu-id="2b8b6-336">Don't use both for the same form.</span></span>

<span data-ttu-id="2b8b6-337">Při přiřazování <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> do formuláře potvrďte, že je vytvořen typ modelu, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="2b8b6-337">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

::: moniker range=">= aspnetcore-5.0"

## <a name="additional-resources"></a><span data-ttu-id="2b8b6-338">Další materiály</span><span class="sxs-lookup"><span data-stu-id="2b8b6-338">Additional resources</span></span>

* <xref:blazor/file-uploads>

::: moniker-end
