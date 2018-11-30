---
title: Řiďte se vytváření webového rozhraní API
author: pranavkm
description: Další informace o vytváření webových rozhraní API v ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: pranavkm
ms.custom: mvc
ms.date: 11/13/2018
uid: web-api/advanced/conventions
ms.openlocfilehash: ede9a46c160cf6a49aa93da710af0bf0b8f59acc
ms.sourcegitcommit: c4572be5ebb301013a5698caf9b5572b76cb2e34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52710072"
---
# <a name="use-web-api-conventions"></a><span data-ttu-id="c17a8-103">Řiďte se vytváření webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="c17a8-103">Use web API conventions</span></span>

<span data-ttu-id="c17a8-104">ASP.NET Core 2.2 představuje způsob, jak extrahovat běžné [dokumentace k rozhraní API](xref:tutorials/web-api-help-pages-using-swagger) a použít ji pro více akcí, řadiče nebo všech řadičů v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="c17a8-104">ASP.NET Core 2.2 introduces a way to extract common [API documentation](xref:tutorials/web-api-help-pages-using-swagger) and apply it to multiple actions, controllers, or all controllers within an assembly.</span></span> <span data-ttu-id="c17a8-105">Vytváření webových rozhraní API jsou jako náhrada upravení jednotlivé akce s [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span><span class="sxs-lookup"><span data-stu-id="c17a8-105">Web API conventions are a substitute for decorating individual actions with [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span></span> <span data-ttu-id="c17a8-106">Umožňuje definovat nejběžnější "konvenční" návratové typy a stavové kódy, které vracejí z akci se způsob, jak vybrat metodu vytváření názvů, který se vztahuje na akce.</span><span class="sxs-lookup"><span data-stu-id="c17a8-106">It allows you to define the most common "conventional" return types and status codes that you return from your action with a way to select the convention method that applies to an action.</span></span>

<span data-ttu-id="c17a8-107">Ve výchozím nastavení, ASP.NET Core MVC 2.2 dodává se sadou výchozích konvencí `Microsoft.AspNetCore.Mvc.DefaultApiConventions`.</span><span class="sxs-lookup"><span data-stu-id="c17a8-107">By default, ASP.NET Core MVC 2.2 ships with a set of default conventions, `Microsoft.AspNetCore.Mvc.DefaultApiConventions`.</span></span> <span data-ttu-id="c17a8-108">Konvence jsou založeny na kontroler, který vygeneruje uživatelské rozhraní ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c17a8-108">The conventions are based on the controller that ASP.NET Core scaffolds.</span></span> <span data-ttu-id="c17a8-109">Pokud vaše akce mají tvar vytváří tento generování uživatelského rozhraní, byste měli úspěšného používání výchozích konvencí.</span><span class="sxs-lookup"><span data-stu-id="c17a8-109">If your actions follow the pattern that scaffolding produces, you should be successful using the default conventions.</span></span>

<span data-ttu-id="c17a8-110">V době běhu <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> rozumí konvence.</span><span class="sxs-lookup"><span data-stu-id="c17a8-110">At runtime, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> understands conventions.</span></span> <span data-ttu-id="c17a8-111">`ApiExplorer` je abstraktní MVC ke komunikaci s generátorů dokumentu Open API.</span><span class="sxs-lookup"><span data-stu-id="c17a8-111">`ApiExplorer` is MVC's abstraction to communicate with Open API document generators.</span></span> <span data-ttu-id="c17a8-112">Atributy použité konvence k akci a jsou zahrnuty v dokumentaci k akce Swaggeru.</span><span class="sxs-lookup"><span data-stu-id="c17a8-112">Attributes from the applied convention get associated with an action and are included in the action's Swagger documentation.</span></span> <span data-ttu-id="c17a8-113">Rozhraní API analyzátorů srozuměni konvence.</span><span class="sxs-lookup"><span data-stu-id="c17a8-113">API analyzers also understand conventions.</span></span> <span data-ttu-id="c17a8-114">Pokud je vaše akce neobvyklé (například vrátí stavový kód, který nebyl zdokumentován použité konvence), vyvolá upozornění, podporu vám ho zdokumentovat.</span><span class="sxs-lookup"><span data-stu-id="c17a8-114">If your action is unconventional (for example, it returns a status code that isn't documented by the applied convention), it produces a warning, encouraging you to document it.</span></span>

<span data-ttu-id="c17a8-115">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c17a8-115">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="apply-web-api-conventions"></a><span data-ttu-id="c17a8-116">Použít konvence webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="c17a8-116">Apply web API conventions</span></span>

<span data-ttu-id="c17a8-117">Existují tři způsoby, jak použít konvence.</span><span class="sxs-lookup"><span data-stu-id="c17a8-117">There are three ways to apply a convention.</span></span> <span data-ttu-id="c17a8-118">Konvence není compose.</span><span class="sxs-lookup"><span data-stu-id="c17a8-118">Conventions don't compose.</span></span> <span data-ttu-id="c17a8-119">Každá akce může být spojen s konvence přesně jeden.</span><span class="sxs-lookup"><span data-stu-id="c17a8-119">Each action may be associated with exactly one convention.</span></span> <span data-ttu-id="c17a8-120">Konkrétnější konvence (podrobnosti jsou dole) mají přednost před těmi méně konkrétní.</span><span class="sxs-lookup"><span data-stu-id="c17a8-120">More specific conventions (detailed below) take precedence over less specific ones.</span></span> <span data-ttu-id="c17a8-121">Výběr je Nedeterministický, když dva nebo více konvence stejnou prioritou použijete akci.</span><span class="sxs-lookup"><span data-stu-id="c17a8-121">The selection is non-deterministic when two or more conventions of the same priority apply to an action.</span></span> <span data-ttu-id="c17a8-122">Existují tyto možnosti použít konvenci akci, od nejkonkrétnější po nejméně konkrétní:</span><span class="sxs-lookup"><span data-stu-id="c17a8-122">The following options exist to apply a convention to an action, from the most specific to the least specific:</span></span>

1. <span data-ttu-id="c17a8-123">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Platí pro jednotlivé akce a určuje konvence typ a metodu konvence, která se použije.</span><span class="sxs-lookup"><span data-stu-id="c17a8-123">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Applies to individual actions and specifies the convention type and the convention method that applies.</span></span> <span data-ttu-id="c17a8-124">V následujícím příkladu metoda konvence `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` aplikován `Update` akce:</span><span class="sxs-lookup"><span data-stu-id="c17a8-124">In the following sample, the convention method `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` is applied to the `Update` action:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=apiconventionmethod&highlight=2-3)]

1. <span data-ttu-id="c17a8-125">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` použijí pro kontroler &mdash; konvence typu se vztahuje na všechny akce v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c17a8-125">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to a controller &mdash; Applies the convention type to all actions on the controller.</span></span> <span data-ttu-id="c17a8-126">Metody vytváření názvů jsou vybaveny pomocné parametry, které určují akce, které by použít (podrobnosti jako součást pro vytváření konvencí).</span><span class="sxs-lookup"><span data-stu-id="c17a8-126">Convention methods are decorated with hints that determine which actions it would apply to (details as part of authoring conventions).</span></span> <span data-ttu-id="c17a8-127">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c17a8-127">For example:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=apiconventiontypeattribute)]

1. <span data-ttu-id="c17a8-128">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` použité u sestavení &mdash; konvence typ se vztahuje na všechny řadiče v aktuálním sestavení.</span><span class="sxs-lookup"><span data-stu-id="c17a8-128">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to an assembly &mdash; Applies the convention type to all controllers in the current assembly.</span></span> <span data-ttu-id="c17a8-129">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c17a8-129">For example:</span></span>

    [!code-csharp[](conventions/sample/Startup.cs?name=apiconventiontypeattribute)]

## <a name="create-web-api-conventions"></a><span data-ttu-id="c17a8-130">Vytvoření webového rozhraní API konvence</span><span class="sxs-lookup"><span data-stu-id="c17a8-130">Create web API conventions</span></span>

<span data-ttu-id="c17a8-131">Konvence je statický typ metody.</span><span class="sxs-lookup"><span data-stu-id="c17a8-131">A convention is a static type with methods.</span></span> <span data-ttu-id="c17a8-132">Tyto metody jsou opatřeny poznámkami s `[ProducesResponseType]` nebo `[ProducesDefaultResponseType]` atributy.</span><span class="sxs-lookup"><span data-stu-id="c17a8-132">These methods are annotated with `[ProducesResponseType]` or `[ProducesDefaultResponseType]` attributes.</span></span>

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

<span data-ttu-id="c17a8-133">Použití této konvence pro sestavení výsledkem metodu konvence použití na každou akci s názvem `Find` a mít přesně jeden parametr s názvem `id`, za předpokladu, že nemají další konkrétnější atributy metadat.</span><span class="sxs-lookup"><span data-stu-id="c17a8-133">Applying this convention to an assembly results in the convention method applying to any action with the name `Find` and having exactly one parameter named `id`, as long as they don't have other more specific metadata attributes.</span></span>

<span data-ttu-id="c17a8-134">Kromě `[ProducesResponseType]` a `[ProducesDefaultResponseType]`, `[ApiConventionNameMatch]` a `[ApiConventionTypeMatch]` můžete použít pro metodu vytváření názvů, který určuje metody, které se vztahují na.</span><span class="sxs-lookup"><span data-stu-id="c17a8-134">In addition to `[ProducesResponseType]` and `[ProducesDefaultResponseType]`, `[ApiConventionNameMatch]` and `[ApiConventionTypeMatch]` can be applied to the convention method that determines the methods they apply to.</span></span> <span data-ttu-id="c17a8-135">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c17a8-135">For example:</span></span>

```csharp
[ProducesResponseType(200)]
[ProducesResponseType(404)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

* <span data-ttu-id="c17a8-136">`Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` Možnost použít pro metodu, označuje, že tato konvence tak dlouho, dokud je předponu "Najít" odpovídá žádnou akci.</span><span class="sxs-lookup"><span data-stu-id="c17a8-136">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` option applied to the method, indicates that the convention can match any action as long as it's prefixed with "Find".</span></span> <span data-ttu-id="c17a8-137">To zahrnuje metody jako `Find`, `FindPet`, nebo `FindById`.</span><span class="sxs-lookup"><span data-stu-id="c17a8-137">This includes methods such as `Find`, `FindPet`, or `FindById`.</span></span>
* <span data-ttu-id="c17a8-138">`Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` Použitý pro parametr určuje, že tato konvence odpovídá metody s koncovkou identifikátorem přípony přesně jeden parametr.</span><span class="sxs-lookup"><span data-stu-id="c17a8-138">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` applied to the parameter indicates that the convention can match methods with exactly one parameter ending in the suffix identifier.</span></span> <span data-ttu-id="c17a8-139">To zahrnuje parametry jako `id` nebo `petId`.</span><span class="sxs-lookup"><span data-stu-id="c17a8-139">This includes parameters such as `id` or `petId`.</span></span> <span data-ttu-id="c17a8-140">`ApiConventionTypeMatch` Podobně lze na typy, chcete-li omezit typu parametru.</span><span class="sxs-lookup"><span data-stu-id="c17a8-140">`ApiConventionTypeMatch` can be similarly applied to types to constrain the type of the parameter.</span></span> <span data-ttu-id="c17a8-141">A `params[]` argument můžete použít k označení zbývající parametry, které není potřeba explicitně odpovídat.</span><span class="sxs-lookup"><span data-stu-id="c17a8-141">A `params[]` argument can be used to indicate remaining parameters that don't need to be explicitly matched.</span></span>
