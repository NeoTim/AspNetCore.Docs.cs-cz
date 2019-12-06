---
title: Použití konvencí webového rozhraní API
author: pranavkm
description: Přečtěte si o konvencích webového rozhraní API v ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: web-api/advanced/conventions
ms.openlocfilehash: 2c7e33da24322504fc5e1be83c0b814710186687
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881320"
---
# <a name="use-web-api-conventions"></a><span data-ttu-id="3dd70-103">Použití konvencí webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="3dd70-103">Use web API conventions</span></span>

<span data-ttu-id="3dd70-104">Od [Pranav Krishnamoorthy](https://github.com/pranavkm) a [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="3dd70-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="3dd70-105">ASP.NET Core 2,2 a novější zahrnuje způsob, jak extrahovat společnou [dokumentaci k rozhraní API](xref:tutorials/web-api-help-pages-using-swagger) a použít ji pro více akcí, řadičů nebo všech řadičů v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="3dd70-105">ASP.NET Core 2.2 and later includes a way to extract common [API documentation](xref:tutorials/web-api-help-pages-using-swagger) and apply it to multiple actions, controllers, or all controllers within an assembly.</span></span> <span data-ttu-id="3dd70-106">Konvence webového rozhraní API jsou náhradou pro upravení jednotlivé akce pomocí [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span><span class="sxs-lookup"><span data-stu-id="3dd70-106">Web API conventions are a substitute for decorating individual actions with [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span></span>

<span data-ttu-id="3dd70-107">Konvence vám umožní:</span><span class="sxs-lookup"><span data-stu-id="3dd70-107">A convention allows you to:</span></span>

* <span data-ttu-id="3dd70-108">Definujte nejběžnější návratové typy a stavové kódy vrácené z konkrétního typu akce.</span><span class="sxs-lookup"><span data-stu-id="3dd70-108">Define the most common return types and status codes returned from a specific type of action.</span></span>
* <span data-ttu-id="3dd70-109">Identifikujte akce, které se odchylují od definovaného standardu.</span><span class="sxs-lookup"><span data-stu-id="3dd70-109">Identify actions that deviate from the defined standard.</span></span>

<span data-ttu-id="3dd70-110">ASP.NET Core MVC 2,2 a novější zahrnuje sadu výchozích konvencí v <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="3dd70-110">ASP.NET Core MVC 2.2 and later includes a set of default conventions in <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>.</span></span> <span data-ttu-id="3dd70-111">Konvence vycházejí z kontroleru (*ValuesController.cs*), který je k dispozici v šabloně projektu **rozhraní API** ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3dd70-111">The conventions are based on the controller (*ValuesController.cs*) provided in the ASP.NET Core **API** project template.</span></span> <span data-ttu-id="3dd70-112">Pokud se vaše akce řídí vzory v šabloně, měli byste být s použitím výchozích konvence úspěšné.</span><span class="sxs-lookup"><span data-stu-id="3dd70-112">If your actions follow the patterns in the template, you should be successful using the default conventions.</span></span> <span data-ttu-id="3dd70-113">Pokud výchozí konvence nevyhovují vašim potřebám, přečtěte si téma [Vytvoření konvencí webového rozhraní API](#create-web-api-conventions).</span><span class="sxs-lookup"><span data-stu-id="3dd70-113">If the default conventions don't meet your needs, see [Create web API conventions](#create-web-api-conventions).</span></span>

<span data-ttu-id="3dd70-114">V době běhu <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> pochopení konvencí.</span><span class="sxs-lookup"><span data-stu-id="3dd70-114">At runtime, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> understands conventions.</span></span> <span data-ttu-id="3dd70-115">`ApiExplorer` je abstrakce MVC ke komunikaci s [openapi](https://www.openapis.org/) (označovanými také jako Swagger) generátory dokumentů.</span><span class="sxs-lookup"><span data-stu-id="3dd70-115">`ApiExplorer` is MVC's abstraction to communicate with [OpenAPI](https://www.openapis.org/) (also known as Swagger) document generators.</span></span> <span data-ttu-id="3dd70-116">Atributy z použité konvence jsou spojeny s akcí a jsou obsaženy v dokumentaci k OpenAPI akce.</span><span class="sxs-lookup"><span data-stu-id="3dd70-116">Attributes from the applied convention are associated with an action and are included in the action's OpenAPI documentation.</span></span> <span data-ttu-id="3dd70-117">[Analyzátory rozhraní API](xref:web-api/advanced/analyzers) také pochopit konvence.</span><span class="sxs-lookup"><span data-stu-id="3dd70-117">[API analyzers](xref:web-api/advanced/analyzers) also understand conventions.</span></span> <span data-ttu-id="3dd70-118">Pokud je vaše akce nepostupná (například vrátí stavový kód, který není dokumentován v použité konvenci), zobrazí se upozornění, které vám umožní zdokumentovat stavový kód.</span><span class="sxs-lookup"><span data-stu-id="3dd70-118">If your action is unconventional (for example, it returns a status code that isn't documented by the applied convention), a warning encourages you to document the status code.</span></span>

<span data-ttu-id="3dd70-119">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3dd70-119">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="apply-web-api-conventions"></a><span data-ttu-id="3dd70-120">Použít konvence webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="3dd70-120">Apply web API conventions</span></span>

<span data-ttu-id="3dd70-121">Konvence netvoří; Každá akce může být přidružena k přesně jedné úmluvě.</span><span class="sxs-lookup"><span data-stu-id="3dd70-121">Conventions don't compose; each action may be associated with exactly one convention.</span></span> <span data-ttu-id="3dd70-122">Konkrétnější konvence mají přednost před méně specifickými konvencemi.</span><span class="sxs-lookup"><span data-stu-id="3dd70-122">More specific conventions take precedence over less specific conventions.</span></span> <span data-ttu-id="3dd70-123">Výběr není deterministický, pokud se dvě nebo více konvencí se stejnou prioritou vztahují na akci.</span><span class="sxs-lookup"><span data-stu-id="3dd70-123">The selection is non-deterministic when two or more conventions of the same priority apply to an action.</span></span> <span data-ttu-id="3dd70-124">Následující možnosti existují, pokud chcete použít konvenci pro akci, od nejpřesnější po nejméně specifickou:</span><span class="sxs-lookup"><span data-stu-id="3dd70-124">The following options exist to apply a convention to an action, from the most specific to the least specific:</span></span>

1. <span data-ttu-id="3dd70-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; se vztahuje na jednotlivé akce a určuje typ konvence a metodu konvence, která se vztahuje.</span><span class="sxs-lookup"><span data-stu-id="3dd70-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Applies to individual actions and specifies the convention type and the convention method that applies.</span></span>

    <span data-ttu-id="3dd70-126">V následujícím příkladu je metoda `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` konvence typu výchozí konvence použita pro akci `Update`:</span><span class="sxs-lookup"><span data-stu-id="3dd70-126">In the following example, the default convention type's `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method is applied to the `Update` action:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    <span data-ttu-id="3dd70-127">Metoda `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` konvence používá pro akci následující atributy:</span><span class="sxs-lookup"><span data-stu-id="3dd70-127">The `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method applies the following attributes to the action:</span></span>

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    ```

    <span data-ttu-id="3dd70-128">Další informace o `[ProducesDefaultResponseType]`najdete v tématu [výchozí odpověď](https://swagger.io/docs/specification/describing-responses/#default).</span><span class="sxs-lookup"><span data-stu-id="3dd70-128">For more information on `[ProducesDefaultResponseType]`, see [Default Response](https://swagger.io/docs/specification/describing-responses/#default).</span></span>

1. <span data-ttu-id="3dd70-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` použité pro kontroler &mdash; aplikuje zadaný typ konvence na všechny akce na kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3dd70-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to a controller &mdash; Applies the specified convention type to all actions on the controller.</span></span> <span data-ttu-id="3dd70-130">Metoda konvence je označena nápovědami, které určují akce, na které se vztahuje metoda konvence.</span><span class="sxs-lookup"><span data-stu-id="3dd70-130">A convention method is marked with hints that determine the actions to which the convention method applies.</span></span> <span data-ttu-id="3dd70-131">Další informace o pokynech najdete v tématu [Vytvoření konvencí webového rozhraní API](#create-web-api-conventions).</span><span class="sxs-lookup"><span data-stu-id="3dd70-131">For more information on hints, see [Create web API conventions](#create-web-api-conventions)).</span></span>

    <span data-ttu-id="3dd70-132">V následujícím příkladu se používá výchozí sada konvencí pro všechny akce v *ContactsConventionController*:</span><span class="sxs-lookup"><span data-stu-id="3dd70-132">In the following example, the default set of conventions is applied to all actions in *ContactsConventionController*:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. <span data-ttu-id="3dd70-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` použitý na sestavení &mdash; použije zadaný typ konvence na všechny řadiče v aktuálním sestavení.</span><span class="sxs-lookup"><span data-stu-id="3dd70-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to an assembly &mdash; Applies the specified convention type to all controllers in the current assembly.</span></span> <span data-ttu-id="3dd70-134">Jako doporučení použijte atributy na úrovni sestavení v souboru *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="3dd70-134">As a recommendation, apply assembly-level attributes in the *Startup.cs* file.</span></span>

    <span data-ttu-id="3dd70-135">V následujícím příkladu je použita výchozí sada konvencí pro všechny řadiče v sestavení:</span><span class="sxs-lookup"><span data-stu-id="3dd70-135">In the following example, the default set of conventions is applied to all controllers in the assembly:</span></span>

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a><span data-ttu-id="3dd70-136">Vytvoření konvencí webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="3dd70-136">Create web API conventions</span></span>

<span data-ttu-id="3dd70-137">Pokud výchozí konvence rozhraní API nevyhovují vašim potřebám, vytvořte vlastní konvence.</span><span class="sxs-lookup"><span data-stu-id="3dd70-137">If the default API conventions don't meet your needs, create your own conventions.</span></span> <span data-ttu-id="3dd70-138">Konvence:</span><span class="sxs-lookup"><span data-stu-id="3dd70-138">A convention is:</span></span>

* <span data-ttu-id="3dd70-139">Statický typ s metodami.</span><span class="sxs-lookup"><span data-stu-id="3dd70-139">A static type with methods.</span></span>
* <span data-ttu-id="3dd70-140">Umožňuje definovat [typy odpovědí](#response-types) a [požadavky na pojmenování](#naming-requirements) u akcí.</span><span class="sxs-lookup"><span data-stu-id="3dd70-140">Capable of defining [response types](#response-types) and [naming requirements](#naming-requirements) on actions.</span></span>

### <a name="response-types"></a><span data-ttu-id="3dd70-141">Typy odpovědí</span><span class="sxs-lookup"><span data-stu-id="3dd70-141">Response types</span></span>

<span data-ttu-id="3dd70-142">Tyto metody jsou opatřeny poznámkami pomocí atributů `[ProducesResponseType]` nebo `[ProducesDefaultResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="3dd70-142">These methods are annotated with `[ProducesResponseType]` or `[ProducesDefaultResponseType]` attributes.</span></span> <span data-ttu-id="3dd70-143">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3dd70-143">For example:</span></span>

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public static void Find(int id)
    {
    }
}
```

<span data-ttu-id="3dd70-144">Pokud nejsou k dispozici konkrétnější atributy metadat, použití této konvence na sestavení vynutilo:</span><span class="sxs-lookup"><span data-stu-id="3dd70-144">If more specific metadata attributes are absent, applying this convention to an assembly enforces that:</span></span>

* <span data-ttu-id="3dd70-145">Metoda konvence se vztahuje na všechny akce s názvem `Find`.</span><span class="sxs-lookup"><span data-stu-id="3dd70-145">The convention method applies to any action named `Find`.</span></span>
* <span data-ttu-id="3dd70-146">V akci `Find` je přítomen parametr s názvem `id`.</span><span class="sxs-lookup"><span data-stu-id="3dd70-146">A parameter named `id` is present on the `Find` action.</span></span>

### <a name="naming-requirements"></a><span data-ttu-id="3dd70-147">Požadavky na pojmenovávání</span><span class="sxs-lookup"><span data-stu-id="3dd70-147">Naming requirements</span></span>

<span data-ttu-id="3dd70-148">Atributy `[ApiConventionNameMatch]` a `[ApiConventionTypeMatch]` lze použít na metodu konvence, která určuje akce, na které se vztahují.</span><span class="sxs-lookup"><span data-stu-id="3dd70-148">The `[ApiConventionNameMatch]` and `[ApiConventionTypeMatch]` attributes can be applied to the convention method that determines the actions to which they apply.</span></span> <span data-ttu-id="3dd70-149">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3dd70-149">For example:</span></span>

```csharp
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(StatusCodes.Status404NotFound)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

<span data-ttu-id="3dd70-150">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="3dd70-150">In the preceding example:</span></span>

* <span data-ttu-id="3dd70-151">Možnost `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` použitá pro metodu označuje, že konvence odpovídá jakékoli akci s předponou "Find".</span><span class="sxs-lookup"><span data-stu-id="3dd70-151">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` option applied to the method indicates that the convention matches any action prefixed with "Find".</span></span> <span data-ttu-id="3dd70-152">Mezi příklady akcí pro porovnání patří `Find`, `FindPet`a `FindById`.</span><span class="sxs-lookup"><span data-stu-id="3dd70-152">Examples of matching actions include `Find`, `FindPet`, and `FindById`.</span></span>
* <span data-ttu-id="3dd70-153">`Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` použité pro parametr označuje, že konvence odpovídá metodám s přesně jedním parametrem končícím identifikátorem přípony.</span><span class="sxs-lookup"><span data-stu-id="3dd70-153">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` applied to the parameter indicates that the convention matches methods with exactly one parameter ending in the suffix identifier.</span></span> <span data-ttu-id="3dd70-154">Příklady zahrnují parametry jako `id` nebo `petId`.</span><span class="sxs-lookup"><span data-stu-id="3dd70-154">Examples include parameters such as `id` or `petId`.</span></span> <span data-ttu-id="3dd70-155">`ApiConventionTypeMatch` lze obdobně použít u typů k omezení typu parametru.</span><span class="sxs-lookup"><span data-stu-id="3dd70-155">`ApiConventionTypeMatch` can be similarly applied to types to constrain the parameter type.</span></span> <span data-ttu-id="3dd70-156">Argument `params[]` označuje zbývající parametry, které nemusejí být explicitně spárovány.</span><span class="sxs-lookup"><span data-stu-id="3dd70-156">A `params[]` argument indicates remaining parameters that don't need to be explicitly matched.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3dd70-157">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="3dd70-157">Additional resources</span></span>

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
