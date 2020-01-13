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
ms.openlocfilehash: a94a433f26e451bbadc73615e502e46d273f05c2
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828136"
---
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a>ASP.NET Core Blazor formuláře a ověřování

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

Formulář je definován pomocí komponenty `EditForm`. Následující formulář ukazuje typické prvky, komponenty a kód Razor:

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

* Formulář ověřuje vstup uživatele v poli `name` pomocí ověřování definovaného v `ExampleModel` typu. Model se vytvoří v bloku `@code` komponenty a je uložený v soukromém poli (`exampleModel`). Pole je přiřazeno atributu `Model` elementu `<EditForm>`.
* Komponenta `DataAnnotationsValidator` připojí podporu ověřování pomocí datových poznámek.
* Komponenta `ValidationSummary` shrnuje ověřovací zprávy.
* `HandleValidSubmit` se spustí, když formulář úspěšně odešle (projde ověřením).

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

Vstupní komponenty poskytují výchozí chování pro ověřování při úpravách a změně jejich třídy CSS tak, aby odrážely stav pole. Některé součásti obsahují užitečnou logiku analýzy. Například `InputDate` a `InputNumber` zpracovávat neanalyzovatelné hodnoty tak, že je zaregistrujete jako chyby ověřování. Typy, které mohou přijmout hodnoty null, podporují také hodnotu null cílového pole (například `int?`).

Následující `Starship` typ definuje logiku ověřování pomocí větší sady vlastností a poznámek k datům než předchozí `ExampleModel`:

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

V předchozím příkladu je `Description` nepovinný, protože nejsou k dispozici žádné anotace dat.

Následující formulář ověřuje vstup uživatele pomocí ověřování definovaného v modelu `Starship`:

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

`EditForm` vytvoří `EditContext` jako [kaskádovou hodnotu](xref:blazor/components#cascading-values-and-parameters) , která sleduje metadata procesu úprav, včetně polí, která byla změněna, a aktuálních zpráv ověření. `EditForm` taky poskytuje praktické události pro platná a neplatná odeslání (`OnValidSubmit`, `OnInvalidSubmit`). Alternativně můžete použít `OnSubmit` k aktivaci ověřování a hodnot polí pro kontrolu pomocí vlastního ověřovacího kódu.

## <a name="inputtext-based-on-the-input-event"></a>InputText na základě události vstupu

Komponentu `InputText` použijte k vytvoření vlastní komponenty, která používá událost `input` namísto události `change`.

Vytvořte komponentu s následujícím kódem a použijte komponentu stejně jako `InputText` se používá:

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a>Podpora ověřování

Součást `DataAnnotationsValidator` připojuje ověřování pomocí datových poznámek k kaskádovým `EditContext`ům. Povolení podpory pro ověřování pomocí datových poznámek vyžaduje toto explicitní gesto. Chcete-li použít jiný systém ověřování než datové poznámky, nahraďte `DataAnnotationsValidator` vlastní implementací. ASP.NET Core implementace je k dispozici pro kontrolu v referenčním zdroji: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazor provádí dva typy ověřování:

* *Ověřování polí* se provede, když se karty uživatele nacházejí v poli. Při ověřování pole přidruží komponenta `DataAnnotationsValidator` všechny hlášené výsledky ověření k poli.
* *Ověřování modelu* se provede, když uživatel formulář odešle. Při ověřování modelu se `DataAnnotationsValidator` komponenta pokusí určit pole na základě názvu člena, který sestavy výsledků ověření. Výsledky ověření, které nejsou přidruženy k jednotlivým členům, jsou přidruženy k modelu, nikoli poli.

### <a name="validation-summary-and-validation-message-components"></a>Komponenty zprávy pro Shrnutí a ověření

Komponenta `ValidationSummary` shrnuje všechny zprávy o ověřování, které se podobají [pomocníka značek Shrnutí ověřování](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Zprávy ověřování výstupu pro určitý model s parametrem `Model`:
  
```razor
<ValidationSummary Model="@starship" />
```

Komponenta `ValidationMessage` zobrazí ověřovací zprávy pro konkrétní pole, které je podobné [Pomocníkovi značky ověřovací zprávy](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Zadejte pole pro ověřování pomocí atributu `For` a lambda výrazu pojmenování vlastnosti modelu:

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

Komponenty `ValidationMessage` a `ValidationSummary` podporují libovolné atributy. Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do generovaného `<div>` nebo `<ul>` elementu.

### <a name="custom-validation-attributes"></a>Vlastní ověřovací atributy

Chcete-li zajistit, aby byl výsledek ověření správně přidružen k poli při použití [vlastního ověřovacího atributu](xref:mvc/models/validation#custom-attributes), předejte <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> ověřovacího kontextu při vytváření <xref:System.ComponentModel.DataAnnotations.ValidationResult>:

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor balíček pro ověření datových poznámek

[Microsoft. AspNetCore.Blazor. Dataanotace. ověřování](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) je balíček, který vyplní mezery v prostředí ověřování pomocí komponenty `DataAnnotationsValidator`. Balíček je momentálně *experimentální*.

### <a name="compareproperty-attribute"></a>[CompareProperty] – atribut

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> nefunguje dobře s komponentou `DataAnnotationsValidator`. [Microsoft. AspNetCore.Blazor. DataAnnotations.](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *experimentální* balíček pro ověření zavádí další atribut ověření `ComparePropertyAttribute`, který tato omezení působí. V Blazor aplikaci `[CompareProperty]` je přímá náhrada pro atribut `[Compare]`. Další informace najdete v tématu [CompareAttribute se ignoruje s OnValidSubmit EditForm (dotnet/AspNetCore #10643)](https://github.com/dotnet/AspNetCore/issues/10643#issuecomment-543909748).

### <a name="nested-models-collection-types-and-complex-types"></a>Vnořené modely, typy kolekcí a komplexní typy

Blazor poskytuje podporu pro ověřování vstupu formuláře pomocí datových poznámek s integrovaným `DataAnnotationsValidator`. `DataAnnotationsValidator` však pouze ověřuje vlastnosti nejvyšší úrovně modelu vázaného na formulář, který není typu kolekce nebo komplexního typu.

Chcete-li ověřit celý graf objektu vázaného modelu, včetně vlastností kolekce a komplexního typu, použijte `ObjectGraphDataAnnotationsValidator` od *experimentálního* [Microsoft. AspNetCore.Blazor. Dataanotace. ověřovací](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) balíček:

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Přidávejte do vlastností modelu poznámky pomocí `[ValidateComplexType]`. V následujících třídách modelu obsahuje Třída `ShipDescription` další datové poznámky, které lze ověřit, je-li model svázán s formulářem:

*Starship.cs*:

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

*ShipDescription.cs*:

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

### <a name="validation-of-complex-or-collection-type-properties"></a>Ověřování vlastností komplexního nebo typu kolekce

Atributy ověřování použité pro vlastnosti modelu jsou ověřovány při odeslání formuláře. Nicméně vlastnosti kolekcí nebo komplexních datových typů modelu nejsou ověřeny při odesílání formuláře komponentou `DataAnnotationsValidator`. Chcete-li v tomto scénáři akceptovat vnořené atributy ověřování, použijte vlastní ověřovací komponentu. Příklad naleznete v části [Ukázka ověřeníBlazor (ASPNET/Samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).

::: moniker-end
