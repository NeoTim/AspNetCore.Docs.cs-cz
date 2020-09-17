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
ms.openlocfilehash: 63cda3348073418e95dd9a0cbdb62e0b5f606383
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2020
ms.locfileid: "90721798"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a>ASP.NET Core Blazor formuláře a ověřování

Od [Daniel Skořepa](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/)a [Luke Latham](https://github.com/guardrex)

Formuláře a ověřování jsou podporovány v Blazor použití [datových poznámek](xref:mvc/models/validation).

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

Formulář je definován pomocí <xref:Microsoft.AspNetCore.Components.Forms.EditForm> komponenty. Následující formulář ukazuje typické prvky, komponenty a Razor kód:

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

V předchozím příkladu:

* Formulář ověřuje vstup uživatele v `name` poli pomocí ověřování definovaného v `ExampleModel` typu. Model se vytvoří v bloku komponenty a je `@code` uložený v soukromém poli ( `exampleModel` ). Pole je přiřazeno `Model` atributu `<EditForm>` elementu.
* <xref:Microsoft.AspNetCore.Components.Forms.InputText> `@bind-Value` Vazby komponenty:
  * Vlastnost modelu ( `exampleModel.Name` ) na <xref:Microsoft.AspNetCore.Components.Forms.InputText> `Value` vlastnost součásti. Další informace o vazbách vlastností naleznete v tématu <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters> .
  * Delegát události změny pro <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` vlastnost komponenty.
* <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [Komponenta validátoru](#validator-components) připojí podporu ověřování pomocí datových poznámek.
* <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Komponenta shrnuje ověřovací zprávy.
* `HandleValidSubmit` aktivuje se, když formulář úspěšně odešle (projde ověřením).

## <a name="built-in-forms-components"></a>Předdefinované součásti formulářů

K dispozici je sada předdefinovaných komponent pro příjem a ověření vstupu uživatele. Vstupy jsou ověřovány při jejich změně a při odeslání formuláře. Dostupné vstupní komponenty jsou uvedené v následující tabulce.

::: moniker range=">= aspnetcore-5.0"

| Vstupní komponenta | Vykresleno jako&hellip; |
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

| Vstupní komponenta | Vykresleno jako&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

> [!NOTE]
> `InputRadio`Komponenty a `InputRadioGroup` jsou k dispozici v ASP.NET Core 5,0 nebo novějším. Další informace získáte výběrem verze 5,0 nebo novější v tomto článku.

::: moniker-end

Všechny vstupní komponenty, včetně <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , podporují libovolné atributy. Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do vykresleného prvku HTML.

Vstupní komponenty poskytují výchozí chování při ověřování při změně pole, včetně aktualizace třídy CSS pole tak, aby odrážely stav pole. Některé součásti obsahují užitečnou logiku analýzy. Například <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> a <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> zpracujte neanalyzovatelné hodnoty tak, že zaregistrujete neanalyzovatelné hodnoty jako chyby ověřování. Typy, které mohou přijmout hodnoty null, podporují také hodnotu null cílového pole (například `int?` ).

Následující `Starship` typ definuje logiku ověřování pomocí větší sady vlastností a poznámek k datům než dříve `ExampleModel` :

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

V předchozím příkladu `Description` je volitelná, protože nejsou k dispozici žádné anotace dat.

Následující formulář ověří uživatelský vstup pomocí ověřování definovaného v `Starship` modelu:

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

<xref:Microsoft.AspNetCore.Components.Forms.EditForm>Vytvoří <xref:Microsoft.AspNetCore.Components.Forms.EditContext> jako [kaskádovou hodnotu](xref:blazor/components/cascading-values-and-parameters) , která sleduje metadata procesu úprav, včetně toho, která pole jsou upravena a aktuální zprávy ověření.

Přiřaďte **buď** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **nebo** <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> k <xref:Microsoft.AspNetCore.Components.Forms.EditForm> . Přiřazení obou není podporované a generuje **chybu za běhu**.

<xref:Microsoft.AspNetCore.Components.Forms.EditForm>Poskytuje užitečné události pro platné a neplatné odeslání formuláře:

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

Slouží <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> k použití vlastního kódu pro aktivaci ověřování a hodnot pole check.

V následujícím příkladu:

* `HandleSubmit`Metoda se spustí, když **`Submit`** je vybráno tlačítko.
* Formulář se ověří voláním metody <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> .
* Další kód se spustí v závislosti na výsledku ověřování. Umístěte obchodní logiku do metody přiřazené k <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> .

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
> Rozhraní API architektury neexistuje, aby se vymazaly ověřovací zprávy přímo z <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . Proto obecně Nedoporučujeme přidávat ověřovací zprávy do nového <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> formuláře ve formuláři. Chcete-li spravovat ověřovací zprávy, použijte [komponentu validátoru](#validator-components) s [ověřovacím kódem obchodní logiky](#business-logic-validation), jak je popsáno v tomto článku.

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a>Podpora zobrazovaného názvu

*Tato část se týká ASP.NET Core v rozhraní .NET 5 Release Candidate 1 (RC1) nebo novějším.*

Následující integrované součásti podporují zobrazované názvy s `DisplayName` parametrem:

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

V následujícím `InputDate` příkladu komponenty:

* Zobrazované jméno ( `DisplayName` ) je nastavené na `birthday` .
* Komponenta je svázána s `BirthDate` vlastností jako `DateTime` typ.

```razor
<InputDate @bind-Value="@BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

Pokud uživatel neposkytne hodnotu data, zobrazí se chyba ověření jako:

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a>Komponenty validátoru

Komponenty validátoru podporují ověřování formuláře, a to tak, že spravují <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> pro formulář <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .

BlazorRozhraní poskytuje <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponentu pro připojení podpory ověřování k formulářům na základě [ověřovacích atributů (datové poznámky)](xref:mvc/models/validation#validation-attributes). Vytvořte vlastní komponenty validátoru pro zpracování ověřovacích zpráv pro různé formuláře na stejné stránce nebo ve stejném formuláři v různých krocích zpracování formuláře, například ověřování na straně klienta následovaný ověřováním na straně serveru. Příklad komponenty validátoru zobrazený v této části se `CustomValidator` používá v následujících částech tohoto článku:

* [Ověřování obchodní logiky](#business-logic-validation)
* [Ověření serveru](#server-validation)

> [!NOTE]
> Vlastní atributy ověřování poznámky k datům se dají použít místo vlastních komponent validátoru v mnoha případech. Vlastní atributy použité pro model formuláře se aktivují s použitím <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponenty. Při použití s ověřováním na straně serveru musí být všechny vlastní atributy použité pro model spustitelné na serveru. Další informace naleznete v tématu <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

Vytvořit komponentu validátoru z <xref:Microsoft.AspNetCore.Components.ComponentBase> :

* Formulář <xref:Microsoft.AspNetCore.Components.Forms.EditContext> je [kaskádový parametr](xref:blazor/components/cascading-values-and-parameters) součásti.
* Při inicializaci komponenty validátoru se vytvoří nový, <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> který udržuje aktuální seznam chyb formuláře.
* Úložiště zpráv přijímá chyby, když kód vývojáře v součásti formuláře volá `DisplayErrors` metodu. Chyby jsou předány `DisplayErrors` metodě v [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) . Ve slovníku je klíč názvem pole formuláře, které obsahuje jednu nebo více chyb. Hodnota je seznam chyb.
* Zprávy jsou vymazány, pokud došlo k některé z následujících:
  * Ověřování je požadováno <xref:Microsoft.AspNetCore.Components.Forms.EditContext> při <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> vyvolání události. Všechny chyby jsou vymazány.
  * Při vyvolání události se změní pole ve formuláři <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> . Vymažou se jenom chyby pro toto pole.
  * `ClearErrors`Metoda je volána kódem pro vývojáře. Všechny chyby jsou vymazány.

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

## <a name="business-logic-validation"></a>Ověřování obchodní logiky

Ověřování obchodní logiky lze provést pomocí [komponenty validátoru](#validator-components) , která přijímá chyby formuláře ve slovníku.

V následujícím příkladu:

* `CustomValidator`Součást z části [komponenty validátoru](#validator-components) tohoto článku se používá.
* `Description`Pokud uživatel vybere `Defense` klasifikaci expedice (), vyžaduje ověření hodnotu pro popis příjemce () `Classification` .

Když jsou ověřovací zprávy nastaveny v komponentě, přidají se do validátoru <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> a zobrazí se v <xref:Microsoft.AspNetCore.Components.Forms.EditForm> :

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
> Jako alternativu k používání [ověřovacích komponent](#validator-components)lze použít ověřovací atributy datových poznámek. Vlastní atributy použité pro model formuláře se aktivují s použitím <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponenty. Při použití s ověřováním na straně serveru musí být atributy spustitelné na serveru. Další informace naleznete v tématu <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

## <a name="server-validation"></a>Ověření serveru

Ověření serveru se dá provést pomocí [komponenty validátoru](#validator-components)serveru:

* Zpracujte ověřování na straně klienta ve formuláři s <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponentou.
* Když formulář projde ověřováním na straně klienta ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> je volán), odešlete <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> rozhraní API back-end serveru pro zpracování formuláře.
* Ověřování modelu procesu na serveru.
* Rozhraní API serveru zahrnuje integrované ověřování poznámek k datům architektury a vlastní logiku ověřování poskytovanou vývojářem. Pokud ověření projde na serveru, zpracujte formulář a odešlete zpět stavový kód úspěchu (*200 – OK*). Pokud se ověření nezdaří, vrátí stavový kód chyby (*400 – Chybný požadavek*) a chyby ověření pole.
* Buď zakažte formulář při úspěchu nebo zobrazte chyby.

Následující příklad je založen na:

* Hostované Blazor řešení vytvořené [ Blazor šablonou hostovaného projektu](xref:blazor/hosting-models#blazor-webassembly). Příklad lze použít se všemi zabezpečenými hostovanými Blazor řešeními popsanými v [dokumentaci zabezpečení a Identity dokumentace](xref:blazor/security/webassembly/index#implementation-guidance).
* Příklad formuláře *databáze Starfleet Starship* v předchozích [vestavěných součástech Forms](#built-in-forms-components) .
* Blazor <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Komponenta rozhraní.
* `CustomValidator`Komponenta zobrazená v části [komponenty validátoru](#validator-components) .

V následujícím příkladu rozhraní API serveru ověřuje, zda je zadána hodnota pro popis příjemce ( `Description` ), pokud uživatel vybere `Defense` klasifikaci expedice ( `Classification` ).

Model umístěte `Starship` do `Shared` projektu řešení tak, aby aplikace klienta i serveru mohly model používat. Vzhledem k tomu, že model vyžaduje datové poznámky, přidejte odkaz na balíček pro [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) do `Shared` souboru projektu projektu:

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

Pokud chcete zjistit nejnovější verzi balíčku, který není ve verzi Preview, podívejte se na **historii verzí** balíčku na adrese [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).

V projektu rozhraní API serveru přidejte kontroler pro zpracování požadavků na ověření Starship ( `Controllers/StarshipValidation.cs` ) a vraťte se k neúspěšným ověřovacím zprávám:

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

Pokud dojde k chybě ověřování vazby modelu na serveru, [`ApiController`](xref:web-api/index) <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> vrátí () obvykle [výchozí chybnou odpověď na požadavek](xref:web-api/index#default-badrequest-response) s hodnotou <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> . Odpověď obsahuje více dat než pouze chyby ověřování, jak je znázorněno v následujícím příkladu, pokud nejsou odeslána všechna pole formuláře *databáze Starfleet Starship* a formulář se nezdařil. ověření se nezdařilo:

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

Pokud rozhraní API serveru vrátí předchozí výchozí odpověď JSON, může klient analyzovat odpověď pro získání podřízených objektů `errors` uzlu. Není ale vhodné analyzovat soubor. Při analýze JSON se vyžaduje další kód po volání <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> , aby se vytvořily [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) chyby pro zpracování chyb ověřování pomocí formulářů. V ideálním případě by mělo serverové rozhraní API vracet pouze chyby ověřování:

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

Chcete-li změnit odpověď rozhraní API serveru tak, aby vracela pouze chyby ověřování, změňte delegáta, který je vyvolán na akcích, které jsou v aplikaci opatřeny poznámkami <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> `Startup.ConfigureServices` . Pro koncový bod rozhraní API ( `/StarshipValidation` ), vraťte a <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> s <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> . Pro jakékoli jiné koncové body rozhraní API zachovejte výchozí chování vrácením výsledku objektu s novým <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> :

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

Další informace naleznete v tématu <xref:web-api/handle-errors#validation-failure-error-response>.

V projektu klienta přidejte komponentu validátoru zobrazenou v části [komponenty validátoru](#validator-components) .

V projektu klienta se aktualizuje formulář *databáze Starfleet Starship* , aby se zobrazily chyby ověření serveru s použitím `CustomValidator` komponenty. Když rozhraní API serveru vrátí ověřovací zprávy, přidají se do `CustomValidator` komponenty <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> . Chyby jsou k dispozici ve formuláři <xref:Microsoft.AspNetCore.Components.Forms.EditContext> pro zobrazení pomocí formuláře <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> :

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
> Jako alternativu k [součástem ověřování](#validator-components)lze použít atributy ověření poznámky k datům. Vlastní atributy použité pro model formuláře se aktivují s použitím <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponenty. Při použití s ověřováním na straně serveru musí být atributy spustitelné na serveru. Další informace naleznete v tématu <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

> [!NOTE]
> Přístup k ověřování na straně serveru v této části je vhodný pro jakékoli Blazor WebAssembly Příklady hostovaných řešení v této sadě dokumentace:
>
> * [Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [Identity WebServer](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a>InputText na základě události vstupu

Pomocí <xref:Microsoft.AspNetCore.Components.Forms.InputText> komponenty můžete vytvořit vlastní komponentu, která `input` místo události používá událost `change` .

V následujícím příkladu `CustomInputText` Komponenta dědí `InputText` komponentu rozhraní a nastaví vazbu na událost ( <xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A> ) na `oninput` událost.

`Shared/CustomInputText.razor`:

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

`CustomInputText`Komponentu lze použít kdekoli <xref:Microsoft.AspNetCore.Components.Forms.InputText> :

`Pages/TestForm.razor`:

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

## <a name="radio-buttons"></a>Přepínače

::: moniker range=">= aspnetcore-5.0"

Pomocí `InputRadio` komponent s `InputRadioGroup` komponentou vytvořte skupinu přepínačů. V následujícím příkladu jsou přidány vlastnosti do `Starship` modelu popsaného v části [předdefinované součásti formulářů](#built-in-forms-components) :

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

`enums`Do aplikace přidejte následující. Vytvořte nový soubor, který bude obsahovat `enums` nebo přidejte do `enums` `Starship.cs` souboru. Zpřístupněte `enums` přístup k `Starship` modelu a formuláři *databáze Starfleet Starship* :

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, Virgin, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

Aktualizujte formulář *databáze Starfleet Starship* , který je popsaný v části [předdefinované komponenty formulářů](#built-in-forms-components) . Přidejte součásti, které chcete vytvořit:

* Skupina přepínacích tlačítek pro výrobce lodi.
* Vnořená skupina přepínacích tlačítek pro expedici barva a modul.

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
> Pokud `Name` je tento parametr vynechán, `InputRadio` komponenty jsou seskupeny podle jejich posledního předchůdce.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Při práci s přepínači ve formuláři je datová vazba zpracovávána jinak než jiné prvky, protože přepínače jsou vyhodnocovány jako skupina. Hodnota každého přepínacího tlačítka je pevná, ale hodnota skupiny přepínačů je hodnota vybraného přepínacího tlačítka. Následující příklad ukazuje, jak:

* Zpracuje datovou vazbu pro skupinu přepínačů.
* Podpora ověřování pomocí vlastní `InputRadio` součásti.

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

V následujícím <xref:Microsoft.AspNetCore.Components.Forms.EditForm> příkladu se `InputRadio` k získání a ověření hodnocení od uživatele používá předchozí komponenta:

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

## <a name="binding-select-element-options-to-c-object-null-values"></a>Vazba `<select>` možností elementu na hodnoty objektu C# `null`

Neexistuje žádný rozumné způsob, jak vyjádřit `<select>` hodnotu možnosti prvku jako hodnotu objektu C# `null` , protože:

* Atributy HTML nemohou mít `null` hodnoty. Nejbližší ekvivalent k `null` v jazyce HTML je absence `value` atributu HTML z `<option>` elementu.
* Při výběru možnosti bez `<option>` `value` atributu prohlížeč považuje hodnotu za *textový obsah* `<option>` elementu.

BlazorRozhraní se nepokouší potlačit výchozí chování, protože by zahrnovalo:

* Vytvoření řetězce alternativních řešení zvláštních případů v rozhraní.
* Zásadní změny v aktuálním chování architektury.

::: moniker range=">= aspnetcore-5.0"

Plausible `null` ekvivalent v HTML je *prázdný řetězec* `value` . BlazorArchitektura zpracovává `null` prázdné převody řetězce pro oboustrannou vazbu na `<select>` hodnotu.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Blazor `null` Při pokusu o obousměrnou vazbu na hodnotu se rozhraní nezpracovává automaticky k prázdným převodům řetězců `<select>` . Další informace naleznete v tématu [Oprava vazby `<select>` na hodnotu null (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).

::: moniker-end

## <a name="validation-support"></a>Podpora ověřování

<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Komponenta připojuje ověřování pomocí datových poznámek do kaskády <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . Povolení podpory pro ověřování pomocí datových poznámek vyžaduje toto explicitní gesto. Chcete-li použít jiný systém ověřování než datové poznámky, nahraďte <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> vlastní implementaci. Implementace ASP.NET Core je k dispozici pro kontrolu v referenčním zdroji: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) . Předchozí odkazy na zdroj referencí poskytují kód z `master` větve úložiště, která představuje aktuální vývoj jednotky produktu pro příští vydání ASP.NET Core. Pokud chcete vybrat větev pro jinou verzi, použijte selektor větve GitHubu (například `release/3.1` ).

Blazor provádí dva typy ověřování:

* *Ověřování polí* se provede, když se karty uživatele nacházejí v poli. Při ověřování pole <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> přidruží součást všechny hlášené výsledky ověření k poli.
* *Ověřování modelu* se provede, když uživatel formulář odešle. Při ověřování modelu se <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Komponenta pokusí určit pole na základě názvu člena, který sestavy výsledků ověření. Výsledky ověření, které nejsou přidruženy k jednotlivým členům, jsou přidruženy k modelu, nikoli poli.

### <a name="validation-summary-and-validation-message-components"></a>Komponenty zprávy pro Shrnutí a ověření

<xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Komponenta shrnuje všechny zprávy o ověřování, které se podobají [pomocníka značek pro Shrnutí ověření](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Zprávy ověřování výstupu pro konkrétní model s `Model` parametrem:
  
```razor
<ValidationSummary Model="@starship" />
```

<xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Komponenta zobrazí ověřovací zprávy pro konkrétní pole, které se podobá [pomocníka značek ověřovací zprávy](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Zadejte pole pro ověření s `For` atributem a výrazem lambda pojmenování vlastnosti modelu:

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Komponenty a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> podporují libovolné atributy. Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do generovaného `<div>` `<ul>` prvku nebo.

Řízení stylu ověřovacích zpráv v šabloně stylů aplikace ( `wwwroot/css/app.css` nebo `wwwroot/css/site.css` ). Výchozí `validation-message` třída nastaví barvu textu pro zprávy ověření na červenou:

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a>Vlastní ověřovací atributy

Chcete-li zajistit, aby byl výsledek ověření správně přidružen k poli při použití [vlastního ověřovacího atributu](xref:mvc/models/validation#custom-attributes), předejte kontext ověření <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> při vytváření <xref:System.ComponentModel.DataAnnotations.ValidationResult> :

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
> <xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> je `null`. Vložené služby pro ověřování v metodě se `IsValid` nepodporují.

::: moniker range=">= aspnetcore-5.0"

## <a name="custom-validation-class-attributes"></a>Vlastní atributy třídy ověřování

Vlastní názvy tříd ověřování jsou užitečné při integraci s rozhraními CSS, jako je například [bootstrap](https://getbootstrap.com/). Chcete-li zadat vlastní názvy tříd ověřování, vytvořte třídu odvozenou z `FieldCssClassProvider` a nastavte třídu v <xref:Microsoft.AspNetCore.Components.Forms.EditContext> instanci:

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

### <a name="no-locblazor-data-annotations-validation-package"></a>Blazor balíček pro ověření datových poznámek

[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)Je balíček, který vyplní prodlevy při ověřování pomocí <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponenty. Balíček je momentálně *experimentální*.

> [!NOTE]
> [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)Balíček má nejnovější verzi *Release Candidate* na adrese [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). V tuto chvíli pokračujte v používání *experimentálního* balíčku Release Candidate. Sestavení balíčku může být přesunuto do rozhraní nebo modulu runtime v budoucí verzi. Podívejte se na [úložiště GitHubu oznámení](https://github.com/aspnet/Announcements), [úložiště GitHub/aspnetcore](https://github.com/dotnet/aspnetcore), nebo v části tohoto tématu, kde najdete další aktualizace.

### <a name="compareproperty-attribute"></a>[CompareProperty] – atribut

<xref:System.ComponentModel.DataAnnotations.CompareAttribute>Nefunguje dobře s <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponentou, protože nepřiřazuje výsledek ověření k určitému členu. To může vést k nekonzistentnímu chování mezi ověřováním na úrovni polí a při ověřování celého modelu při odeslání. [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *Experimentální* balíček zavádí další ověřovací atribut, který tato `ComparePropertyAttribute` omezení funguje. V Blazor aplikaci `[CompareProperty]` je přímá náhrada za [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) atribut.

### <a name="nested-models-collection-types-and-complex-types"></a>Vnořené modely, typy kolekcí a komplexní typy

Blazor poskytuje podporu pro ověřování vstupu formuláře pomocí datových poznámek s integrovaným <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> . <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Pouze ověří vlastnosti nejvyšší úrovně modelu svázaného s formulářem, který není vlastností kolekce nebo komplexního typu.

Chcete-li ověřit celý graf objektu vázaného modelu, včetně vlastností kolekce a komplexního typu, použijte příkaz `ObjectGraphDataAnnotationsValidator` poskytnutý *experimentálním* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) balíčkem:

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Přidávejte do vlastností modelu poznámky `[ValidateComplexType]` . V následujících třídách modelu `ShipDescription` obsahuje Třída další datové poznámky pro ověření, když je model svázán s formulářem:

`Starship.cs`:

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

`ShipDescription.cs`:

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

### <a name="enable-the-submit-button-based-on-form-validation"></a>Povolit tlačítko Odeslat na základě ověření formuláře

Povolení a zakázání tlačítka Odeslat na základě ověření formuláře:

* <xref:Microsoft.AspNetCore.Components.Forms.EditContext>K přiřazení modelu při inicializaci komponenty použijte formulář.
* Ověřte formulář v <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> zpětném volání kontextu a povolte a zakažte tlačítko Odeslat.
* Odpojte obslužnou rutinu události v `Dispose` metodě. Další informace naleznete v tématu <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

> [!NOTE]
> Při použití <xref:Microsoft.AspNetCore.Components.Forms.EditContext> , nepřiřazujte také <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> k <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .

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

V předchozím příkladu nastavte `formInvalid` na `false` if:

* Formulář je předem načten s platnými výchozími hodnotami.
* Chcete povolit tlačítko Odeslat, když se formulář načte.

Vedlejším účinkem předcházejícího přístupu je, že <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> Komponenta je naplněná neplatnými poli poté, co uživatel komunikuje s jakýmkoli jedním polem. Tento scénář je možné vyřešit jedním z následujících způsobů:

* Nepoužívejte <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> komponentu na formuláři.
* Zpřístupní <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> komponentu, když je vybráno tlačítko Odeslat (například v `HandleValidSubmit` metodě).

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

## <a name="troubleshoot"></a>Řešení potíží

> InvalidOperationException: EditForm vyžaduje parametr modelu nebo parametr EditContext, ale ne obojí.

Potvrďte, že <xref:Microsoft.AspNetCore.Components.Forms.EditForm> má <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **nebo** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . U stejného formuláře nepoužívejte obojí.

Při přiřazování <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> do formuláře potvrďte, že je vytvořen typ modelu, jak ukazuje následující příklad:

```csharp
private ExampleModel exampleModel = new ExampleModel();
```

## <a name="additional-resources"></a>Další materiály

* <xref:blazor/file-uploads>
