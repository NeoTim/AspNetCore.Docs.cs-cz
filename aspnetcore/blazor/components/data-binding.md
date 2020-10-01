---
title: ASP.NET Core Blazor datové vazby
author: guardrex
description: Přečtěte si o funkcích datových vazeb pro komponenty a prvky modelu DOM v Blazor aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 3f823ca9cf96b7ff439ade59f946db222b7f7e60
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606691"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="81545-103">ASP.NET Core Blazor datové vazby</span><span class="sxs-lookup"><span data-stu-id="81545-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="81545-104">Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="81545-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="81545-105">Razor komponenty poskytují funkce datových vazeb prostřednictvím atributu elementu HTML s názvem [`@bind`](xref:mvc/views/razor#bind) pole, vlastnosti nebo Razor hodnoty výrazu.</span><span class="sxs-lookup"><span data-stu-id="81545-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="81545-106">Následující příklad vytvoří vazby `<input>` elementu k `currentValue` poli a `<input>` elementu na `CurrentValue` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="81545-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="81545-107">Pokud jeden z prvků ztratí fokus, je jeho vázané pole nebo vlastnost aktualizováno.</span><span class="sxs-lookup"><span data-stu-id="81545-107">When one of the elements loses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="81545-108">Textové pole je aktualizováno v uživatelském rozhraní pouze v případě, že je komponenta vykreslena, nikoli v reakci na změnu hodnoty pole nebo vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="81545-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="81545-109">Vzhledem k tomu, že se komponenty vykreslují po spuštění kódu obslužné rutiny události, se aktualizace polí a vlastností *obvykle* projeví v uživatelském rozhraní hned po aktivaci obslužné rutiny události.</span><span class="sxs-lookup"><span data-stu-id="81545-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="81545-110">Použití [`@bind`](xref:mvc/views/razor#bind) s `CurrentValue` vlastností ( `<input @bind="CurrentValue" />` ) je v podstatě ekvivalentem následujícího:</span><span class="sxs-lookup"><span data-stu-id="81545-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="81545-111">Při vykreslení komponenty `value` z vlastnosti input element přichází `CurrentValue` .</span><span class="sxs-lookup"><span data-stu-id="81545-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="81545-112">Když uživatel zadá do textového pole a změní fokus prvku, `onchange` událost je aktivována a `CurrentValue` vlastnost je nastavena na změněnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="81545-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="81545-113">Ve skutečnosti je generování kódu složitější než, protože [`@bind`](xref:mvc/views/razor#bind) zpracovává případy, kde jsou prováděny převody typu.</span><span class="sxs-lookup"><span data-stu-id="81545-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="81545-114">V zásadě [`@bind`](xref:mvc/views/razor#bind) přidruží aktuální hodnotu výrazu k `value` atributu a zpracovává změny pomocí registrované obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="81545-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="81545-115">Navázání vlastnosti nebo pole na jiné události také zahrnutím `@bind:event` atributu s `event` parametrem.</span><span class="sxs-lookup"><span data-stu-id="81545-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="81545-116">Následující příklad váže `CurrentValue` vlastnost na `oninput` událost:</span><span class="sxs-lookup"><span data-stu-id="81545-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="81545-117">Na rozdíl od `onchange` , která je aktivována, když prvek ztratí fokus, `oninput` je aktivována při změně hodnoty textového pole.</span><span class="sxs-lookup"><span data-stu-id="81545-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

<span data-ttu-id="81545-118">Vazba atributů rozlišuje velká a malá písmena:</span><span class="sxs-lookup"><span data-stu-id="81545-118">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="81545-119">`@bind` je platný.</span><span class="sxs-lookup"><span data-stu-id="81545-119">`@bind` is valid.</span></span>
* <span data-ttu-id="81545-120">`@Bind` a `@BIND` jsou neplatné.</span><span class="sxs-lookup"><span data-stu-id="81545-120">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="81545-121">Hodnoty, které nelze analyzovat</span><span class="sxs-lookup"><span data-stu-id="81545-121">Unparsable values</span></span>

<span data-ttu-id="81545-122">Když uživatel poskytne neanalyzovatelné hodnoty prvku DataBound, hodnota neanalyzovat se automaticky vrátí na předchozí hodnotu, když se aktivuje událost BIND.</span><span class="sxs-lookup"><span data-stu-id="81545-122">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="81545-123">Představte si následující scénář:</span><span class="sxs-lookup"><span data-stu-id="81545-123">Consider the following scenario:</span></span>

* <span data-ttu-id="81545-124">`<input>`Element je svázán `int` s typem s počáteční hodnotou `123` :</span><span class="sxs-lookup"><span data-stu-id="81545-124">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="81545-125">Uživatel aktualizuje hodnotu prvku na `123.45` stránce a změní fokus prvku.</span><span class="sxs-lookup"><span data-stu-id="81545-125">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="81545-126">V předchozím scénáři je hodnota elementu vrácena na `123` .</span><span class="sxs-lookup"><span data-stu-id="81545-126">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="81545-127">Pokud je hodnota `123.45` zamítnuta ve prospěch původní hodnoty `123` , uživateli se rozumí, že jejich hodnota nebyla přijata.</span><span class="sxs-lookup"><span data-stu-id="81545-127">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="81545-128">Ve výchozím nastavení se vazba vztahuje na událost elementu `onchange` ( `@bind="{PROPERTY OR FIELD}"` ).</span><span class="sxs-lookup"><span data-stu-id="81545-128">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="81545-129">Slouží `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` ke spuštění vazby pro jinou událost.</span><span class="sxs-lookup"><span data-stu-id="81545-129">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="81545-130">Pro `oninput` událost ( `@bind:event="oninput"` ) se reverze provádí po stisknutí klávesy, která zavádí neanalyzovatelné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="81545-130">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="81545-131">Při cílení `oninput` události s `int` typem vázaného na uživatele je znemožněno zadání `.` znaku.</span><span class="sxs-lookup"><span data-stu-id="81545-131">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="81545-132">`.`Znak je okamžitě odstraněn, takže uživatel obdrží okamžitou zpětnou vazbu, že jsou povolena pouze celá čísla.</span><span class="sxs-lookup"><span data-stu-id="81545-132">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="81545-133">K dispozici jsou situace, kdy vrácení hodnoty na `oninput` událost není ideální, například pokud by měl uživatel povoleno vymazat neanalyzovatelné `<input>` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="81545-133">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="81545-134">K alternativám patří:</span><span class="sxs-lookup"><span data-stu-id="81545-134">Alternatives include:</span></span>

* <span data-ttu-id="81545-135">Nepoužívejte `oninput` událost.</span><span class="sxs-lookup"><span data-stu-id="81545-135">Don't use the `oninput` event.</span></span> <span data-ttu-id="81545-136">Použijte výchozí `onchange` událost (pouze zadat `@bind="{PROPERTY OR FIELD}"` ), kde není platná hodnota vrácena, dokud prvek neztratí fokus.</span><span class="sxs-lookup"><span data-stu-id="81545-136">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="81545-137">Umožňuje vytvořit propojení s typem s možnou hodnotou null, například `int?` nebo `string` a poskytnout vlastní logiku pro zpracování neplatných položek.</span><span class="sxs-lookup"><span data-stu-id="81545-137">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="81545-138">Použijte [součást pro ověření formuláře](xref:blazor/forms-validation), jako je například <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> nebo <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="81545-138">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="81545-139">Komponenty ověřování formuláře mají integrovanou podporu pro správu neplatných vstupů.</span><span class="sxs-lookup"><span data-stu-id="81545-139">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="81545-140">Další informace naleznete v tématu <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="81545-140">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="81545-141">Součásti pro ověření formuláře:</span><span class="sxs-lookup"><span data-stu-id="81545-141">Form validation components:</span></span>
  * <span data-ttu-id="81545-142">Povolí uživateli zadání neplatných vstupů a přijetí chyb ověřování na přidruženém <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="81545-142">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="81545-143">Zobrazí chyby ověřování v uživatelském rozhraní, aniž by došlo ke konfliktu s uživatelem, který zadává další data z formuláře.</span><span class="sxs-lookup"><span data-stu-id="81545-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="81545-144">Řetězce formátu</span><span class="sxs-lookup"><span data-stu-id="81545-144">Format strings</span></span>

<span data-ttu-id="81545-145">Datové vazby fungují s <xref:System.DateTime> řetězci formátu pomocí `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="81545-145">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="81545-146">Jiné formátovací výrazy, jako je například Měna nebo formáty čísel, nejsou v tuto chvíli k dispozici.</span><span class="sxs-lookup"><span data-stu-id="81545-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="81545-147">V předchozím kódu je `<input>` výchozí typ pole ( `type` ) elementu `text` .</span><span class="sxs-lookup"><span data-stu-id="81545-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="81545-148">`@bind:format` je podporováno pro vázání následujících typů rozhraní .NET:</span><span class="sxs-lookup"><span data-stu-id="81545-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="81545-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="81545-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="81545-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="81545-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="81545-151">`@bind:format`Atribut určuje formát data, který se má použít pro `value` `<input>` element.</span><span class="sxs-lookup"><span data-stu-id="81545-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="81545-152">Formát je také použit k analýze hodnoty při `onchange` výskytu události.</span><span class="sxs-lookup"><span data-stu-id="81545-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="81545-153">Zadání formátu pro `date` Typ pole se nedoporučuje, protože Blazor má vestavěnou podporu pro formátování kalendářních dat.</span><span class="sxs-lookup"><span data-stu-id="81545-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="81545-154">Navzdory doporučení použijte pouze `yyyy-MM-dd` formát data pro vazbu, aby správně fungoval, pokud je zadán `date` Typ pole:</span><span class="sxs-lookup"><span data-stu-id="81545-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="81545-155">Vazba nadřazený-to-podřízený s parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="81545-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="81545-156">Parametry součásti umožňují svázat vlastnosti a pole nadřazené komponenty se `@bind-{PROPERTY OR FIELD}` syntaxí.</span><span class="sxs-lookup"><span data-stu-id="81545-156">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="81545-157">Následující `Child` součást ( `Shared/Child.razor` ) má `Year` parametr komponenty a `YearChanged` zpětné volání:</span><span class="sxs-lookup"><span data-stu-id="81545-157">The following `Child` component (`Shared/Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="81545-158">Zpětné volání ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ) musí být pojmenováno jako název parametru součásti následovaný `Changed` příponou ( `{PARAMETER NAME}Changed` ).</span><span class="sxs-lookup"><span data-stu-id="81545-158">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="81545-159">V předchozím příkladu má zpětné volání název `YearChanged` .</span><span class="sxs-lookup"><span data-stu-id="81545-159">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="81545-160">Další informace o <xref:Microsoft.AspNetCore.Components.EventCallback%601> naleznete v tématu <xref:blazor/components/event-handling#eventcallback> .</span><span class="sxs-lookup"><span data-stu-id="81545-160">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="81545-161">V následující `Parent` součásti ( `Parent.razor` ) `year` je pole svázáno s `Year` parametrem podřízené komponenty:</span><span class="sxs-lookup"><span data-stu-id="81545-161">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="81545-162">`Year`Parametr je svázán, protože má doprovodnou `YearChanged` událost, která odpovídá typu `Year` parametru.</span><span class="sxs-lookup"><span data-stu-id="81545-162">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="81545-163">Podle konvence může být vlastnost vázána na odpovídající obslužnou rutinu události zahrnutím `@bind-{PROPERTY}:event` atributu přiřazeného k obslužné rutině.</span><span class="sxs-lookup"><span data-stu-id="81545-163">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="81545-164">`<Child @bind-Year="year" />` je ekvivalentem zápisu:</span><span class="sxs-lookup"><span data-stu-id="81545-164">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="81545-165">Vazba podřízenosti k nadřazené položce s zřetězenou vazbou</span><span class="sxs-lookup"><span data-stu-id="81545-165">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="81545-166">Běžným scénářem je zřetězení parametru vázaného na data na element stránky ve výstupu součásti.</span><span class="sxs-lookup"><span data-stu-id="81545-166">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="81545-167">Tento scénář se nazývá *zřetězená vazba* , protože k více úrovním vazby dochází současně.</span><span class="sxs-lookup"><span data-stu-id="81545-167">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="81545-168">Zřetězenou BIND nelze implementovat s [`@bind`](xref:mvc/views/razor#bind) syntaxí v podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="81545-168">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="81545-169">Obslužná rutina události a hodnota se musí zadat samostatně.</span><span class="sxs-lookup"><span data-stu-id="81545-169">The event handler and value must be specified separately.</span></span> <span data-ttu-id="81545-170">Nadřazená komponenta však může použít [`@bind`](xref:mvc/views/razor#bind) syntaxi s parametrem podřízené komponenty.</span><span class="sxs-lookup"><span data-stu-id="81545-170">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the child component's parameter.</span></span>

