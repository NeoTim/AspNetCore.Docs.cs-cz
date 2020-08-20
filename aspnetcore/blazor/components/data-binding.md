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
ms.openlocfilehash: 3b41aedcbd0d2c22b20d8fa3a21b8af97d1fbb2c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628557"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="4d639-103">ASP.NET Core Blazor datové vazby</span><span class="sxs-lookup"><span data-stu-id="4d639-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="4d639-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="4d639-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="4d639-105">Razor komponenty poskytují funkce datových vazeb prostřednictvím atributu elementu HTML s názvem [`@bind`](xref:mvc/views/razor#bind) pole, vlastnosti nebo Razor hodnoty výrazu.</span><span class="sxs-lookup"><span data-stu-id="4d639-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="4d639-106">Následující příklad vytvoří vazby `<input>` elementu k `currentValue` poli a `<input>` elementu na `CurrentValue` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="4d639-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

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

<span data-ttu-id="4d639-107">Pokud jeden z prvků ztratí fokus, je jeho vázané pole nebo vlastnost aktualizováno.</span><span class="sxs-lookup"><span data-stu-id="4d639-107">When one of the elements looses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="4d639-108">Textové pole je aktualizováno v uživatelském rozhraní pouze v případě, že je komponenta vykreslena, nikoli v reakci na změnu hodnoty pole nebo vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="4d639-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="4d639-109">Vzhledem k tomu, že se komponenty vykreslují po spuštění kódu obslužné rutiny události, se aktualizace polí a vlastností *obvykle* projeví v uživatelském rozhraní hned po aktivaci obslužné rutiny události.</span><span class="sxs-lookup"><span data-stu-id="4d639-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="4d639-110">Použití [`@bind`](xref:mvc/views/razor#bind) s `CurrentValue` vlastností ( `<input @bind="CurrentValue" />` ) je v podstatě ekvivalentem následujícího:</span><span class="sxs-lookup"><span data-stu-id="4d639-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="4d639-111">Při vykreslení komponenty `value` z vlastnosti input element přichází `CurrentValue` .</span><span class="sxs-lookup"><span data-stu-id="4d639-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="4d639-112">Když uživatel zadá do textového pole a změní fokus prvku, `onchange` událost je aktivována a `CurrentValue` vlastnost je nastavena na změněnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4d639-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="4d639-113">Ve skutečnosti je generování kódu složitější než, protože [`@bind`](xref:mvc/views/razor#bind) zpracovává případy, kde jsou prováděny převody typu.</span><span class="sxs-lookup"><span data-stu-id="4d639-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="4d639-114">V zásadě [`@bind`](xref:mvc/views/razor#bind) přidruží aktuální hodnotu výrazu k `value` atributu a zpracovává změny pomocí registrované obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="4d639-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="4d639-115">Navázání vlastnosti nebo pole na jiné události také zahrnutím `@bind:event` atributu s `event` parametrem.</span><span class="sxs-lookup"><span data-stu-id="4d639-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="4d639-116">Následující příklad váže `CurrentValue` vlastnost na `oninput` událost:</span><span class="sxs-lookup"><span data-stu-id="4d639-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="4d639-117">Na rozdíl od `onchange` , která je aktivována, když prvek ztratí fokus, `oninput` je aktivována při změně hodnoty textového pole.</span><span class="sxs-lookup"><span data-stu-id="4d639-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="4d639-118">Použijte `@bind-{ATTRIBUTE}` s `@bind-{ATTRIBUTE}:event` syntaxí pro svázání atributů elementu jiné než `value` .</span><span class="sxs-lookup"><span data-stu-id="4d639-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="4d639-119">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="4d639-119">In the following example:</span></span>

* <span data-ttu-id="4d639-120">Styl odstavce je při načtení součásti () **červený** `style="color:red"` .</span><span class="sxs-lookup"><span data-stu-id="4d639-120">The paragraph's style is **red** when the component loads (`style="color:red"`).</span></span>
* <span data-ttu-id="4d639-121">Uživatel změní hodnotu textového pole tak, aby odrážel jiný styl barvy CSS a změnil fokus prvku stránky.</span><span class="sxs-lookup"><span data-stu-id="4d639-121">The user changes the value of the text box to reflect a different CSS color style and changes the page's element focus.</span></span> <span data-ttu-id="4d639-122">Uživatel například změní hodnotu textového pole na a stiskne klávesu `color:blue` <kbd>TAB</kbd> na klávesnici.</span><span class="sxs-lookup"><span data-stu-id="4d639-122">For example, the user changes the text box value to `color:blue` and presses the <kbd>Tab</kbd> key on the keyboard.</span></span>
* <span data-ttu-id="4d639-123">Když se změní fokus prvku:</span><span class="sxs-lookup"><span data-stu-id="4d639-123">When the element focus changes:</span></span>
  * <span data-ttu-id="4d639-124">Hodnota `paragraphStyle` je přiřazena z `<input>` hodnoty elementu.</span><span class="sxs-lookup"><span data-stu-id="4d639-124">The value of `paragraphStyle` is assigned from the `<input>` element's value.</span></span>
  * <span data-ttu-id="4d639-125">Styl odstavce je aktualizován tak, aby odrážel nový styl v `paragraphStyle` .</span><span class="sxs-lookup"><span data-stu-id="4d639-125">The paragraph style is updated to reflect the new style in `paragraphStyle`.</span></span> <span data-ttu-id="4d639-126">Pokud je styl aktualizován na `color:blue` , změní se barva textu na **modrou**.</span><span class="sxs-lookup"><span data-stu-id="4d639-126">If the style is updated to `color:blue`, the text color changes to **blue**.</span></span>

```razor
<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

<span data-ttu-id="4d639-127">Vazba atributů rozlišuje velká a malá písmena:</span><span class="sxs-lookup"><span data-stu-id="4d639-127">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="4d639-128">`@bind` je platný.</span><span class="sxs-lookup"><span data-stu-id="4d639-128">`@bind` is valid.</span></span>
* <span data-ttu-id="4d639-129">`@Bind` a `@BIND` jsou neplatné.</span><span class="sxs-lookup"><span data-stu-id="4d639-129">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="4d639-130">Hodnoty, které nelze analyzovat</span><span class="sxs-lookup"><span data-stu-id="4d639-130">Unparsable values</span></span>

<span data-ttu-id="4d639-131">Když uživatel poskytne neanalyzovatelné hodnoty prvku DataBound, hodnota neanalyzovat se automaticky vrátí na předchozí hodnotu, když se aktivuje událost BIND.</span><span class="sxs-lookup"><span data-stu-id="4d639-131">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="4d639-132">Představte si následující scénář:</span><span class="sxs-lookup"><span data-stu-id="4d639-132">Consider the following scenario:</span></span>

* <span data-ttu-id="4d639-133">`<input>`Element je svázán `int` s typem s počáteční hodnotou `123` :</span><span class="sxs-lookup"><span data-stu-id="4d639-133">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="4d639-134">Uživatel aktualizuje hodnotu prvku na `123.45` stránce a změní fokus prvku.</span><span class="sxs-lookup"><span data-stu-id="4d639-134">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="4d639-135">V předchozím scénáři je hodnota elementu vrácena na `123` .</span><span class="sxs-lookup"><span data-stu-id="4d639-135">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="4d639-136">Pokud je hodnota `123.45` zamítnuta ve prospěch původní hodnoty `123` , uživateli se rozumí, že jejich hodnota nebyla přijata.</span><span class="sxs-lookup"><span data-stu-id="4d639-136">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="4d639-137">Ve výchozím nastavení se vazba vztahuje na událost elementu `onchange` ( `@bind="{PROPERTY OR FIELD}"` ).</span><span class="sxs-lookup"><span data-stu-id="4d639-137">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="4d639-138">Slouží `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` ke spuštění vazby pro jinou událost.</span><span class="sxs-lookup"><span data-stu-id="4d639-138">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="4d639-139">Pro `oninput` událost ( `@bind:event="oninput"` ) se reverze provádí po stisknutí klávesy, která zavádí neanalyzovatelné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4d639-139">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="4d639-140">Při cílení `oninput` události s `int` typem vázaného na uživatele je znemožněno zadání `.` znaku.</span><span class="sxs-lookup"><span data-stu-id="4d639-140">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="4d639-141">`.`Znak je okamžitě odstraněn, takže uživatel obdrží okamžitou zpětnou vazbu, že jsou povolena pouze celá čísla.</span><span class="sxs-lookup"><span data-stu-id="4d639-141">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="4d639-142">K dispozici jsou situace, kdy vrácení hodnoty na `oninput` událost není ideální, například pokud by měl uživatel povoleno vymazat neanalyzovatelné `<input>` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4d639-142">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="4d639-143">K alternativám patří:</span><span class="sxs-lookup"><span data-stu-id="4d639-143">Alternatives include:</span></span>

* <span data-ttu-id="4d639-144">Nepoužívejte `oninput` událost.</span><span class="sxs-lookup"><span data-stu-id="4d639-144">Don't use the `oninput` event.</span></span> <span data-ttu-id="4d639-145">Použijte výchozí `onchange` událost (pouze zadat `@bind="{PROPERTY OR FIELD}"` ), kde není platná hodnota vrácena, dokud prvek neztratí fokus.</span><span class="sxs-lookup"><span data-stu-id="4d639-145">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="4d639-146">Umožňuje vytvořit propojení s typem s možnou hodnotou null, například `int?` nebo `string` a poskytnout vlastní logiku pro zpracování neplatných položek.</span><span class="sxs-lookup"><span data-stu-id="4d639-146">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="4d639-147">Použijte [součást pro ověření formuláře](xref:blazor/forms-validation), jako je například <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> nebo <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="4d639-147">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="4d639-148">Komponenty ověřování formuláře mají integrovanou podporu pro správu neplatných vstupů.</span><span class="sxs-lookup"><span data-stu-id="4d639-148">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="4d639-149">Další informace naleznete v tématu <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="4d639-149">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="4d639-150">Součásti pro ověření formuláře:</span><span class="sxs-lookup"><span data-stu-id="4d639-150">Form validation components:</span></span>
  * <span data-ttu-id="4d639-151">Povolí uživateli zadání neplatných vstupů a přijetí chyb ověřování na přidruženém <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="4d639-151">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="4d639-152">Zobrazí chyby ověřování v uživatelském rozhraní, aniž by došlo ke konfliktu s uživatelem, který zadává další data z formuláře.</span><span class="sxs-lookup"><span data-stu-id="4d639-152">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="4d639-153">Řetězce formátu</span><span class="sxs-lookup"><span data-stu-id="4d639-153">Format strings</span></span>

<span data-ttu-id="4d639-154">Datové vazby fungují s <xref:System.DateTime> řetězci formátu pomocí `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="4d639-154">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="4d639-155">Jiné formátovací výrazy, jako je například Měna nebo formáty čísel, nejsou v tuto chvíli k dispozici.</span><span class="sxs-lookup"><span data-stu-id="4d639-155">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="4d639-156">V předchozím kódu je `<input>` výchozí typ pole ( `type` ) elementu `text` .</span><span class="sxs-lookup"><span data-stu-id="4d639-156">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="4d639-157">`@bind:format` je podporováno pro vázání následujících typů rozhraní .NET:</span><span class="sxs-lookup"><span data-stu-id="4d639-157">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="4d639-158"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="4d639-158"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="4d639-159"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="4d639-159"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="4d639-160">`@bind:format`Atribut určuje formát data, který se má použít pro `value` `<input>` element.</span><span class="sxs-lookup"><span data-stu-id="4d639-160">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="4d639-161">Formát je také použit k analýze hodnoty při `onchange` výskytu události.</span><span class="sxs-lookup"><span data-stu-id="4d639-161">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="4d639-162">Zadání formátu pro `date` Typ pole se nedoporučuje, protože Blazor má vestavěnou podporu pro formátování kalendářních dat.</span><span class="sxs-lookup"><span data-stu-id="4d639-162">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="4d639-163">Navzdory doporučení použijte pouze `yyyy-MM-dd` formát data pro vazbu, aby správně fungoval, pokud je zadán `date` Typ pole:</span><span class="sxs-lookup"><span data-stu-id="4d639-163">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="4d639-164">Vazba nadřazený-to-podřízený s parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="4d639-164">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="4d639-165">Parametry součásti umožňují svázat vlastnosti a pole nadřazené komponenty se `@bind-{PROPERTY OR FIELD}` syntaxí.</span><span class="sxs-lookup"><span data-stu-id="4d639-165">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="4d639-166">Následující `Child` součást ( `Child.razor` ) má `Year` parametr komponenty a `YearChanged` zpětné volání:</span><span class="sxs-lookup"><span data-stu-id="4d639-166">The following `Child` component (`Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
        <p>
            <button @onclick="UpdateYear">
                Update Child <code>Year</code> and call 
                <code>YearChanged.InvokeAsync(Year)</code>
            </button>
        </p>
    </div>
</div>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private Task UpdateYear()
    {
        Year = r.Next(10050, 12021);

        return YearChanged.InvokeAsync(Year);
    }
}
```

<span data-ttu-id="4d639-167">Zpětné volání ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ) musí být pojmenováno jako název parametru součásti následovaný `Changed` příponou ( `{PARAMETER NAME}Changed` ).</span><span class="sxs-lookup"><span data-stu-id="4d639-167">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="4d639-168">V předchozím příkladu má zpětné volání název `YearChanged` .</span><span class="sxs-lookup"><span data-stu-id="4d639-168">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="4d639-169">Další informace o <xref:Microsoft.AspNetCore.Components.EventCallback%601> naleznete v tématu <xref:blazor/components/event-handling#eventcallback> .</span><span class="sxs-lookup"><span data-stu-id="4d639-169">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="4d639-170">V následující `Parent` součásti ( `Parent.razor` ) `year` je pole svázáno s `Year` parametrem podřízené komponenty:</span><span class="sxs-lookup"><span data-stu-id="4d639-170">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1978;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="4d639-171">`Year`Parametr je svázán, protože má doprovodnou `YearChanged` událost, která odpovídá typu `Year` parametru.</span><span class="sxs-lookup"><span data-stu-id="4d639-171">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="4d639-172">Podle konvence může být vlastnost vázána na odpovídající obslužnou rutinu události zahrnutím `@bind-{PROPERTY}:event` atributu přiřazeného k obslužné rutině.</span><span class="sxs-lookup"><span data-stu-id="4d639-172">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="4d639-173">`<Child @bind-Year="year" />` je ekvivalentem zápisu:</span><span class="sxs-lookup"><span data-stu-id="4d639-173">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="4d639-174">Vazba podřízenosti k nadřazené položce s zřetězenou vazbou</span><span class="sxs-lookup"><span data-stu-id="4d639-174">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="4d639-175">Běžným scénářem je zřetězení parametru vázaného na data na element stránky ve výstupu součásti.</span><span class="sxs-lookup"><span data-stu-id="4d639-175">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="4d639-176">Tento scénář se nazývá *zřetězená vazba* , protože k více úrovním vazby dochází současně.</span><span class="sxs-lookup"><span data-stu-id="4d639-176">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="4d639-177">Zřetězenou BIND nelze implementovat s [`@bind`](xref:mvc/views/razor#bind) syntaxí v podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="4d639-177">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="4d639-178">Obslužná rutina události a hodnota se musí zadat samostatně.</span><span class="sxs-lookup"><span data-stu-id="4d639-178">The event handler and value must be specified separately.</span></span> <span data-ttu-id="4d639-179">Nadřazená komponenta však může použít [`@bind`](xref:mvc/views/razor#bind) syntaxi s parametrem podřízené komponenty.</span><span class="sxs-lookup"><span data-stu-id="4d639-179">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the child component's parameter.</span></span>

<span data-ttu-id="4d639-180">Následující `PasswordField` součást ( `PasswordField.razor` ):</span><span class="sxs-lookup"><span data-stu-id="4d639-180">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="4d639-181">Nastaví `<input>` hodnotu elementu na `Password` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="4d639-181">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="4d639-182">Zpřístupňuje změny `Password` vlastnosti nadřazené komponentě pomocí [`EventCallback`](xref:blazor/components/event-handling#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="4d639-182">Exposes changes of the `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span></span>
* <span data-ttu-id="4d639-183">Používá `onclick` událost k aktivaci `ToggleShowPassword` metody.</span><span class="sxs-lookup"><span data-stu-id="4d639-183">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="4d639-184">Další informace naleznete v tématu <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="4d639-184">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="4d639-185">`PasswordField`Komponenta se používá v jiné součásti:</span><span class="sxs-lookup"><span data-stu-id="4d639-185">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="4d639-186">Chcete-li provést kontrolu nebo chyby depeše v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="4d639-186">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="4d639-187">Vytvořte pole zálohování pro `Password` ( `password` v následujícím ukázkovém kódu).</span><span class="sxs-lookup"><span data-stu-id="4d639-187">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="4d639-188">Proveďte kontroly nebo chyby depeší v metodě `Password` setter.</span><span class="sxs-lookup"><span data-stu-id="4d639-188">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="4d639-189">Následující příklad poskytuje okamžitou zpětnou vazbu uživateli, pokud se v hodnotě hesla používá mezera:</span><span class="sxs-lookup"><span data-stu-id="4d639-189">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="4d639-190">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="4d639-190">Additional resources</span></span>

* [<span data-ttu-id="4d639-191">Vazba na přepínače ve formuláři</span><span class="sxs-lookup"><span data-stu-id="4d639-191">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="4d639-192">Vazba `<select>` možností elementu na hodnoty objektu C# `null` ve formuláři</span><span class="sxs-lookup"><span data-stu-id="4d639-192">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
