---
title: Řiďte se vytváření webového rozhraní API
author: pranavkm
description: Další informace o vytváření webových rozhraní API v ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/13/2018
uid: web-api/advanced/conventions
ms.openlocfilehash: 64be4984779724eb60af3b70d4f52b22eae32213
ms.sourcegitcommit: a467828b5e4eaae291d961ffe2279a571900de23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58142305"
---
# <a name="use-web-api-conventions"></a><span data-ttu-id="2795a-103">Řiďte se vytváření webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="2795a-103">Use web API conventions</span></span>

<span data-ttu-id="2795a-104">Podle [Pranav Krishnamoorthy](https://github.com/pranavkm) a [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="2795a-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="2795a-105">ASP.NET Core 2.2 a novější zahrnuje způsob, jak extrahovat běžné [dokumentace k rozhraní API](xref:tutorials/web-api-help-pages-using-swagger) a použít ji pro více akcí, řadiče nebo všech řadičů v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="2795a-105">ASP.NET Core 2.2 and later includes a way to extract common [API documentation](xref:tutorials/web-api-help-pages-using-swagger) and apply it to multiple actions, controllers, or all controllers within an assembly.</span></span> <span data-ttu-id="2795a-106">Vytváření webových rozhraní API jsou jako náhrada upravení jednotlivé akce s [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span><span class="sxs-lookup"><span data-stu-id="2795a-106">Web API conventions are a substitute for decorating individual actions with [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span></span>

<span data-ttu-id="2795a-107">Konvence umožňuje:</span><span class="sxs-lookup"><span data-stu-id="2795a-107">A convention allows you to:</span></span>

* <span data-ttu-id="2795a-108">Definujte nejběžnější návratové typy a stavové kódy vrácená z konkrétní typ akce.</span><span class="sxs-lookup"><span data-stu-id="2795a-108">Define the most common return types and status codes returned from a specific type of action.</span></span>
* <span data-ttu-id="2795a-109">Určete akce, které se liší od standardu definované.</span><span class="sxs-lookup"><span data-stu-id="2795a-109">Identify actions that deviate from the defined standard.</span></span>

<span data-ttu-id="2795a-110">ASP.NET Core MVC 2.2 a novější obsahuje sadu výchozích konvencí v <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="2795a-110">ASP.NET Core MVC 2.2 and later includes a set of default conventions in <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>.</span></span> <span data-ttu-id="2795a-111">Konvence jsou založeny na kontroler (*ValuesController.cs*) k dispozici v ASP.NET Core **API** šablony projektu.</span><span class="sxs-lookup"><span data-stu-id="2795a-111">The conventions are based on the controller (*ValuesController.cs*) provided in the ASP.NET Core **API** project template.</span></span> <span data-ttu-id="2795a-112">Pokud vaše akce provést tyto vzory se dají v šabloně, měli byste být úspěšného používání výchozích konvencí.</span><span class="sxs-lookup"><span data-stu-id="2795a-112">If your actions follow the patterns in the template, you should be successful using the default conventions.</span></span> <span data-ttu-id="2795a-113">Pokud výchozí konvence nevyhovují vašim potřebám, přečtěte si téma [vytvoření webového rozhraní API konvence](#create-web-api-conventions).</span><span class="sxs-lookup"><span data-stu-id="2795a-113">If the default conventions don't meet your needs, see [Create web API conventions](#create-web-api-conventions).</span></span>

<span data-ttu-id="2795a-114">V době běhu <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> rozumí konvence.</span><span class="sxs-lookup"><span data-stu-id="2795a-114">At runtime, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> understands conventions.</span></span> <span data-ttu-id="2795a-115">`ApiExplorer` je abstraktní MVC ke komunikaci s [OpenAPI](https://www.openapis.org/) (také označované jako Swagger) dokumentu generátorů.</span><span class="sxs-lookup"><span data-stu-id="2795a-115">`ApiExplorer` is MVC's abstraction to communicate with [OpenAPI](https://www.openapis.org/) (also known as Swagger) document generators.</span></span> <span data-ttu-id="2795a-116">Atributy použité konvence jsou spojeny s akcí a jsou zahrnuty v dokumentaci k OpenAPI akce.</span><span class="sxs-lookup"><span data-stu-id="2795a-116">Attributes from the applied convention are associated with an action and are included in the action's OpenAPI documentation.</span></span> <span data-ttu-id="2795a-117">[Rozhraní API analyzátorů](xref:web-api/advanced/analyzers) srozuměni konvence.</span><span class="sxs-lookup"><span data-stu-id="2795a-117">[API analyzers](xref:web-api/advanced/analyzers) also understand conventions.</span></span> <span data-ttu-id="2795a-118">Pokud je vaše akce neobvyklé (například vrátí stavový kód, který nebyl zdokumentován použité konvence), upozornění doporučuje dokumentu stavový kód.</span><span class="sxs-lookup"><span data-stu-id="2795a-118">If your action is unconventional (for example, it returns a status code that isn't documented by the applied convention), a warning encourages you to document the status code.</span></span>

<span data-ttu-id="2795a-119">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2795a-119">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="apply-web-api-conventions"></a><span data-ttu-id="2795a-120">Použít konvence webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="2795a-120">Apply web API conventions</span></span>

<span data-ttu-id="2795a-121">Konvence není compose; Každá akce může být spojen s konvence přesně jeden.</span><span class="sxs-lookup"><span data-stu-id="2795a-121">Conventions don't compose; each action may be associated with exactly one convention.</span></span> <span data-ttu-id="2795a-122">Konkrétnější konvence mají přednost před specifické pro less konvence.</span><span class="sxs-lookup"><span data-stu-id="2795a-122">More specific conventions take precedence over less specific conventions.</span></span> <span data-ttu-id="2795a-123">Výběr je Nedeterministický, když dva nebo více konvence stejnou prioritou použijete akci.</span><span class="sxs-lookup"><span data-stu-id="2795a-123">The selection is non-deterministic when two or more conventions of the same priority apply to an action.</span></span> <span data-ttu-id="2795a-124">Existují tyto možnosti použít konvenci akci, od nejkonkrétnější po nejméně konkrétní:</span><span class="sxs-lookup"><span data-stu-id="2795a-124">The following options exist to apply a convention to an action, from the most specific to the least specific:</span></span>

1. <span data-ttu-id="2795a-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Platí pro jednotlivé akce a určuje konvence typ a metodu konvence, která se použije.</span><span class="sxs-lookup"><span data-stu-id="2795a-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Applies to individual actions and specifies the convention type and the convention method that applies.</span></span>

    <span data-ttu-id="2795a-126">V následujícím příkladu, výchozí typ konvence společnosti `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` konvence metoda platí pro `Update` akce:</span><span class="sxs-lookup"><span data-stu-id="2795a-126">In the following example, the default convention type's `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method is applied to the `Update` action:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    <span data-ttu-id="2795a-127">`Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` Metoda konvence platí následující atributy pro akci:</span><span class="sxs-lookup"><span data-stu-id="2795a-127">The `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method applies the following attributes to the action:</span></span>

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(204)]
    [ProducesResponseType(404)]
    [ProducesResponseType(400)]
    ```

<span data-ttu-id="2795a-128">Další informace o `[ProducesDefaultResponseType]`, naleznete v tématu [výchozí odpověď](https://swagger.io/docs/specification/describing-responses/#default).</span><span class="sxs-lookup"><span data-stu-id="2795a-128">For more information on `[ProducesDefaultResponseType]`, see [Default Response](https://swagger.io/docs/specification/describing-responses/#default).</span></span>

1. <span data-ttu-id="2795a-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` použijí pro kontroler &mdash; typ zadané konvence se vztahuje na všechny akce v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2795a-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to a controller &mdash; Applies the specified convention type to all actions on the controller.</span></span> <span data-ttu-id="2795a-130">Metoda konvence je upravený pomocí tipů, které určují akce, u kterých bude použito metodu konvence.</span><span class="sxs-lookup"><span data-stu-id="2795a-130">A convention method is decorated with hints that determine the actions to which the convention method applies.</span></span> <span data-ttu-id="2795a-131">Další informace o pomocných parametrů najdete v tématu [vytvoření webového rozhraní API konvence](#create-web-api-conventions)).</span><span class="sxs-lookup"><span data-stu-id="2795a-131">For more information on hints, see [Create web API conventions](#create-web-api-conventions)).</span></span>

    <span data-ttu-id="2795a-132">V následujícím příkladu se použije výchozí sadu pravidel pro všechny akce v *ContactsConventionController*:</span><span class="sxs-lookup"><span data-stu-id="2795a-132">In the following example, the default set of conventions is applied to all actions in *ContactsConventionController*:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. <span data-ttu-id="2795a-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` použité u sestavení &mdash; typ zadané konvence se vztahuje na všechny řadiče v aktuálním sestavení.</span><span class="sxs-lookup"><span data-stu-id="2795a-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to an assembly &mdash; Applies the specified convention type to all controllers in the current assembly.</span></span> <span data-ttu-id="2795a-134">Jako doporučení, použijte atributy úrovně sestavení v *Startup.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="2795a-134">As a recommendation, apply assembly-level attributes in the *Startup.cs* file.</span></span>

    <span data-ttu-id="2795a-135">V následujícím příkladu se použije výchozí sadu konvence pro všemi řadiči v sestavení:</span><span class="sxs-lookup"><span data-stu-id="2795a-135">In the following example, the default set of conventions is applied to all controllers in the assembly:</span></span>

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a><span data-ttu-id="2795a-136">Vytvoření webového rozhraní API konvence</span><span class="sxs-lookup"><span data-stu-id="2795a-136">Create web API conventions</span></span>

<span data-ttu-id="2795a-137">Pokud výchozích konvencí API nevyhovují vašim potřebám, vytvořte vlastní zásady odvíjející.</span><span class="sxs-lookup"><span data-stu-id="2795a-137">If the default API conventions don't meet your needs, create your own conventions.</span></span> <span data-ttu-id="2795a-138">Konvence je:</span><span class="sxs-lookup"><span data-stu-id="2795a-138">A convention is:</span></span>

* <span data-ttu-id="2795a-139">Statického typu metody.</span><span class="sxs-lookup"><span data-stu-id="2795a-139">A static type with methods.</span></span>
* <span data-ttu-id="2795a-140">Dokáže definování [typů odpovědi](#response-types) a [požadavkům na názvy](#naming-requirements) na akce.</span><span class="sxs-lookup"><span data-stu-id="2795a-140">Capable of defining [response types](#response-types) and [naming requirements](#naming-requirements) on actions.</span></span>

### <a name="response-types"></a><span data-ttu-id="2795a-141">Typy odezvy</span><span class="sxs-lookup"><span data-stu-id="2795a-141">Response types</span></span>

<span data-ttu-id="2795a-142">Tyto metody jsou opatřeny poznámkami s `[ProducesResponseType]` nebo `[ProducesDefaultResponseType]` atributy.</span><span class="sxs-lookup"><span data-stu-id="2795a-142">These methods are annotated with `[ProducesResponseType]` or `[ProducesDefaultResponseType]` attributes.</span></span> <span data-ttu-id="2795a-143">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2795a-143">For example:</span></span>

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(200)]
    [ProducesResponseType(404)]
    public static void Find(int id)
    {
    }
}
```

<span data-ttu-id="2795a-144">Pokud chybí konkrétnější atributy metadat, vynucuje použití Tato konvence pro sestavení, který:</span><span class="sxs-lookup"><span data-stu-id="2795a-144">If more specific metadata attributes are absent, applying this convention to an assembly enforces that:</span></span>

* <span data-ttu-id="2795a-145">Metoda konvence platí pro všechny akce s názvem `Find`.</span><span class="sxs-lookup"><span data-stu-id="2795a-145">The convention method applies to any action named `Find`.</span></span>
* <span data-ttu-id="2795a-146">Parametr s názvem `id` je k dispozici na `Find` akce.</span><span class="sxs-lookup"><span data-stu-id="2795a-146">A parameter named `id` is present on the `Find` action.</span></span>

### <a name="naming-requirements"></a><span data-ttu-id="2795a-147">Požadavky na pojmenování</span><span class="sxs-lookup"><span data-stu-id="2795a-147">Naming requirements</span></span>

<span data-ttu-id="2795a-148">`[ApiConventionNameMatch]` a `[ApiConventionTypeMatch]` atributy lze použít pro metodu vytváření názvů, který určuje akce, na které se vztahují.</span><span class="sxs-lookup"><span data-stu-id="2795a-148">The `[ApiConventionNameMatch]` and `[ApiConventionTypeMatch]` attributes can be applied to the convention method that determines the actions to which they apply.</span></span> <span data-ttu-id="2795a-149">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2795a-149">For example:</span></span>

```csharp
[ProducesResponseType(200)]
[ProducesResponseType(404)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

<span data-ttu-id="2795a-150">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2795a-150">In the preceding example:</span></span>

* <span data-ttu-id="2795a-151">`Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` Možnost použít pro metodu označuje, že odpovídá konvenci žádnou akci s předponou "Najít".</span><span class="sxs-lookup"><span data-stu-id="2795a-151">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` option applied to the method indicates that the convention matches any action prefixed with "Find".</span></span> <span data-ttu-id="2795a-152">Příklady odpovídající akce `Find`, `FindPet`, a `FindById`.</span><span class="sxs-lookup"><span data-stu-id="2795a-152">Examples of matching actions include `Find`, `FindPet`, and `FindById`.</span></span>
* <span data-ttu-id="2795a-153">`Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` Použitý pro parametr označuje, že odpovídá konvenci metody s koncovkou identifikátorem přípony přesně jeden parametr.</span><span class="sxs-lookup"><span data-stu-id="2795a-153">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` applied to the parameter indicates that the convention matches methods with exactly one parameter ending in the suffix identifier.</span></span> <span data-ttu-id="2795a-154">Příklady zahrnují parametry, jako například `id` nebo `petId`.</span><span class="sxs-lookup"><span data-stu-id="2795a-154">Examples include parameters such as `id` or `petId`.</span></span> <span data-ttu-id="2795a-155">`ApiConventionTypeMatch` Podobně lze na typy k omezení parametru typu.</span><span class="sxs-lookup"><span data-stu-id="2795a-155">`ApiConventionTypeMatch` can be similarly applied to types to constrain the parameter type.</span></span> <span data-ttu-id="2795a-156">A `params[]` argument určuje zbývající parametry, které není potřeba explicitně odpovídat.</span><span class="sxs-lookup"><span data-stu-id="2795a-156">A `params[]` argument indicates remaining parameters that don't need to be explicitly matched.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2795a-157">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2795a-157">Additional resources</span></span>

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
