---
title: ASP.NET Core formuláře a ověřování Blazor
author: guardrex
description: Naučte se používat scénáře ověřování formulářů a polí v Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/forms-validation
ms.openlocfilehash: 6f6ace3deb7ed4262b643d897273bc767334b5e6
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207198"
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

* Formulář ověřuje vstup uživatele v `name` poli pomocí ověřování definovaného `ExampleModel` v typu. Model se vytvoří v `@code` bloku komponenty a je uložený v soukromém poli (`exampleModel`). Pole je přiřazeno `Model` atributu `<EditForm>` elementu.
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

Vstupní komponenty poskytují výchozí chování pro ověřování při úpravách a změně jejich třídy CSS tak, aby odrážely stav pole. Některé součásti obsahují užitečnou logiku analýzy. Například a `InputDate` `InputNumber` zpracujte neanalyzovatelné hodnoty tak, že je zaregistrujete jako chyby ověřování. Typy, které mohou přijmout hodnoty null, podporují také hodnotu null cílového pole (například `int?`).

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

Vytvoří jako kaskádovou hodnotu, která sleduje metadata procesu úprav, včetně toho, která pole jsou upravena a aktuální zprávy ověření. [](xref:blazor/components#cascading-values-and-parameters) `EditForm` `EditContext` Poskytuje taky praktické události pro platná a neplatná odeslání (`OnValidSubmit`, `OnInvalidSubmit`). `EditForm` Alternativně můžete `OnSubmit` použít k aktivaci ověřování a hodnot polí pro kontrolu pomocí vlastního ověřovacího kódu.

## <a name="inputtext-based-on-the-input-event"></a>InputText na základě události vstupu

Pomocí komponenty můžete vytvořit vlastní komponentu, která místo `change` události `input` používá událost. `InputText`

Vytvořte komponentu s následujícím kódem a použijte komponentu stejně jako `InputText` je použit:

```cshtml
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a>Podpora ověřování

Komponenta připojuje ověřování pomocí datových poznámek do `EditContext`kaskády. `DataAnnotationsValidator` Povolení podpory pro ověřování pomocí datových poznámek vyžaduje toto explicitní gesto. Chcete-li použít jiný systém ověřování než datové poznámky, nahraďte `DataAnnotationsValidator` vlastní implementaci. ASP.NET Core implementace je k dispozici pro kontrolu v referenčním zdroji: [](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)DataAnnotationsValidator/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Komponenta shrnuje všechny zprávy o ověřování, které jsou podobné [pomocníka značek pro ověřování](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper). `ValidationSummary`

Komponenta zobrazí ověřovací zprávy pro konkrétní pole, které se podobá [pomocníka značek ověřovací zprávy.](xref:mvc/views/working-with-forms#the-validation-message-tag-helper) `ValidationMessage` Zadejte pole pro ověření s `For` atributem a výrazem lambda pojmenování vlastnosti modelu:

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

Komponenty `ValidationMessage` a`ValidationSummary` podporují libovolné atributy. Všechny atributy, které se neshodují s parametrem komponenty, `<div>` jsou `<ul>` přidány do generovaného prvku nebo.

### <a name="validation-of-complex-or-collection-type-properties"></a>Ověřování vlastností komplexního nebo typu kolekce

Atributy ověřování použité pro vlastnosti modelu jsou ověřovány při odeslání formuláře. Nicméně vlastnosti kolekcí nebo komplexních datových typů modelu nejsou ověřeny při odesílání formuláře. Chcete-li v tomto scénáři akceptovat vnořené atributy ověřování, použijte vlastní ověřovací komponentu. Příklad naleznete v tématu [Ukázka ověření Blazor v úložišti GitHub/Samples](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).
