---
title: Řiďte se vytváření webového rozhraní API
author: pranavkm
description: Další informace o vytváření webových rozhraní API v ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: pranavkm
ms.custom: mvc
ms.date: 11/13/2018
uid: web-api/advanced/conventions
ms.openlocfilehash: 023b8d09511aa42966e2a7d1c85e407bb6e79b0f
ms.sourcegitcommit: f202864efca81a72ea7120c0692940c40d9d0630
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51635363"
---
# <a name="use-web-api-conventions"></a><span data-ttu-id="0cd4b-103">Řiďte se vytváření webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="0cd4b-103">Use web API conventions</span></span>

<span data-ttu-id="0cd4b-104">ASP.NET Core 2.2 představuje způsob, jak extrahovat běžné [dokumentace k rozhraní API](xref:tutorials/web-api-help-pages-using-swagger) a použít ji pro více akcí, řadiče nebo všech řadičů v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-104">ASP.NET Core 2.2 introduces a way to extract common [API documentation](xref:tutorials/web-api-help-pages-using-swagger) and apply it to multiple actions, controllers, or all controllers within an assembly.</span></span> <span data-ttu-id="0cd4b-105">Vytváření webových rozhraní API jsou jako náhrada upravení jednotlivé akce s [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span><span class="sxs-lookup"><span data-stu-id="0cd4b-105">Web API conventions are a substitute for decorating individual actions with [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span></span> <span data-ttu-id="0cd4b-106">Umožňuje definovat nejběžnější "konvenční" návratové typy a stavové kódy, které vracejí z akci se způsob, jak vybrat metodu vytváření názvů, který se vztahuje na akce.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-106">It allows you to define the most common "conventional" return types and status codes that you return from your action with a way to select the convention method that applies to an action.</span></span>

<span data-ttu-id="0cd4b-107">Ve výchozím nastavení, ASP.NET Core MVC 2.2 dodává se sadou výchozích konvencí `Microsoft.AspNetCore.Mvc.DefaultApiConventions`.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-107">By default, ASP.NET Core MVC 2.2 ships with a set of default conventions, `Microsoft.AspNetCore.Mvc.DefaultApiConventions`.</span></span> <span data-ttu-id="0cd4b-108">Konvence jsou založeny na kontroler, který vygeneruje uživatelské rozhraní ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-108">The conventions are based on the controller that ASP.NET Core scaffolds.</span></span> <span data-ttu-id="0cd4b-109">Pokud vaše akce mají tvar vytváří tento generování uživatelského rozhraní, byste měli úspěšného používání výchozích konvencí.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-109">If your actions follow the pattern that scaffolding produces, you should be successful using the default conventions.</span></span>

