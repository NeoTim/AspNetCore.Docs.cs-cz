---
title: Použití konvencí webového rozhraní API
author: pranavkm
description: Přečtěte si o konvencích webového rozhraní API v ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/conventions
ms.openlocfilehash: e18a2096c3b9fa788ba2a9713d0f25c1e76af2db
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019960"
---
# <a name="use-web-api-conventions"></a><span data-ttu-id="b045d-103">Použití konvencí webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="b045d-103">Use web API conventions</span></span>

<span data-ttu-id="b045d-104">Od [Pranav Krishnamoorthy](https://github.com/pranavkm) a [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="b045d-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="b045d-105">ASP.NET Core 2,2 a novější zahrnuje způsob, jak extrahovat společnou [dokumentaci k rozhraní API](xref:tutorials/web-api-help-pages-using-swagger) a použít ji pro více akcí, řadičů nebo všech řadičů v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="b045d-105">ASP.NET Core 2.2 and later includes a way to extract common [API documentation](xref:tutorials/web-api-help-pages-using-swagger) and apply it to multiple actions, controllers, or all controllers within an assembly.</span></span> <span data-ttu-id="b045d-106">Konvence webového rozhraní API jsou náhradou za upravení jednotlivé akce pomocí [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) .</span><span class="sxs-lookup"><span data-stu-id="b045d-106">Web API conventions are a substitute for decorating individual actions with [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span></span>

<span data-ttu-id="b045d-107">Konvence vám umožní:</span><span class="sxs-lookup"><span data-stu-id="b045d-107">A convention allows you to:</span></span>

* <span data-ttu-id="b045d-108">Definujte nejběžnější návratové typy a stavové kódy vrácené z konkrétního typu akce.</span><span class="sxs-lookup"><span data-stu-id="b045d-108">Define the most common return types and status codes returned from a specific type of action.</span></span>
* <span data-ttu-id="b045d-109">Identifikujte akce, které se odchylují od definovaného standardu.</span><span class="sxs-lookup"><span data-stu-id="b045d-109">Identify actions that deviate from the defined standard.</span></span>

<span data-ttu-id="b045d-110">ASP.NET Core MVC 2,2 a novější obsahuje sadu výchozích konvencí pro <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="b045d-110">ASP.NET Core MVC 2.2 and later includes a set of default conventions in <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>.</span></span> <span data-ttu-id="b045d-111">Konvence vycházejí z kontroleru (*ValuesController.cs*), který je k dispozici v šabloně projektu **rozhraní API** ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b045d-111">The conventions are based on the controller (*ValuesController.cs*) provided in the ASP.NET Core **API** project template.</span></span> <span data-ttu-id="b045d-112">Pokud se vaše akce řídí vzory v šabloně, měli byste být s použitím výchozích konvence úspěšné.</span><span class="sxs-lookup"><span data-stu-id="b045d-112">If your actions follow the patterns in the template, you should be successful using the default conventions.</span></span> <span data-ttu-id="b045d-113">Pokud výchozí konvence nevyhovují vašim potřebám, přečtěte si téma [Vytvoření konvencí webového rozhraní API](#create-web-api-conventions).</span><span class="sxs-lookup"><span data-stu-id="b045d-113">If the default conventions don't meet your needs, see [Create web API conventions](#create-web-api-conventions).</span></span>

<span data-ttu-id="b045d-114">V době běhu <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> rozumí konvence.</span><span class="sxs-lookup"><span data-stu-id="b045d-114">At runtime, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> understands conventions.</span></span> <span data-ttu-id="b045d-115">`ApiExplorer`má abstrakce MVC ke komunikaci s [openapi](https://www.openapis.org/) (označuje se také jako Swagger) generátory dokumentů.</span><span class="sxs-lookup"><span data-stu-id="b045d-115">`ApiExplorer` is MVC's abstraction to communicate with [OpenAPI](https://www.openapis.org/) (also known as Swagger) document generators.</span></span> <span data-ttu-id="b045d-116">Atributy z použité konvence jsou spojeny s akcí a jsou obsaženy v dokumentaci k OpenAPI akce.</span><span class="sxs-lookup"><span data-stu-id="b045d-116">Attributes from the applied convention are associated with an action and are included in the action's OpenAPI documentation.</span></span> <span data-ttu-id="b045d-117">[Analyzátory rozhraní API](xref:web-api/advanced/analyzers) také pochopit konvence.</span><span class="sxs-lookup"><span data-stu-id="b045d-117">[API analyzers](xref:web-api/advanced/analyzers) also understand conventions.</span></span> <span data-ttu-id="b045d-118">Pokud je vaše akce nepostupná (například vrátí stavový kód, který není dokumentován v použité konvenci), zobrazí se upozornění, které vám umožní zdokumentovat stavový kód.</span><span class="sxs-lookup"><span data-stu-id="b045d-118">If your action is unconventional (for example, it returns a status code that isn't documented by the applied convention), a warning encourages you to document the status code.</span></span>

<span data-ttu-id="b045d-119">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b045d-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="apply-web-api-conventions"></a><span data-ttu-id="b045d-120">Použít konvence webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="b045d-120">Apply web API conventions</span></span>

<span data-ttu-id="b045d-121">Konvence netvoří; Každá akce může být přidružena k přesně jedné úmluvě.</span><span class="sxs-lookup"><span data-stu-id="b045d-121">Conventions don't compose; each action may be associated with exactly one convention.</span></span> <span data-ttu-id="b045d-122">Konkrétnější konvence mají přednost před méně specifickými konvencemi.</span><span class="sxs-lookup"><span data-stu-id="b045d-122">More specific conventions take precedence over less specific conventions.</span></span> <span data-ttu-id="b045d-123">Výběr není deterministický, pokud se dvě nebo více konvencí se stejnou prioritou vztahují na akci.</span><span class="sxs-lookup"><span data-stu-id="b045d-123">The selection is non-deterministic when two or more conventions of the same priority apply to an action.</span></span> <span data-ttu-id="b045d-124">Následující možnosti existují, pokud chcete použít konvenci pro akci, od nejpřesnější po nejméně specifickou:</span><span class="sxs-lookup"><span data-stu-id="b045d-124">The following options exist to apply a convention to an action, from the most specific to the least specific:</span></span>

1. <span data-ttu-id="b045d-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute`&mdash;Platí pro jednotlivé akce a určuje typ konvence a metodu konvence, která se vztahuje.</span><span class="sxs-lookup"><span data-stu-id="b045d-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Applies to individual actions and specifies the convention type and the convention method that applies.</span></span>

    <span data-ttu-id="b045d-126">V následujícím příkladu `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` je použita metoda konvence typu výchozí konvence pro `Update` akci:</span><span class="sxs-lookup"><span data-stu-id="b045d-126">In the following example, the default convention type's `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method is applied to the `Update` action:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    <span data-ttu-id="b045d-127">`Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put`Metoda konvence používá pro akci následující atributy:</span><span class="sxs-lookup"><span data-stu-id="b045d-127">The `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method applies the following attributes to the action:</span></span>

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    ```

    <span data-ttu-id="b045d-128">Další informace o naleznete v `[ProducesDefaultResponseType]` tématu [výchozí odpověď](https://swagger.io/docs/specification/describing-responses/#default).</span><span class="sxs-lookup"><span data-stu-id="b045d-128">For more information on `[ProducesDefaultResponseType]`, see [Default Response](https://swagger.io/docs/specification/describing-responses/#default).</span></span>

1. <span data-ttu-id="b045d-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute`použití pro kontroler &mdash; aplikuje zadaný typ konvence na všechny akce v řadiči.</span><span class="sxs-lookup"><span data-stu-id="b045d-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to a controller &mdash; Applies the specified convention type to all actions on the controller.</span></span> <span data-ttu-id="b045d-130">Metoda konvence je označena nápovědami, které určují akce, na které se vztahuje metoda konvence.</span><span class="sxs-lookup"><span data-stu-id="b045d-130">A convention method is marked with hints that determine the actions to which the convention method applies.</span></span> <span data-ttu-id="b045d-131">Další informace o pokynech najdete v tématu [Vytvoření konvencí webového rozhraní API](#create-web-api-conventions).</span><span class="sxs-lookup"><span data-stu-id="b045d-131">For more information on hints, see [Create web API conventions](#create-web-api-conventions)).</span></span>

    <span data-ttu-id="b045d-132">V následujícím příkladu se používá výchozí sada konvencí pro všechny akce v *ContactsConventionController*:</span><span class="sxs-lookup"><span data-stu-id="b045d-132">In the following example, the default set of conventions is applied to all actions in *ContactsConventionController*:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. <span data-ttu-id="b045d-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute`aplikování na sestavení &mdash; použije zadaný typ konvence na všechny řadiče v aktuálním sestavení.</span><span class="sxs-lookup"><span data-stu-id="b045d-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to an assembly &mdash; Applies the specified convention type to all controllers in the current assembly.</span></span> <span data-ttu-id="b045d-134">Jako doporučení použijte atributy na úrovni sestavení v souboru *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="b045d-134">As a recommendation, apply assembly-level attributes in the *Startup.cs* file.</span></span>

    <span data-ttu-id="b045d-135">V následujícím příkladu je použita výchozí sada konvencí pro všechny řadiče v sestavení:</span><span class="sxs-lookup"><span data-stu-id="b045d-135">In the following example, the default set of conventions is applied to all controllers in the assembly:</span></span>

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a><span data-ttu-id="b045d-136">Vytvoření konvencí webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="b045d-136">Create web API conventions</span></span>

<span data-ttu-id="b045d-137">Pokud výchozí konvence rozhraní API nevyhovují vašim potřebám, vytvořte vlastní konvence.</span><span class="sxs-lookup"><span data-stu-id="b045d-137">If the default API conventions don't meet your needs, create your own conventions.</span></span> <span data-ttu-id="b045d-138">Konvence:</span><span class="sxs-lookup"><span data-stu-id="b045d-138">A convention is:</span></span>

* <span data-ttu-id="b045d-139">Statický typ s metodami.</span><span class="sxs-lookup"><span data-stu-id="b045d-139">A static type with methods.</span></span>
* <span data-ttu-id="b045d-140">Umožňuje definovat [typy odpovědí](#response-types) a [požadavky na pojmenování](#naming-requirements) u akcí.</span><span class="sxs-lookup"><span data-stu-id="b045d-140">Capable of defining [response types](#response-types) and [naming requirements](#naming-requirements) on actions.</span></span>

### <a name="response-types"></a><span data-ttu-id="b045d-141">Typy odpovědí</span><span class="sxs-lookup"><span data-stu-id="b045d-141">Response types</span></span>

<span data-ttu-id="b045d-142">Tyto metody jsou opatřeny poznámkami `[ProducesResponseType]` nebo `[ProducesDefaultResponseType]` atributy.</span><span class="sxs-lookup"><span data-stu-id="b045d-142">These methods are annotated with `[ProducesResponseType]` or `[ProducesDefaultResponseType]` attributes.</span></span> <span data-ttu-id="b045d-143">Například:</span><span class="sxs-lookup"><span data-stu-id="b045d-143">For example:</span></span>

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

<span data-ttu-id="b045d-144">Pokud nejsou k dispozici konkrétnější atributy metadat, použití této konvence na sestavení vynutilo:</span><span class="sxs-lookup"><span data-stu-id="b045d-144">If more specific metadata attributes are absent, applying this convention to an assembly enforces that:</span></span>

* <span data-ttu-id="b045d-145">Metoda konvence se vztahuje na jakoukoli akci s názvem `Find` .</span><span class="sxs-lookup"><span data-stu-id="b045d-145">The convention method applies to any action named `Find`.</span></span>
* <span data-ttu-id="b045d-146">`id`V akci se nachází parametr s názvem `Find` .</span><span class="sxs-lookup"><span data-stu-id="b045d-146">A parameter named `id` is present on the `Find` action.</span></span>

### <a name="naming-requirements"></a><span data-ttu-id="b045d-147">Požadavky na pojmenování</span><span class="sxs-lookup"><span data-stu-id="b045d-147">Naming requirements</span></span>

<span data-ttu-id="b045d-148">`[ApiConventionNameMatch]`Atributy a `[ApiConventionTypeMatch]` lze použít na metodu konvence, která určuje akce, na které se vztahují.</span><span class="sxs-lookup"><span data-stu-id="b045d-148">The `[ApiConventionNameMatch]` and `[ApiConventionTypeMatch]` attributes can be applied to the convention method that determines the actions to which they apply.</span></span> <span data-ttu-id="b045d-149">Například:</span><span class="sxs-lookup"><span data-stu-id="b045d-149">For example:</span></span>

```csharp
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(StatusCodes.Status404NotFound)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

<span data-ttu-id="b045d-150">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b045d-150">In the preceding example:</span></span>

* <span data-ttu-id="b045d-151">`Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix`Možnost použitá na metodu označuje, že konvence odpovídá jakékoli akci s předponou "Find".</span><span class="sxs-lookup"><span data-stu-id="b045d-151">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` option applied to the method indicates that the convention matches any action prefixed with "Find".</span></span> <span data-ttu-id="b045d-152">Mezi příklady akcí pro porovnání patří `Find` , `FindPet` a `FindById` .</span><span class="sxs-lookup"><span data-stu-id="b045d-152">Examples of matching actions include `Find`, `FindPet`, and `FindById`.</span></span>
* <span data-ttu-id="b045d-153">`Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix`Parametr aplikovaný na parametr označuje, že konvence odpovídá metodám s právě jedním parametrem končícím v identifikátoru přípony.</span><span class="sxs-lookup"><span data-stu-id="b045d-153">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` applied to the parameter indicates that the convention matches methods with exactly one parameter ending in the suffix identifier.</span></span> <span data-ttu-id="b045d-154">Příklady zahrnují parametry jako `id` nebo `petId` .</span><span class="sxs-lookup"><span data-stu-id="b045d-154">Examples include parameters such as `id` or `petId`.</span></span> <span data-ttu-id="b045d-155">`ApiConventionTypeMatch`může být obdobně aplikován na typy pro omezení typu parametru.</span><span class="sxs-lookup"><span data-stu-id="b045d-155">`ApiConventionTypeMatch` can be similarly applied to types to constrain the parameter type.</span></span> <span data-ttu-id="b045d-156">`params[]`Argument označuje zbývající parametry, které nemusejí být explicitně spárovány.</span><span class="sxs-lookup"><span data-stu-id="b045d-156">A `params[]` argument indicates remaining parameters that don't need to be explicitly matched.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b045d-157">Další materiály</span><span class="sxs-lookup"><span data-stu-id="b045d-157">Additional resources</span></span>

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
