---
title: ASP.NET Core Blazor formulářů a ověřování
author: guardrex
description: Naučte se používat v Blazor formuláře a scénáře ověření pole.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/forms-validation
ms.openlocfilehash: a47ff2ea1fbde8a5d2c6e8bcd73744812767de77
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538460"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET Core Blazor formulářů a ověřování

Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

Formuláře a ověřování jsou podporovány v Blazor pomocí [anotacemi dat](xref:mvc/models/validation).

> [!NOTE]
> Formuláře a scénáře ověřování se pravděpodobně změní v každé verzi preview.

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

Formulář je definován pomocí `EditForm` komponenty. Následující formulář demonstruje obvyklé prvky, komponenty a kód Razor:

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

* Ověří uživatelský vstup ve formuláři `name` pole pomocí ověřování definované v `ExampleModel` typu. Model je vytvořen v součásti `@code` blokovat a uchovávat v soukromé pole (`exampleModel`). Poli je přiřazena k `Model` atribut `<EditForm>` elementu.
* `DataAnnotationsValidator` Komponenty připojí podporu ověřování pomocí datových poznámek.
* `ValidationSummary` Komponenta obsahuje souhrn ověřovacích zpráv.
* `HandleValidSubmit` se aktivuje v případě formulář úspěšného odeslání (předá ověření).

Sada předdefinovaných vstupní komponent jsou k dispozici pro příjem a ověření vstupu uživatele. Vstupy se ověřují, když se změní, a když se odešle formulář. V následující tabulce jsou uvedeny dostupné vstupní součásti.

| Vstupní komponenty | Se vykresluje jako&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

Všechny vstupní komponenty, včetně `EditForm`, podporují libovolnými atributy. Všechny atributy, které neodpovídá součást parametru se přidá do vykreslovaného elementu HTML.

Vstupní komponenty poskytují výchozí chování pro ověřování na Upravit a změna jejich třídu šablony stylů CSS tak, aby odrážely stavu pole. Některé součásti zahrnují užitečné analýzy logiku. Například `InputDate` a `InputNumber` elegantně zpracovat Neanalyzovatelný hodnoty tak, že je zaregistrujete jako chyby ověření. Typy, které může přijmout hodnoty null také podporují možnost použití hodnoty Null z cílového pole (například `int?`).

Následující `Starship` typ definuje logiku ověřování pomocí větší sadu vlastností a dat poznámky než dříve `ExampleModel`:

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

V předchozím příkladu `Description` je volitelný, protože nejsou žádné datové poznámky.

Následující formulář ověřuje vstup uživatele s použitím ověřování definované v `Starship` modelu:

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
        <InputTextArea Id="description" @bind-Value="starship.Description" />
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
        <InputDate Id="productionDate" @bind-Value="starship.ProductionDate" />
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

`EditForm` Vytvoří `EditContext` jako [kaskádové hodnotu](xref:blazor/components#cascading-values-and-parameters) metadata o procesu úprav, včetně polí, která se změnily a aktuální ověřovacích zpráv, který sleduje. `EditForm` Také poskytuje pohodlné události pro platné a neplatné odešle (`OnValidSubmit`, `OnInvalidSubmit`). Můžete taky použít `OnSubmit` aktivovat ověřování a zkontrolovat hodnoty pole s vlastního ověřovacího kódu.

`DataAnnotationsValidator` Komponenty připojí podporu ověřování pomocí datových poznámek k kaskádovým příkazem `EditContext`. Povolení podpory pro ověření pomocí anotací dat při aktuálně vyžaduje tento explicitní gesta, ale můžeme uvažujete, takže jde o výchozí chování, které můžete přepsat. Abyste mohli použít jiné ověření systému než datových poznámek, nahraďte `DataAnnotationsValidator` s vlastní implementaci. Implementace ASP.NET Core je k dispozici pro kontrolu v nástroji zdroj odkazu: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs). *Implementace ASP.NET Core je v souladu s rychlé aktualizace během období verze preview.*

`ValidationSummary` Komponenta obsahuje souhrn všech ověřovacích zpráv, které je podobné [pomocná rutina značek v souhrnu ověření](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).

`ValidationMessage` Komponenty zobrazuje zprávy o ověřování pro konkrétní pole, které se podobá [pomocné rutiny značky zpráva ověření](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Zadejte pole, které pro ověření pomocí `For` atribut a výraz lambda pojmenování vlastnost modelu:

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

`ValidationMessage` a `ValidationSummary` komponenty podporují libovolnými atributy. Všechny atributy, které neodpovídá součást parametru se přidá do vytvořeného `<div>` nebo `<ul>` elementu.