<span data-ttu-id="0cd4b-110">V době běhu <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> rozumí konvence.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-110">At runtime, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> understands conventions.</span></span> <span data-ttu-id="0cd4b-111">`ApiExplorer` je abstraktní MVC ke komunikaci s generátorů dokumentu Open API.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-111">`ApiExplorer` is MVC's abstraction to communicate with Open API document generators.</span></span> <span data-ttu-id="0cd4b-112">Atributy použité konvence k akci a jsou zahrnuty v dokumentaci k akce Swaggeru.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-112">Attributes from the applied convention get associated with an action and are included in the action's Swagger documentation.</span></span> <span data-ttu-id="0cd4b-113">Rozhraní API analyzátorů srozuměni konvence.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-113">API analyzers also understand conventions.</span></span> <span data-ttu-id="0cd4b-114">Pokud je vaše akce neobvyklé (například vrátí stavový kód, který nebyl zdokumentován použité konvence), vyvolá upozornění, podporu vám ho zdokumentovat.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-114">If your action is unconventional (for example, it returns a status code that isn't documented by the applied convention), it produces a warning, encouraging you to document it.</span></span>

<span data-ttu-id="0cd4b-115">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0cd4b-115">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="apply-web-api-conventions"></a><span data-ttu-id="0cd4b-116">Použít konvence webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="0cd4b-116">Apply web API conventions</span></span>

<span data-ttu-id="0cd4b-117">Existují tři způsoby, jak použít konvence.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-117">There are three ways to apply a convention.</span></span> <span data-ttu-id="0cd4b-118">Konvence není compose, každá akce může být spojen s konvence přesně jeden.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-118">Conventions don't compose, each action may be associated with exactly one convention.</span></span> <span data-ttu-id="0cd4b-119">Konkrétnější konvence (podrobnosti jsou dole) mají přednost před těmi méně konkrétní.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-119">More specific conventions (detailed below) take precedence over less specific ones.</span></span> <span data-ttu-id="0cd4b-120">Výběr je Nedeterministický, když dva nebo více konvence stejnou prioritou použijete akci.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-120">The selection is non-deterministic when two or more conventions of the same priority apply to an action.</span></span> <span data-ttu-id="0cd4b-121">Existují tyto možnosti použít konvenci akci, od nejkonkrétnější po nejméně konkrétní:</span><span class="sxs-lookup"><span data-stu-id="0cd4b-121">The following options exist to apply a convention to an action, from the most specific to the least specific:</span></span>

1. <span data-ttu-id="0cd4b-122">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Platí pro jednotlivé akce a určuje konvence typ a metodu konvence, která se použije.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-122">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Applies to individual actions and specifies the convention type and the convention method that applies.</span></span> <span data-ttu-id="0cd4b-123">V následujícím příkladu metoda konvence `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` aplikován `Update` akce:</span><span class="sxs-lookup"><span data-stu-id="0cd4b-123">In the following sample, the convention method `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` is applied to the `Update` action:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=apiconventionmethod&highlight=2-3)]

1. <span data-ttu-id="0cd4b-124">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` použijí pro kontroler &mdash; konvence typu se vztahuje na všechny akce v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-124">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to a controller &mdash; Applies the convention type to all actions on the controller.</span></span> <span data-ttu-id="0cd4b-125">Metody vytváření názvů jsou vybaveny pomocné parametry, které určují akce, které by použít (podrobnosti jako součást pro vytváření konvencí).</span><span class="sxs-lookup"><span data-stu-id="0cd4b-125">Convention methods are decorated with hints that determine which actions it would apply to (details as part of authoring conventions).</span></span> <span data-ttu-id="0cd4b-126">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0cd4b-126">For example:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=apiconventiontypeattribute)]

1. <span data-ttu-id="0cd4b-127">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` použité u sestavení &mdash; konvence typ se vztahuje na všechny řadiče v aktuálním sestavení.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-127">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to an assembly &mdash; Applies the convention type to all controllers in the current assembly.</span></span> <span data-ttu-id="0cd4b-128">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0cd4b-128">For example:</span></span>

    [!code-csharp[](conventions/sample/Startup.cs?name=apiconventiontypeattribute)]

## <a name="create-web-api-conventions"></a><span data-ttu-id="0cd4b-129">Vytvoření webového rozhraní API konvence</span><span class="sxs-lookup"><span data-stu-id="0cd4b-129">Create web API conventions</span></span>

<span data-ttu-id="0cd4b-130">Konvence je statický typ metody.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-130">A convention is a static type with methods.</span></span> <span data-ttu-id="0cd4b-131">Tyto metody jsou opatřeny poznámkami s `[ProducesResponseType]` nebo `[ProducesDefaultResponseType]` atributy.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-131">These methods are annotated with `[ProducesResponseType]` or `[ProducesDefaultResponseType]` attributes.</span></span>

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

<span data-ttu-id="0cd4b-132">Použití této konvence pro sestavení výsledkem metodu konvence použití na každou akci s názvem `Find` a mít přesně jeden parametr s názvem `id`, za předpokladu, že nemají další konkrétnější atributy metadat.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-132">Applying this convention to an assembly results in the convention method applying to any action with the name `Find` and having exactly one parameter named `id`, as long as they don't have other more specific metadata attributes.</span></span>

<span data-ttu-id="0cd4b-133">Kromě `[ProducesResponseType]` a `[ProducesDefaultResponseType]`, `[ApiConventionNameMatch]` a `[ApiConventionTypeMatch]` můžete použít pro metodu vytváření názvů, který určuje metody, které se vztahují na.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-133">In addition to `[ProducesResponseType]` and `[ProducesDefaultResponseType]`, `[ApiConventionNameMatch]` and `[ApiConventionTypeMatch]` can be applied to the convention method that determines the methods they apply to.</span></span> <span data-ttu-id="0cd4b-134">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0cd4b-134">For example:</span></span>

```csharp
[ProducesResponseType(200)]
[ProducesResponseType(404)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

* <span data-ttu-id="0cd4b-135">`Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` Možnost použít pro metodu, označuje, že tato konvence tak dlouho, dokud je předponu "Najít" odpovídá žádnou akci.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-135">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` option applied to the method, indicates that the convention can match any action as long as it's prefixed with "Find".</span></span> <span data-ttu-id="0cd4b-136">To zahrnuje metody jako `Find`, `FindPet`, nebo `FindById`.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-136">This includes methods such as `Find`, `FindPet`, or `FindById`.</span></span>
* <span data-ttu-id="0cd4b-137">`Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` Použitý pro parametr určuje, že tato konvence odpovídá metody s koncovkou identifikátorem přípony přesně jeden parametr.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-137">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` applied to the parameter indicates that the convention can match methods with exactly one parameter ending in the suffix identifier.</span></span> <span data-ttu-id="0cd4b-138">To zahrnuje parametry jako `id` nebo `petId`.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-138">This includes parameters such as `id` or `petId`.</span></span> <span data-ttu-id="0cd4b-139">`ApiConventionTypeMatch` Podobně lze na typy, chcete-li omezit typu parametru.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-139">`ApiConventionTypeMatch` can be similarly applied to types to constrain the type of the parameter.</span></span> <span data-ttu-id="0cd4b-140">A `params[]` argument můžete použít k označení zbývající parametry, které není potřeba explicitně odpovídat.</span><span class="sxs-lookup"><span data-stu-id="0cd4b-140">A `params[]` argument can be used to indicate remaining parameters that don't need to be explicitly matched.</span></span>