<span data-ttu-id="81545-171">Následující `PasswordField` součást ( `PasswordField.razor` ):</span><span class="sxs-lookup"><span data-stu-id="81545-171">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="81545-172">Nastaví `<input>` hodnotu prvku na `password` pole.</span><span class="sxs-lookup"><span data-stu-id="81545-172">Sets an `<input>` element's value to a `password` field.</span></span>
* <span data-ttu-id="81545-173">Zpřístupňuje změny `Password` vlastnosti nadřazené komponentě [`EventCallback`](xref:blazor/components/event-handling#eventcallback) , která se předává do aktuální hodnoty `password` pole dítěte jako argument.</span><span class="sxs-lookup"><span data-stu-id="81545-173">Exposes changes of a `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback) that passes in the current value of the child's `password` field as its argument.</span></span>
* <span data-ttu-id="81545-174">Používá `onclick` událost k aktivaci `ToggleShowPassword` metody.</span><span class="sxs-lookup"><span data-stu-id="81545-174">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="81545-175">Další informace naleznete v tématu <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="81545-175">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="81545-176">`PasswordField`Komponenta se používá v jiné součásti:</span><span class="sxs-lookup"><span data-stu-id="81545-176">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="81545-177">Provede kontroly nebo chyby depeší v metodě, která vyvolá delegáta vazby.</span><span class="sxs-lookup"><span data-stu-id="81545-177">Perform checks or trap errors in the method that invokes the binding's delegate.</span></span> <span data-ttu-id="81545-178">Následující příklad poskytuje okamžitou zpětnou vazbu uživateli, pokud se v hodnotě hesla používá mezera:</span><span class="sxs-lookup"><span data-stu-id="81545-178">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="bind-across-more-than-two-components"></a><span data-ttu-id="81545-179">Vazba napříč více než dvěma komponentami</span><span class="sxs-lookup"><span data-stu-id="81545-179">Bind across more than two components</span></span>

<span data-ttu-id="81545-180">Můžete vytvořit propojení s libovolným počtem vnořených komponent, ale musíte se s ním zacházet s jednosměrným tokem dat:</span><span class="sxs-lookup"><span data-stu-id="81545-180">You can bind through any number of nested components, but you must respect the one-way flow of data:</span></span>

* <span data-ttu-id="81545-181">Průběh oznámení změn *v hierarchii*.</span><span class="sxs-lookup"><span data-stu-id="81545-181">Change notifications *flow up the hierarchy*.</span></span>
* <span data-ttu-id="81545-182">Nové hodnoty parametrů *flowují hierarchii*.</span><span class="sxs-lookup"><span data-stu-id="81545-182">New parameter values *flow down the hierarchy*.</span></span>

<span data-ttu-id="81545-183">Běžným a doporučeným přístupem je ukládat pouze podkladová data v nadřazené komponentě, aby nedocházelo k nejasnostem o tom, jaký stav se musí aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="81545-183">A common and recommended approach is to only store the underlying data in the parent component to avoid any confusion about what state must be updated.</span></span>

<span data-ttu-id="81545-184">Následující komponenty ukazují předchozí koncepty:</span><span class="sxs-lookup"><span data-stu-id="81545-184">The following components demonstrate the preceding concepts:</span></span>

<span data-ttu-id="81545-185">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="81545-185">`ParentComponent.razor`:</span></span>

```razor
<h1>Parent Component</h1>

<p>Parent Property: <b>@parentValue</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-Property="parentValue" />

@code {
    private string parentValue = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentValue = $"Set in Parent {DateTime.Now}";
    }
}
```

<span data-ttu-id="81545-186">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="81545-186">`ChildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-Property="BoundValue" />
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private string BoundValue
    {
        get => Property;
        set => PropertyChanged.InvokeAsync(value);
    }

    private Task ChangeValue()
    {
        return PropertyChanged.InvokeAsync($"Set in Child {DateTime.Now}");
    }
}
```

<span data-ttu-id="81545-187">`GrandchildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="81545-187">`GrandchildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private Task ChangeValue()
    {
        return PropertyChanged.InvokeAsync($"Set in Grandchild {DateTime.Now}");
    }
}
```

<span data-ttu-id="81545-188">Alternativní postup, který je vhodný pro sdílení dat v paměti napříč komponentami, které nejsou nutně vnořené, najdete v tématu <xref:blazor/state-management#in-memory-state-container-service> .</span><span class="sxs-lookup"><span data-stu-id="81545-188">For an alternative approach suited to sharing data in-memory across components that aren't necessarily nested, see <xref:blazor/state-management#in-memory-state-container-service>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="81545-189">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="81545-189">Additional resources</span></span>

* [<span data-ttu-id="81545-190">Vazba na přepínače ve formuláři</span><span class="sxs-lookup"><span data-stu-id="81545-190">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="81545-191">Vazba `<select>` možností elementu na hodnoty objektu C# `null` ve formuláři</span><span class="sxs-lookup"><span data-stu-id="81545-191">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
