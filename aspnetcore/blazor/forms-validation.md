---
<span data-ttu-id="91e34-101">title: ' ASP.NET Core Blazor Forms a Validation ' Autor: Description: ' Naučte se používat scénáře ověřování formulářů a polí v Blazor . '</span><span class="sxs-lookup"><span data-stu-id="91e34-101">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="91e34-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="91e34-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="91e34-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="91e34-103">'Blazor'</span></span>
- <span data-ttu-id="91e34-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="91e34-104">'Identity'</span></span>
- <span data-ttu-id="91e34-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="91e34-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="91e34-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="91e34-106">'Razor'</span></span>
- <span data-ttu-id="91e34-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="91e34-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="91e34-108">ASP.NET Core Blazor formuláře a ověřování</span><span class="sxs-lookup"><span data-stu-id="91e34-108">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="91e34-109">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="91e34-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="91e34-110">Formuláře a ověřování jsou podporovány v Blazor použití [datových poznámek](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="91e34-110">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="91e34-111">Následující `ExampleModel` typ definuje logiku ověřování pomocí datových poznámek:</span><span class="sxs-lookup"><span data-stu-id="91e34-111">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="91e34-112">Formulář je definován pomocí <xref:Microsoft.AspNetCore.Components.Forms.EditForm> komponenty.</span><span class="sxs-lookup"><span data-stu-id="91e34-112">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="91e34-113">Následující formulář ukazuje typické prvky, komponenty a Razor kód:</span><span class="sxs-lookup"><span data-stu-id="91e34-113">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
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

<span data-ttu-id="91e34-114">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="91e34-114">In the preceding example:</span></span>

