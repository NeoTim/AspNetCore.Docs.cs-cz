---
title: ASP.NET Core Blazor datové vazby
author: guardrex
description: Přečtěte si o funkcích datových vazeb pro komponenty a prvky modelu DOM v Blazor aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 6f5ad6b8f225834c92d6e33d8bcf608b56678e67
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014669"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="01d0c-103">ASP.NET Core Blazor datové vazby</span><span class="sxs-lookup"><span data-stu-id="01d0c-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="01d0c-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="01d0c-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="01d0c-105">Razorkomponenty poskytují funkce datových vazeb prostřednictvím atributu elementu HTML s názvem [`@bind`](xref:mvc/views/razor#bind) pole, vlastnosti nebo Razor hodnoty výrazu.</span><span class="sxs-lookup"><span data-stu-id="01d0c-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="01d0c-106">Následující příklad váže `CurrentValue` vlastnost k hodnotě v textovém poli:</span><span class="sxs-lookup"><span data-stu-id="01d0c-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="01d0c-107">Pokud textové pole ztratí fokus, je hodnota vlastnosti aktualizována.</span><span class="sxs-lookup"><span data-stu-id="01d0c-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="01d0c-108">Textové pole je aktualizováno v uživatelském rozhraní pouze v případě, že je komponenta vykreslena, nikoli v reakci na změnu hodnoty vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="01d0c-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="01d0c-109">Vzhledem k tomu, že se komponenty vykreslují po spuštění kódu obslužné rutiny události, se aktualizace vlastností *obvykle* projeví v uživatelském rozhraní hned po aktivaci obslužné rutiny události.</span><span class="sxs-lookup"><span data-stu-id="01d0c-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="01d0c-110">Použití [`@bind`](xref:mvc/views/razor#bind) s `CurrentValue` vlastností ( `<input @bind="CurrentValue" />` ) je v podstatě ekvivalentem následujícího:</span><span class="sxs-lookup"><span data-stu-id="01d0c-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="01d0c-111">Při vykreslení komponenty `value` z vlastnosti input element přichází `CurrentValue` .</span><span class="sxs-lookup"><span data-stu-id="01d0c-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="01d0c-112">Když uživatel zadá do textového pole a změní fokus prvku, `onchange` událost je aktivována a `CurrentValue` vlastnost je nastavena na změněnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="01d0c-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="01d0c-113">Ve skutečnosti je generování kódu složitější, protože [`@bind`](xref:mvc/views/razor#bind) zpracovává případy, kde jsou prováděny převody typů.</span><span class="sxs-lookup"><span data-stu-id="01d0c-113">In reality, the code generation is more complex because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="01d0c-114">V zásadě [`@bind`](xref:mvc/views/razor#bind) přidruží aktuální hodnotu výrazu k `value` atributu a zpracovává změny pomocí registrované obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="01d0c-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="01d0c-115">Navázání vlastnosti nebo pole na jiné události také zahrnutím `@bind:event` atributu s `event` parametrem.</span><span class="sxs-lookup"><span data-stu-id="01d0c-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="01d0c-116">Následující příklad váže `CurrentValue` vlastnost na `oninput` událost:</span><span class="sxs-lookup"><span data-stu-id="01d0c-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="01d0c-117">Na rozdíl od `onchange` , která je aktivována, když prvek ztratí fokus, `oninput` je aktivována při změně hodnoty textového pole.</span><span class="sxs-lookup"><span data-stu-id="01d0c-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="01d0c-118">Použijte `@bind-{ATTRIBUTE}` s `@bind-{ATTRIBUTE}:event` syntaxí pro svázání atributů elementu jiné než `value` .</span><span class="sxs-lookup"><span data-stu-id="01d0c-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="01d0c-119">V následujícím příkladu je styl odstavce aktualizován při `paragraphStyle` změně hodnoty:</span><span class="sxs-lookup"><span data-stu-id="01d0c-119">In the following example, the paragraph's style is updated when the `paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

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

<span data-ttu-id="01d0c-120">Vazba atributů rozlišuje velká a malá písmena:</span><span class="sxs-lookup"><span data-stu-id="01d0c-120">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="01d0c-121">`@bind`je platný.</span><span class="sxs-lookup"><span data-stu-id="01d0c-121">`@bind` is valid.</span></span>
* <span data-ttu-id="01d0c-122">`@Bind`a `@BIND` jsou neplatné.</span><span class="sxs-lookup"><span data-stu-id="01d0c-122">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="01d0c-123">Hodnoty, které nelze analyzovat</span><span class="sxs-lookup"><span data-stu-id="01d0c-123">Unparsable values</span></span>

<span data-ttu-id="01d0c-124">Když uživatel poskytne neanalyzovatelné hodnoty prvku DataBound, hodnota neanalyzovat se automaticky vrátí na předchozí hodnotu, když se aktivuje událost BIND.</span><span class="sxs-lookup"><span data-stu-id="01d0c-124">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="01d0c-125">Představte si následující scénář:</span><span class="sxs-lookup"><span data-stu-id="01d0c-125">Consider the following scenario:</span></span>

* <span data-ttu-id="01d0c-126">`<input>`Element je svázán `int` s typem s počáteční hodnotou `123` :</span><span class="sxs-lookup"><span data-stu-id="01d0c-126">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="01d0c-127">Uživatel aktualizuje hodnotu prvku na `123.45` stránce a změní fokus prvku.</span><span class="sxs-lookup"><span data-stu-id="01d0c-127">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="01d0c-128">V předchozím scénáři je hodnota elementu vrácena na `123` .</span><span class="sxs-lookup"><span data-stu-id="01d0c-128">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="01d0c-129">Pokud je hodnota `123.45` zamítnuta ve prospěch původní hodnoty `123` , uživateli se rozumí, že jejich hodnota nebyla přijata.</span><span class="sxs-lookup"><span data-stu-id="01d0c-129">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="01d0c-130">Ve výchozím nastavení se vazba vztahuje na událost elementu `onchange` ( `@bind="{PROPERTY OR FIELD}"` ).</span><span class="sxs-lookup"><span data-stu-id="01d0c-130">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="01d0c-131">Slouží `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` ke spuštění vazby pro jinou událost.</span><span class="sxs-lookup"><span data-stu-id="01d0c-131">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="01d0c-132">Pro `oninput` událost ( `@bind:event="oninput"` ) se reverze provádí po stisknutí klávesy, která zavádí neanalyzovatelné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="01d0c-132">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="01d0c-133">Při cílení `oninput` události s `int` typem vázaného na uživatele je znemožněno zadání `.` znaku.</span><span class="sxs-lookup"><span data-stu-id="01d0c-133">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="01d0c-134">`.`Znak je okamžitě odstraněn, takže uživatel obdrží okamžitou zpětnou vazbu, že jsou povolena pouze celá čísla.</span><span class="sxs-lookup"><span data-stu-id="01d0c-134">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="01d0c-135">K dispozici jsou situace, kdy vrácení hodnoty na `oninput` událost není ideální, například pokud by měl uživatel povoleno vymazat neanalyzovatelné `<input>` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="01d0c-135">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="01d0c-136">K alternativám patří:</span><span class="sxs-lookup"><span data-stu-id="01d0c-136">Alternatives include:</span></span>

* <span data-ttu-id="01d0c-137">Nepoužívejte `oninput` událost.</span><span class="sxs-lookup"><span data-stu-id="01d0c-137">Don't use the `oninput` event.</span></span> <span data-ttu-id="01d0c-138">Použijte výchozí `onchange` událost (pouze zadat `@bind="{PROPERTY OR FIELD}"` ), kde není platná hodnota vrácena, dokud prvek neztratí fokus.</span><span class="sxs-lookup"><span data-stu-id="01d0c-138">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="01d0c-139">Vytvořte vazby na typ s možnou hodnotou null, například `int?` nebo `string` , a poskytněte vlastní logiku pro zpracování neplatných položek.</span><span class="sxs-lookup"><span data-stu-id="01d0c-139">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="01d0c-140">Použijte [součást pro ověření formuláře](xref:blazor/forms-validation), jako je například <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> nebo <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="01d0c-140">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="01d0c-141">Komponenty ověřování formuláře mají integrovanou podporu pro správu neplatných vstupů.</span><span class="sxs-lookup"><span data-stu-id="01d0c-141">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="01d0c-142">Součásti pro ověření formuláře:</span><span class="sxs-lookup"><span data-stu-id="01d0c-142">Form validation components:</span></span>
  * <span data-ttu-id="01d0c-143">Povolí uživateli zadání neplatných vstupů a přijetí chyb ověřování na přidruženém <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="01d0c-143">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="01d0c-144">Zobrazí chyby ověřování v uživatelském rozhraní, aniž by došlo ke konfliktu s uživatelem, který zadává další data z formuláře.</span><span class="sxs-lookup"><span data-stu-id="01d0c-144">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="01d0c-145">Řetězce formátu</span><span class="sxs-lookup"><span data-stu-id="01d0c-145">Format strings</span></span>

<span data-ttu-id="01d0c-146">Datové vazby fungují s <xref:System.DateTime> řetězci formátu pomocí `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="01d0c-146">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="01d0c-147">Jiné formátovací výrazy, jako je například Měna nebo formáty čísel, nejsou v tuto chvíli k dispozici.</span><span class="sxs-lookup"><span data-stu-id="01d0c-147">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="01d0c-148">V předchozím kódu je `<input>` výchozí typ pole ( `type` ) elementu `text` .</span><span class="sxs-lookup"><span data-stu-id="01d0c-148">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="01d0c-149">`@bind:format`je podporováno pro vázání následujících typů rozhraní .NET:</span><span class="sxs-lookup"><span data-stu-id="01d0c-149">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="01d0c-150"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="01d0c-150"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="01d0c-151"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="01d0c-151"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="01d0c-152">`@bind:format`Atribut určuje formát data, který se má použít pro `value` `<input>` element.</span><span class="sxs-lookup"><span data-stu-id="01d0c-152">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="01d0c-153">Formát je také použit k analýze hodnoty při `onchange` výskytu události.</span><span class="sxs-lookup"><span data-stu-id="01d0c-153">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="01d0c-154">Zadání formátu pro `date` Typ pole se nedoporučuje, protože Blazor má vestavěnou podporu pro formátování kalendářních dat.</span><span class="sxs-lookup"><span data-stu-id="01d0c-154">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="01d0c-155">Navzdory doporučení použijte pouze `yyyy-MM-dd` formát data, aby vazba fungovala správně, pokud je zadán `date` Typ pole:</span><span class="sxs-lookup"><span data-stu-id="01d0c-155">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="01d0c-156">Vazba nadřazený-to-podřízený s parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="01d0c-156">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="01d0c-157">Vazba rozpoznává parametry komponenty, kde `@bind-{PROPERTY}` může vytvořit vazbu hodnoty vlastnosti z nadřazené komponenty dolů na podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="01d0c-157">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="01d0c-158">Vazba z podřízeného na nadřazenou položku je popsaná v tématu [vazba typu podřízená k nadřazenému s zřetězenou](#child-to-parent-binding-with-chained-bind) vazbou.</span><span class="sxs-lookup"><span data-stu-id="01d0c-158">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="01d0c-159">Následující podřízená komponenta ( `ChildComponent` ) má `Year` parametr komponenty a `YearChanged` zpětné volání:</span><span class="sxs-lookup"><span data-stu-id="01d0c-159">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="01d0c-160"><xref:Microsoft.AspNetCore.Components.EventCallback%601>Jako název parametru komponenty následovaný `Changed` příponou ( `{PARAMETER NAME}Changed` ) v předchozím příkladu musí být pojmenovaná `YearChanged` .</span><span class="sxs-lookup"><span data-stu-id="01d0c-160">The <xref:Microsoft.AspNetCore.Components.EventCallback%601> must be named as the component parameter name followed by the `Changed` suffix (`{PARAMETER NAME}Changed`), `YearChanged` in the preceding example.</span></span> <span data-ttu-id="01d0c-161">Další informace o <xref:Microsoft.AspNetCore.Components.EventCallback%601> naleznete v tématu <xref:blazor/components/event-handling#eventcallback> .</span><span class="sxs-lookup"><span data-stu-id="01d0c-161">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="01d0c-162">Následující nadřazená komponenta používá:</span><span class="sxs-lookup"><span data-stu-id="01d0c-162">The following parent component uses:</span></span>

* <span data-ttu-id="01d0c-163">`ChildComponent`a váže `ParentYear` parametr z nadřazené položky k `Year` parametru v podřízené komponentě.</span><span class="sxs-lookup"><span data-stu-id="01d0c-163">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="01d0c-164">`onclick`Událost se používá ke spuštění `ChangeTheYear` metody.</span><span class="sxs-lookup"><span data-stu-id="01d0c-164">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="01d0c-165">Další informace naleznete v tématu <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="01d0c-165">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="01d0c-166">Načítání `ParentComponent` kódu generuje následující značky:</span><span class="sxs-lookup"><span data-stu-id="01d0c-166">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="01d0c-167">Pokud `ParentYear` je hodnota vlastnosti změněna výběrem tlačítka v `ParentComponent` , `Year` vlastnost `ChildComponent` je aktualizována.</span><span class="sxs-lookup"><span data-stu-id="01d0c-167">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="01d0c-168">Nová hodnota `Year` se vykreslí v uživatelském rozhraní, když `ParentComponent` se znovu vykreslí:</span><span class="sxs-lookup"><span data-stu-id="01d0c-168">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="01d0c-169">`Year`Parametr je svázán, protože má doprovodnou `YearChanged` událost, která odpovídá typu `Year` parametru.</span><span class="sxs-lookup"><span data-stu-id="01d0c-169">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="01d0c-170">Podle konvence `<ChildComponent @bind-Year="ParentYear" />` má v podstatě ekvivalent zápisu:</span><span class="sxs-lookup"><span data-stu-id="01d0c-170">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="01d0c-171">Obecně platí, že vlastnost může být vázána na odpovídající obslužnou rutinu události zahrnutím `@bind-{PROPRETY}:event` atributu.</span><span class="sxs-lookup"><span data-stu-id="01d0c-171">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="01d0c-172">Vlastnost `MyProp` může být například svázána s `MyEventHandler` použitím následujících dvou atributů:</span><span class="sxs-lookup"><span data-stu-id="01d0c-172">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="01d0c-173">Vazba podřízenosti k nadřazené položce s zřetězenou vazbou</span><span class="sxs-lookup"><span data-stu-id="01d0c-173">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="01d0c-174">Běžným scénářem je zřetězení parametru vázaného na data na element stránky ve výstupu součásti.</span><span class="sxs-lookup"><span data-stu-id="01d0c-174">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="01d0c-175">Tento scénář se nazývá *zřetězená vazba* , protože k více úrovním vazby dochází současně.</span><span class="sxs-lookup"><span data-stu-id="01d0c-175">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="01d0c-176">Zřetězenou BIND nelze implementovat s [`@bind`](xref:mvc/views/razor#bind) syntaxí v elementu stránky.</span><span class="sxs-lookup"><span data-stu-id="01d0c-176">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the page's element.</span></span> <span data-ttu-id="01d0c-177">Obslužná rutina události a hodnota se musí zadat samostatně.</span><span class="sxs-lookup"><span data-stu-id="01d0c-177">The event handler and value must be specified separately.</span></span> <span data-ttu-id="01d0c-178">Nadřazená komponenta však může použít [`@bind`](xref:mvc/views/razor#bind) syntaxi s parametrem komponenty.</span><span class="sxs-lookup"><span data-stu-id="01d0c-178">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the component's parameter.</span></span>

<span data-ttu-id="01d0c-179">Následující `PasswordField` součást ( `PasswordField.razor` ):</span><span class="sxs-lookup"><span data-stu-id="01d0c-179">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="01d0c-180">Nastaví `<input>` hodnotu elementu na `Password` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="01d0c-180">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="01d0c-181">Zpřístupňuje změny `Password` vlastnosti nadřazené komponentě pomocí [`EventCallback`](xref:blazor/components/event-handling#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="01d0c-181">Exposes changes of the `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span></span>
* <span data-ttu-id="01d0c-182">Používá `onclick` událost k aktivaci `ToggleShowPassword` metody.</span><span class="sxs-lookup"><span data-stu-id="01d0c-182">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="01d0c-183">Další informace naleznete v tématu <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="01d0c-183">For more information, see <xref:blazor/components/event-handling>.</span></span>

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

<span data-ttu-id="01d0c-184">`PasswordField`Komponenta se používá v jiné součásti:</span><span class="sxs-lookup"><span data-stu-id="01d0c-184">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="01d0c-185">Chcete-li provést kontrolu nebo chyby depeše v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="01d0c-185">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="01d0c-186">Vytvořte pole zálohování pro `Password` ( `password` v následujícím ukázkovém kódu).</span><span class="sxs-lookup"><span data-stu-id="01d0c-186">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="01d0c-187">Proveďte kontroly nebo chyby depeší v metodě `Password` setter.</span><span class="sxs-lookup"><span data-stu-id="01d0c-187">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="01d0c-188">Následující příklad poskytuje okamžitou zpětnou vazbu uživateli, pokud se v hodnotě hesla používá mezera:</span><span class="sxs-lookup"><span data-stu-id="01d0c-188">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="01d0c-189">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="01d0c-189">Additional resources</span></span>

* [<span data-ttu-id="01d0c-190">Vazba na přepínače ve formuláři</span><span class="sxs-lookup"><span data-stu-id="01d0c-190">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="01d0c-191">Vazba `<select>` možností elementu na hodnoty objektu C# `null` ve formuláři</span><span class="sxs-lookup"><span data-stu-id="01d0c-191">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