* <span data-ttu-id="91e34-115">Formulář ověřuje vstup uživatele v `name` poli pomocí ověřování definovaného v `ExampleModel` typu.</span><span class="sxs-lookup"><span data-stu-id="91e34-115">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="91e34-116">Model se vytvoří v bloku komponenty a je `@code` uložený v soukromém poli ( `exampleModel` ).</span><span class="sxs-lookup"><span data-stu-id="91e34-116">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="91e34-117">Pole je přiřazeno `Model` atributu `<EditForm>` elementu.</span><span class="sxs-lookup"><span data-stu-id="91e34-117">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="91e34-118"><xref:Microsoft.AspNetCore.Components.Forms.InputText> `@bind-Value` Vazby komponenty:</span><span class="sxs-lookup"><span data-stu-id="91e34-118">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="91e34-119">Vlastnost modelu ( `exampleModel.Name` ) na <xref:Microsoft.AspNetCore.Components.Forms.InputText> `Value` vlastnost součásti.</span><span class="sxs-lookup"><span data-stu-id="91e34-119">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="91e34-120">Další informace o vazbách vlastností naleznete v tématu <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters> .</span><span class="sxs-lookup"><span data-stu-id="91e34-120">For more information on property binding, see <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="91e34-121">Delegát události změny pro <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` vlastnost komponenty.</span><span class="sxs-lookup"><span data-stu-id="91e34-121">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="91e34-122"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Komponenta připojuje ověřování pomocí datových poznámek.</span><span class="sxs-lookup"><span data-stu-id="91e34-122">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="91e34-123"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Komponenta shrnuje ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="91e34-123">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="91e34-124">`HandleValidSubmit`aktivuje se, když formulář úspěšně odešle (projde ověřením).</span><span class="sxs-lookup"><span data-stu-id="91e34-124">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="91e34-125">K dispozici je sada předdefinovaných vstupních komponent pro příjem a ověření vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="91e34-125">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="91e34-126">Vstupy jsou ověřovány při jejich změně a při odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="91e34-126">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="91e34-127">Dostupné vstupní komponenty jsou uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="91e34-127">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="91e34-128">Vstupní komponenta</span><span class="sxs-lookup"><span data-stu-id="91e34-128">Input component</span></span> | <span data-ttu-id="91e34-129">Vykresleno jako&hellip;</span><span class="sxs-lookup"><span data-stu-id="91e34-129">Rendered as&hellip;</span></span> |
| ---
<span data-ttu-id="91e34-130">title: ' ASP.NET Core Blazor Forms a Validation ' Autor: Description: ' Naučte se používat scénáře ověřování formulářů a polí v Blazor . '</span><span class="sxs-lookup"><span data-stu-id="91e34-130">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="91e34-131">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="91e34-131">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="91e34-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="91e34-132">'Blazor'</span></span>
- <span data-ttu-id="91e34-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="91e34-133">'Identity'</span></span>
- <span data-ttu-id="91e34-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="91e34-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="91e34-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="91e34-135">'Razor'</span></span>
- <span data-ttu-id="91e34-136">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="91e34-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="91e34-137">title: ' ASP.NET Core Blazor Forms a Validation ' Autor: Description: ' Naučte se používat scénáře ověřování formulářů a polí v Blazor . '</span><span class="sxs-lookup"><span data-stu-id="91e34-137">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="91e34-138">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="91e34-138">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="91e34-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="91e34-139">'Blazor'</span></span>
- <span data-ttu-id="91e34-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="91e34-140">'Identity'</span></span>
- <span data-ttu-id="91e34-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="91e34-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="91e34-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="91e34-142">'Razor'</span></span>
- <span data-ttu-id="91e34-143">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="91e34-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="91e34-144">title: ' ASP.NET Core Blazor Forms a Validation ' Autor: Description: ' Naučte se používat scénáře ověřování formulářů a polí v Blazor . '</span><span class="sxs-lookup"><span data-stu-id="91e34-144">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="91e34-145">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="91e34-145">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="91e34-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="91e34-146">'Blazor'</span></span>
- <span data-ttu-id="91e34-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="91e34-147">'Identity'</span></span>
- <span data-ttu-id="91e34-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="91e34-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="91e34-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="91e34-149">'Razor'</span></span>
- <span data-ttu-id="91e34-150">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="91e34-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="91e34-151">title: ' ASP.NET Core Blazor Forms a Validation ' Autor: Description: ' Naučte se používat scénáře ověřování formulářů a polí v Blazor . '</span><span class="sxs-lookup"><span data-stu-id="91e34-151">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="91e34-152">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="91e34-152">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="91e34-153">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="91e34-153">'Blazor'</span></span>
- <span data-ttu-id="91e34-154">'Identity'</span><span class="sxs-lookup"><span data-stu-id="91e34-154">'Identity'</span></span>
- <span data-ttu-id="91e34-155">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="91e34-155">'Let's Encrypt'</span></span>
- <span data-ttu-id="91e34-156">'Razor'</span><span class="sxs-lookup"><span data-stu-id="91e34-156">'Razor'</span></span>
- <span data-ttu-id="91e34-157">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="91e34-157">'SignalR' uid:</span></span> 

-
<span data-ttu-id="91e34-158">title: ' ASP.NET Core Blazor Forms a Validation ' Autor: Description: ' Naučte se používat scénáře ověřování formulářů a polí v Blazor . '</span><span class="sxs-lookup"><span data-stu-id="91e34-158">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="91e34-159">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="91e34-159">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="91e34-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="91e34-160">'Blazor'</span></span>
- <span data-ttu-id="91e34-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="91e34-161">'Identity'</span></span>
- <span data-ttu-id="91e34-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="91e34-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="91e34-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="91e34-163">'Razor'</span></span>
- <span data-ttu-id="91e34-164">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="91e34-164">'SignalR' uid:</span></span> 

<span data-ttu-id="91e34-165">-------- | ---Název: ' ASP.NET Core Blazor Forms a Validation ' Autor: Description: ' Naučte se používat scénáře ověřování formulářů a polí v Blazor . '</span><span class="sxs-lookup"><span data-stu-id="91e34-165">-------- | --- title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="91e34-166">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="91e34-166">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="91e34-167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="91e34-167">'Blazor'</span></span>
- <span data-ttu-id="91e34-168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="91e34-168">'Identity'</span></span>
- <span data-ttu-id="91e34-169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="91e34-169">'Let's Encrypt'</span></span>
- <span data-ttu-id="91e34-170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="91e34-170">'Razor'</span></span>
- <span data-ttu-id="91e34-171">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="91e34-171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="91e34-172">title: ' ASP.NET Core Blazor Forms a Validation ' Autor: Description: ' Naučte se používat scénáře ověřování formulářů a polí v Blazor . '</span><span class="sxs-lookup"><span data-stu-id="91e34-172">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="91e34-173">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="91e34-173">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="91e34-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="91e34-174">'Blazor'</span></span>
- <span data-ttu-id="91e34-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="91e34-175">'Identity'</span></span>
- <span data-ttu-id="91e34-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="91e34-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="91e34-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="91e34-177">'Razor'</span></span>
- <span data-ttu-id="91e34-178">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="91e34-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="91e34-179">title: ' ASP.NET Core Blazor Forms a Validation ' Autor: Description: ' Naučte se používat scénáře ověřování formulářů a polí v Blazor . '</span><span class="sxs-lookup"><span data-stu-id="91e34-179">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="91e34-180">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="91e34-180">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="91e34-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="91e34-181">'Blazor'</span></span>
- <span data-ttu-id="91e34-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="91e34-182">'Identity'</span></span>
- <span data-ttu-id="91e34-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="91e34-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="91e34-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="91e34-184">'Razor'</span></span>
- <span data-ttu-id="91e34-185">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="91e34-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="91e34-186">title: ' ASP.NET Core Blazor Forms a Validation ' Autor: Description: ' Naučte se používat scénáře ověřování formulářů a polí v Blazor . '</span><span class="sxs-lookup"><span data-stu-id="91e34-186">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="91e34-187">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="91e34-187">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="91e34-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="91e34-188">'Blazor'</span></span>
- <span data-ttu-id="91e34-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="91e34-189">'Identity'</span></span>
- <span data-ttu-id="91e34-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="91e34-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="91e34-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="91e34-191">'Razor'</span></span>
- <span data-ttu-id="91e34-192">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="91e34-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="91e34-193">title: ' ASP.NET Core Blazor Forms a Validation ' Autor: Description: ' Naučte se používat scénáře ověřování formulářů a polí v Blazor . '</span><span class="sxs-lookup"><span data-stu-id="91e34-193">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="91e34-194">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="91e34-194">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="91e34-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="91e34-195">'Blazor'</span></span>
- <span data-ttu-id="91e34-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="91e34-196">'Identity'</span></span>
- <span data-ttu-id="91e34-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="91e34-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="91e34-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="91e34-198">'Razor'</span></span>
- <span data-ttu-id="91e34-199">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="91e34-199">'SignalR' uid:</span></span> 

-
<span data-ttu-id="91e34-200">title: ' ASP.NET Core Blazor Forms a Validation ' Autor: Description: ' Naučte se používat scénáře ověřování formulářů a polí v Blazor . '</span><span class="sxs-lookup"><span data-stu-id="91e34-200">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="91e34-201">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="91e34-201">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="91e34-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="91e34-202">'Blazor'</span></span>
- <span data-ttu-id="91e34-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="91e34-203">'Identity'</span></span>
- <span data-ttu-id="91e34-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="91e34-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="91e34-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="91e34-205">'Razor'</span></span>
- <span data-ttu-id="91e34-206">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="91e34-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="91e34-207">title: ' ASP.NET Core Blazor Forms a Validation ' Autor: Description: ' Naučte se používat scénáře ověřování formulářů a polí v Blazor . '</span><span class="sxs-lookup"><span data-stu-id="91e34-207">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="91e34-208">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="91e34-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="91e34-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="91e34-209">'Blazor'</span></span>
- <span data-ttu-id="91e34-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="91e34-210">'Identity'</span></span>
- <span data-ttu-id="91e34-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="91e34-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="91e34-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="91e34-212">'Razor'</span></span>
- <span data-ttu-id="91e34-213">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="91e34-213">'SignalR' uid:</span></span> 

<span data-ttu-id="91e34-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span><span class="sxs-lookup"><span data-stu-id="91e34-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span></span>

<span data-ttu-id="91e34-215">Všechny vstupní komponenty, včetně <xref:Microsoft.AspNetCore.Components.Forms.EditForm> , podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="91e34-215">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="91e34-216">Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do vykresleného prvku HTML.</span><span class="sxs-lookup"><span data-stu-id="91e34-216">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="91e34-217">Vstupní komponenty poskytují výchozí chování pro ověřování při úpravách a změně jejich třídy CSS tak, aby odrážely stav pole.</span><span class="sxs-lookup"><span data-stu-id="91e34-217">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="91e34-218">Některé součásti obsahují užitečnou logiku analýzy.</span><span class="sxs-lookup"><span data-stu-id="91e34-218">Some components include useful parsing logic.</span></span> <span data-ttu-id="91e34-219">Například <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> a <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> zpracujte neanalyzovatelné hodnoty tak, že je zaregistrujete jako chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="91e34-219">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="91e34-220">Typy, které mohou přijmout hodnoty null, podporují také hodnotu null cílového pole (například `int?` ).</span><span class="sxs-lookup"><span data-stu-id="91e34-220">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="91e34-221">Následující `Starship` typ definuje logiku ověřování pomocí větší sady vlastností a poznámek k datům než dříve `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="91e34-221">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="91e34-222">V předchozím příkladu `Description` je volitelná, protože nejsou k dispozici žádné anotace dat.</span><span class="sxs-lookup"><span data-stu-id="91e34-222">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="91e34-223">Následující formulář ověří uživatelský vstup pomocí ověřování definovaného v `Starship` modelu:</span><span class="sxs-lookup"><span data-stu-id="91e34-223">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
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
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="91e34-224"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Vytvoří <xref:Microsoft.AspNetCore.Components.Forms.EditContext> jako [kaskádovou hodnotu](xref:blazor/components#cascading-values-and-parameters) , která sleduje metadata procesu úprav, včetně toho, která pole jsou upravena a aktuální zprávy ověření.</span><span class="sxs-lookup"><span data-stu-id="91e34-224">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="91e34-225"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Poskytuje taky praktické události pro platná a neplatná odeslání ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> , <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit> ).</span><span class="sxs-lookup"><span data-stu-id="91e34-225">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="91e34-226">Alternativně můžete použít <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> k aktivaci ověřování a hodnot polí pro kontrolu pomocí vlastního ověřovacího kódu.</span><span class="sxs-lookup"><span data-stu-id="91e34-226">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="91e34-227">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="91e34-227">In the following example:</span></span>

* <span data-ttu-id="91e34-228">`HandleSubmit`Metoda se spustí, když je vybráno tlačítko **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="91e34-228">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="91e34-229">Formulář se ověří pomocí formuláře <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="91e34-229">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="91e34-230">Formulář se dále ověří předáním <xref:Microsoft.AspNetCore.Components.Forms.EditContext> `ServerValidate` metody, která volá koncový bod webového rozhraní API na serveru (*není zobrazený*).</span><span class="sxs-lookup"><span data-stu-id="91e34-230">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="91e34-231">Další kód se spustí v závislosti na výsledku ověřování na straně klienta a serveru kontrolou `isValid` .</span><span class="sxs-lookup"><span data-stu-id="91e34-231">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="91e34-232">InputText na základě události vstupu</span><span class="sxs-lookup"><span data-stu-id="91e34-232">InputText based on the input event</span></span>

<span data-ttu-id="91e34-233">Pomocí <xref:Microsoft.AspNetCore.Components.Forms.InputText> komponenty můžete vytvořit vlastní komponentu, která `input` místo události používá událost `change` .</span><span class="sxs-lookup"><span data-stu-id="91e34-233">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="91e34-234">Vytvořte komponentu s následujícím kódem a použijte komponentu stejně jako <xref:Microsoft.AspNetCore.Components.Forms.InputText> je použit:</span><span class="sxs-lookup"><span data-stu-id="91e34-234">Create a component with the following markup, and use the component just as <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="91e34-235">Práce s přepínači</span><span class="sxs-lookup"><span data-stu-id="91e34-235">Work with radio buttons</span></span>

<span data-ttu-id="91e34-236">Při práci s přepínači ve formuláři je datová vazba zpracovávána jinak než jiné prvky, protože přepínače jsou vyhodnocovány jako skupina.</span><span class="sxs-lookup"><span data-stu-id="91e34-236">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="91e34-237">Hodnota každého přepínacího tlačítka je pevná, ale hodnota skupiny přepínačů je hodnota vybraného přepínacího tlačítka.</span><span class="sxs-lookup"><span data-stu-id="91e34-237">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="91e34-238">Následující příklad ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="91e34-238">The following example shows how to:</span></span>

* <span data-ttu-id="91e34-239">Zpracuje datovou vazbu pro skupinu přepínačů.</span><span class="sxs-lookup"><span data-stu-id="91e34-239">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="91e34-240">Podpora ověřování pomocí vlastní `InputRadio` součásti.</span><span class="sxs-lookup"><span data-stu-id="91e34-240">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="91e34-241">V následujícím <xref:Microsoft.AspNetCore.Components.Forms.EditForm> příkladu se `InputRadio` k získání a ověření hodnocení od uživatele používá předchozí komponenta:</span><span class="sxs-lookup"><span data-stu-id="91e34-241">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
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
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a><span data-ttu-id="91e34-242">Podpora ověřování</span><span class="sxs-lookup"><span data-stu-id="91e34-242">Validation support</span></span>

<span data-ttu-id="91e34-243"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Komponenta připojuje ověřování pomocí datových poznámek do kaskády <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="91e34-243">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="91e34-244">Povolení podpory pro ověřování pomocí datových poznámek vyžaduje toto explicitní gesto.</span><span class="sxs-lookup"><span data-stu-id="91e34-244">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="91e34-245">Chcete-li použít jiný systém ověřování než datové poznámky, nahraďte <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> vlastní implementaci.</span><span class="sxs-lookup"><span data-stu-id="91e34-245">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="91e34-246">Implementace ASP.NET Core je k dispozici pro kontrolu ve zdroji odkazu: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="91e34-246">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="91e34-247">provádí dva typy ověřování:</span><span class="sxs-lookup"><span data-stu-id="91e34-247"> performs two types of validation:</span></span>

* <span data-ttu-id="91e34-248">*Ověřování polí* se provede, když se karty uživatele nacházejí v poli.</span><span class="sxs-lookup"><span data-stu-id="91e34-248">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="91e34-249">Při ověřování pole <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> přidruží součást všechny hlášené výsledky ověření k poli.</span><span class="sxs-lookup"><span data-stu-id="91e34-249">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="91e34-250">*Ověřování modelu* se provede, když uživatel formulář odešle.</span><span class="sxs-lookup"><span data-stu-id="91e34-250">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="91e34-251">Při ověřování modelu se <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Komponenta pokusí určit pole na základě názvu člena, který sestavy výsledků ověření.</span><span class="sxs-lookup"><span data-stu-id="91e34-251">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="91e34-252">Výsledky ověření, které nejsou přidruženy k jednotlivým členům, jsou přidruženy k modelu, nikoli poli.</span><span class="sxs-lookup"><span data-stu-id="91e34-252">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="91e34-253">Komponenty zprávy pro Shrnutí a ověření</span><span class="sxs-lookup"><span data-stu-id="91e34-253">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="91e34-254"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Komponenta shrnuje všechny zprávy o ověřování, které se podobají [pomocníka značek pro Shrnutí ověření](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="91e34-254">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="91e34-255">Zprávy ověřování výstupu pro konkrétní model s `Model` parametrem:</span><span class="sxs-lookup"><span data-stu-id="91e34-255">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="91e34-256"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Komponenta zobrazí ověřovací zprávy pro konkrétní pole, které se podobá [pomocníka značek ověřovací zprávy](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="91e34-256">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="91e34-257">Zadejte pole pro ověření s `For` atributem a výrazem lambda pojmenování vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="91e34-257">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="91e34-258"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Komponenty a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> podporují libovolné atributy.</span><span class="sxs-lookup"><span data-stu-id="91e34-258">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="91e34-259">Všechny atributy, které se neshodují s parametrem komponenty, jsou přidány do generovaného `<div>` `<ul>` prvku nebo.</span><span class="sxs-lookup"><span data-stu-id="91e34-259">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="91e34-260">Vlastní ověřovací atributy</span><span class="sxs-lookup"><span data-stu-id="91e34-260">Custom validation attributes</span></span>

<span data-ttu-id="91e34-261">Chcete-li zajistit, aby byl výsledek ověření správně přidružen k poli při použití [vlastního ověřovacího atributu](xref:mvc/models/validation#custom-attributes), předejte kontext ověření <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> při vytváření <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="91e34-261">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="blazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="91e34-262">balíček pro ověření datových poznámek</span><span class="sxs-lookup"><span data-stu-id="91e34-262"> data annotations validation package</span></span>

<span data-ttu-id="91e34-263">[Microsoft. AspNetCore. Components. Dataanotaces. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) je balíček, který vyplní mezery při ověřování pomocí <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponenty.</span><span class="sxs-lookup"><span data-stu-id="91e34-263">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="91e34-264">Balíček je momentálně *experimentální*.</span><span class="sxs-lookup"><span data-stu-id="91e34-264">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="91e34-265">[CompareProperty] – atribut</span><span class="sxs-lookup"><span data-stu-id="91e34-265">[CompareProperty] attribute</span></span>

<span data-ttu-id="91e34-266"><xref:System.ComponentModel.DataAnnotations.CompareAttribute>Nefunguje dobře s <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> komponentou, protože nepřiřazuje výsledek ověření k určitému členu.</span><span class="sxs-lookup"><span data-stu-id="91e34-266">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="91e34-267">To může vést k nekonzistentnímu chování mezi ověřováním na úrovni polí a při ověřování celého modelu při odeslání.</span><span class="sxs-lookup"><span data-stu-id="91e34-267">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="91e34-268">Balíček [Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experiment* zavádí další ověřovací atribut, `ComparePropertyAttribute` který tato omezení funguje.</span><span class="sxs-lookup"><span data-stu-id="91e34-268">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="91e34-269">V Blazor aplikaci `[CompareProperty]` je přímá náhrada za [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="91e34-269">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="91e34-270">Vnořené modely, typy kolekcí a komplexní typy</span><span class="sxs-lookup"><span data-stu-id="91e34-270">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="91e34-271">poskytuje podporu pro ověřování vstupu formuláře pomocí datových poznámek s integrovaným <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="91e34-271"> provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="91e34-272"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Pouze ověří vlastnosti nejvyšší úrovně modelu svázaného s formulářem, který není vlastností kolekce nebo komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="91e34-272">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="91e34-273">Chcete-li ověřit celý graf objektu vázaného modelu, včetně vlastností kolekce a komplexního typu, použijte rozhraní, které `ObjectGraphDataAnnotationsValidator` poskytuje *experimentální* balíček [Microsoft. AspNetCore. Components. DataAnnotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="91e34-273">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="91e34-274">Přidávejte do vlastností modelu poznámky `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="91e34-274">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="91e34-275">V následujících třídách modelu `ShipDescription` obsahuje Třída další datové poznámky pro ověření, když je model svázán s formulářem:</span><span class="sxs-lookup"><span data-stu-id="91e34-275">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="91e34-276">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="91e34-276">*Starship.cs*:</span></span>

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

<span data-ttu-id="91e34-277">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="91e34-277">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="91e34-278">Povolit tlačítko Odeslat na základě ověření formuláře</span><span class="sxs-lookup"><span data-stu-id="91e34-278">Enable the submit button based on form validation</span></span>

<span data-ttu-id="91e34-279">Povolení a zakázání tlačítka Odeslat na základě ověření formuláře:</span><span class="sxs-lookup"><span data-stu-id="91e34-279">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="91e34-280"><xref:Microsoft.AspNetCore.Components.Forms.EditContext>K přiřazení modelu při inicializaci komponenty použijte formulář.</span><span class="sxs-lookup"><span data-stu-id="91e34-280">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="91e34-281">Ověřte formulář v <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> zpětném volání kontextu a povolte a zakažte tlačítko Odeslat.</span><span class="sxs-lookup"><span data-stu-id="91e34-281">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="91e34-282">Odpojte obslužnou rutinu události v `Dispose` metodě.</span><span class="sxs-lookup"><span data-stu-id="91e34-282">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="91e34-283">Další informace naleznete v tématu <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="91e34-283">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
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

<span data-ttu-id="91e34-284">V předchozím příkladu nastavte `formInvalid` na `false` if:</span><span class="sxs-lookup"><span data-stu-id="91e34-284">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="91e34-285">Formulář je předem načten s platnými výchozími hodnotami.</span><span class="sxs-lookup"><span data-stu-id="91e34-285">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="91e34-286">Chcete povolit tlačítko Odeslat, když se formulář načte.</span><span class="sxs-lookup"><span data-stu-id="91e34-286">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="91e34-287">Vedlejším účinkem předcházejícího přístupu je, že <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> Komponenta je naplněná neplatnými poli poté, co uživatel komunikuje s jakýmkoli jedním polem.</span><span class="sxs-lookup"><span data-stu-id="91e34-287">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="91e34-288">Tento scénář je možné vyřešit jedním z následujících způsobů:</span><span class="sxs-lookup"><span data-stu-id="91e34-288">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="91e34-289">Nepoužívejte <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> komponentu na formuláři.</span><span class="sxs-lookup"><span data-stu-id="91e34-289">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="91e34-290">Zpřístupní <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> komponentu, když je vybráno tlačítko Odeslat (například v `HandleValidSubmit` metodě).</span><span class="sxs-lookup"><span data-stu-id="91e34-290">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
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
