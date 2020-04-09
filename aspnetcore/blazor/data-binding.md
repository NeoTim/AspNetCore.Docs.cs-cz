---
title: ASP.NET Blazor základní datové vazby
author: guardrex
description: Seznamte se s funkcemi datových Blazor vazeb pro komponenty a prvky modelu DOM v aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: a7b3730dad48b5bbb6134dab181051da4e3651b4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80320955"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a><span data-ttu-id="b53ce-103">ASP.NET Blazor základní datové vazby</span><span class="sxs-lookup"><span data-stu-id="b53ce-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="b53ce-104">[Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b53ce-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="b53ce-105">Komponenty razor poskytují funkce datové vazby [`@bind`](xref:mvc/views/razor#bind) prostřednictvím atributu elementu HTML pojmenovaného s hodnotou pole, vlastnosti nebo výrazu Razor.</span><span class="sxs-lookup"><span data-stu-id="b53ce-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="b53ce-106">Následující příklad sváže `CurrentValue` vlastnost s hodnotou textového pole:</span><span class="sxs-lookup"><span data-stu-id="b53ce-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="b53ce-107">Když textové pole ztratí fokus, hodnota vlastnosti se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="b53ce-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="b53ce-108">Textové pole je aktualizováno v unovém počítači pouze v případě, že je komponenta vykreslena, nikoli v reakci na změnu hodnoty vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b53ce-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="b53ce-109">Vzhledem k tomu, že komponenty vykreslují samy po spuštění kódu obslužné rutiny události, aktualizace vlastností se *obvykle* projeví v unovém uzly ihned po aktivaci obslužné rutiny události.</span><span class="sxs-lookup"><span data-stu-id="b53ce-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="b53ce-110">Použití `@bind` s `CurrentValue` vlastností (`<input @bind="CurrentValue" />`) je v podstatě ekvivalentní následujícímu:</span><span class="sxs-lookup"><span data-stu-id="b53ce-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="b53ce-111">Při vykreslení komponenty `value` vstupní prvek pochází z `CurrentValue` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b53ce-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="b53ce-112">Když uživatel zadá do textového pole `onchange` a změní fokus prvku, událost je aktivována a `CurrentValue` vlastnost je nastavena na změněnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b53ce-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="b53ce-113">Ve skutečnosti generování kódu je `@bind` složitější, protože zpracovává případy, kde jsou prováděny převody typu.</span><span class="sxs-lookup"><span data-stu-id="b53ce-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="b53ce-114">V zásadě `@bind` přidruží aktuální hodnotu `value` výrazu s atributem a zpracovává změny pomocí registrované obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="b53ce-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="b53ce-115">Svázat vlastnost nebo pole na jiné `@bind:event` události `event` také včetně atribut s parametrem.</span><span class="sxs-lookup"><span data-stu-id="b53ce-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="b53ce-116">Následující příklad váže `CurrentValue` vlastnost na `oninput` událost:</span><span class="sxs-lookup"><span data-stu-id="b53ce-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="b53ce-117">Na `onchange`rozdíl od , který se `oninput` aktivuje, když prvek ztratí fokus, aktivuje při změně hodnoty textového pole.</span><span class="sxs-lookup"><span data-stu-id="b53ce-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="b53ce-118">Použití `@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` se syntaxí k vazbě jiných atributů elementu než `value`.</span><span class="sxs-lookup"><span data-stu-id="b53ce-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="b53ce-119">V následujícím příkladu je styl odstavce aktualizován `_paragraphStyle` při změně hodnoty:</span><span class="sxs-lookup"><span data-stu-id="b53ce-119">In the following example, the paragraph's style is updated when the `_paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="_paragraphStyle" />
</p>

<p @bind-style="_paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string _paragraphStyle = "color:red";
}
```

<span data-ttu-id="b53ce-120">Vazba atributů rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="b53ce-120">Attribute binding is case sensitive.</span></span> <span data-ttu-id="b53ce-121">Například `@bind` je platný `@Bind` a je neplatný.</span><span class="sxs-lookup"><span data-stu-id="b53ce-121">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="b53ce-122">Neanalyzovatelné hodnoty</span><span class="sxs-lookup"><span data-stu-id="b53ce-122">Unparsable values</span></span>

<span data-ttu-id="b53ce-123">Když uživatel poskytuje neanalyzovatelnou hodnotu prvku vázanému na data, neanalyzovatelná hodnota se automaticky vrátí na předchozí hodnotu při aktivaci události vazby.</span><span class="sxs-lookup"><span data-stu-id="b53ce-123">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="b53ce-124">Představte si následující scénář:</span><span class="sxs-lookup"><span data-stu-id="b53ce-124">Consider the following scenario:</span></span>

* <span data-ttu-id="b53ce-125">Prvek `<input>` je vázán `int` na typ s `123`počáteční hodnotou :</span><span class="sxs-lookup"><span data-stu-id="b53ce-125">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="b53ce-126">Uživatel aktualizuje hodnotu prvku `123.45` na stránce a změní fokus prvku.</span><span class="sxs-lookup"><span data-stu-id="b53ce-126">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="b53ce-127">V předchozím scénáři je hodnota prvku vrácena `123`na .</span><span class="sxs-lookup"><span data-stu-id="b53ce-127">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="b53ce-128">Když je `123.45` hodnota odmítnuta ve prospěch `123`původní hodnoty , uživatel pochopí, že jejich hodnota nebyla přijata.</span><span class="sxs-lookup"><span data-stu-id="b53ce-128">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="b53ce-129">Ve výchozím nastavení se vazba `onchange` vztahuje`@bind="{PROPERTY OR FIELD}"`na událost prvku ( ).</span><span class="sxs-lookup"><span data-stu-id="b53ce-129">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="b53ce-130">Slouží `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` k aktivaci vazby na jinou událost.</span><span class="sxs-lookup"><span data-stu-id="b53ce-130">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="b53ce-131">Pro `oninput` událost`@bind:event="oninput"`( ), dojde k reverzi po každém stisknutí klávesy, která zavádí neopravitelnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b53ce-131">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="b53ce-132">Při cílení `oninput` na `int`událost pomocí typu -bound nemůže uživatel `.` zadat znak.</span><span class="sxs-lookup"><span data-stu-id="b53ce-132">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="b53ce-133">Znak `.` je okamžitě odebrán, takže uživatel obdrží okamžitou zpětnou vazbu, že jsou povolena pouze celá čísla.</span><span class="sxs-lookup"><span data-stu-id="b53ce-133">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="b53ce-134">Existují scénáře, kde vrácení hodnoty `oninput` události není ideální, například když by mělo být uživateli `<input>` povoleno vymazat neopravitelnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b53ce-134">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="b53ce-135">Alternativy zahrnují:</span><span class="sxs-lookup"><span data-stu-id="b53ce-135">Alternatives include:</span></span>

* <span data-ttu-id="b53ce-136">Nepoužívejte `oninput` událost.</span><span class="sxs-lookup"><span data-stu-id="b53ce-136">Don't use the `oninput` event.</span></span> <span data-ttu-id="b53ce-137">Použijte výchozí `onchange` událost (pouze určete), `@bind="{PROPERTY OR FIELD}"`kde neplatná hodnota není vrácena, dokud prvek neztratí fokus.</span><span class="sxs-lookup"><span data-stu-id="b53ce-137">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="b53ce-138">Vazba na typ s `int?` možnou hodnotou null, například nebo `string`, a poskytnout vlastní logiku pro zpracování neplatných položek.</span><span class="sxs-lookup"><span data-stu-id="b53ce-138">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="b53ce-139">Použijte [komponentu ověření](xref:blazor/forms-validation)formuláře `InputNumber` `InputDate`, například nebo .</span><span class="sxs-lookup"><span data-stu-id="b53ce-139">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="b53ce-140">Součásti ověření formuláře mají integrovanou podporu pro správu neplatných vstupů.</span><span class="sxs-lookup"><span data-stu-id="b53ce-140">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="b53ce-141">Součásti ověření formuláře:</span><span class="sxs-lookup"><span data-stu-id="b53ce-141">Form validation components:</span></span>
  * <span data-ttu-id="b53ce-142">Povolit uživateli zadat neplatný vstup a `EditContext`přijímat chyby ověření na přidružené .</span><span class="sxs-lookup"><span data-stu-id="b53ce-142">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="b53ce-143">Zobrazení chyb ověření v uživatelském rozhraní bez zasahování do zadání dalších dat webového formuláře uživatelem.</span><span class="sxs-lookup"><span data-stu-id="b53ce-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="b53ce-144">Formátovací řetězce</span><span class="sxs-lookup"><span data-stu-id="b53ce-144">Format strings</span></span>

<span data-ttu-id="b53ce-145">Datová vazba funguje <xref:System.DateTime> [`@bind:format`](xref:mvc/views/razor#bind)s formátovacími řetězci pomocí .</span><span class="sxs-lookup"><span data-stu-id="b53ce-145">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="b53ce-146">Jiné formátové výrazy, například formáty měny nebo čísel, nejsou v tuto chvíli k dispozici.</span><span class="sxs-lookup"><span data-stu-id="b53ce-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="b53ce-147">V předchozím kódu je `<input>` výchozí typ pole`type`prvku ( `text`) .</span><span class="sxs-lookup"><span data-stu-id="b53ce-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="b53ce-148">`@bind:format`je podporovánpro vazbu následující typy .NET:</span><span class="sxs-lookup"><span data-stu-id="b53ce-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="b53ce-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="b53ce-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="b53ce-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="b53ce-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="b53ce-151">Atribut `@bind:format` určuje formát data, který `value` se `<input>` má použít pro prvek.</span><span class="sxs-lookup"><span data-stu-id="b53ce-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="b53ce-152">Formát se také používá k analýzě `onchange` hodnoty, když dojde k události.</span><span class="sxs-lookup"><span data-stu-id="b53ce-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="b53ce-153">Určení formátu pro `date` typ pole se nedoporučuje, protože Blazor má integrovanou podporu pro formátování kalendářních dat.</span><span class="sxs-lookup"><span data-stu-id="b53ce-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="b53ce-154">Navzdory doporučení používejte formát data `yyyy-MM-dd` pro vazbu ke správnému fungování, `date` pokud je formát dodán s typem pole:</span><span class="sxs-lookup"><span data-stu-id="b53ce-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="b53ce-155">Vazba nadřazenou dítě s parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="b53ce-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="b53ce-156">Vazba rozpozná parametry `@bind-{PROPERTY}` komponenty, kde můžete vázat hodnotu vlastnosti z nadřazené součásti dolů na podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="b53ce-156">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="b53ce-157">Vazba z podřízeného nadřazeného objektu je zahrnuta ve [vazbě Child-to-parent s oddílem zřetězené vazby.](#child-to-parent-binding-with-chained-bind)</span><span class="sxs-lookup"><span data-stu-id="b53ce-157">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="b53ce-158">Následující podřízená`ChildComponent`komponenta `Year` ( `YearChanged` ) má parametr komponenty a zpětné volání:</span><span class="sxs-lookup"><span data-stu-id="b53ce-158">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="b53ce-159">`EventCallback<T>`je vysvětleno v <xref:blazor/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="b53ce-159">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="b53ce-160">Následující nadřazená komponenta používá:</span><span class="sxs-lookup"><span data-stu-id="b53ce-160">The following parent component uses:</span></span>

* <span data-ttu-id="b53ce-161">`ChildComponent`a váže `ParentYear` parametr z nadřazeného na `Year` parametr na podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="b53ce-161">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="b53ce-162">Událost `onclick` se používá k `ChangeTheYear` aktivaci metody.</span><span class="sxs-lookup"><span data-stu-id="b53ce-162">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="b53ce-163">Další informace naleznete v tématu <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="b53ce-163">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="b53ce-164">Vložením `ParentComponent` vytvoříte následující značky:</span><span class="sxs-lookup"><span data-stu-id="b53ce-164">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="b53ce-165">Pokud se hodnota `ParentYear` vlastnosti změní výběrem tlačítka `ParentComponent`v `Year` , `ChildComponent` vlastnost je aktualizována.</span><span class="sxs-lookup"><span data-stu-id="b53ce-165">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="b53ce-166">Nová hodnota `Year` je vykreslen v ui `ParentComponent` při rerendered:</span><span class="sxs-lookup"><span data-stu-id="b53ce-166">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="b53ce-167">Parametr `Year` je bindable, protože `YearChanged` má doprovodnou událost, `Year` která odpovídá typu parametru.</span><span class="sxs-lookup"><span data-stu-id="b53ce-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="b53ce-168">Podle konvence, `<ChildComponent @bind-Year="ParentYear" />` je v podstatě ekvivalentní psaní:</span><span class="sxs-lookup"><span data-stu-id="b53ce-168">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="b53ce-169">Obecně platí, že vlastnost může být vázána `@bind-{PROPRETY}:event` na odpovídající obslužnou rutinu události zahrnutím atributu.</span><span class="sxs-lookup"><span data-stu-id="b53ce-169">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="b53ce-170">Vlastnost `MyProp` může být například `MyEventHandler` vázána na použití následujících dvou atributů:</span><span class="sxs-lookup"><span data-stu-id="b53ce-170">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="b53ce-171">Vazba mezi dětmi s řetězovou vazbou</span><span class="sxs-lookup"><span data-stu-id="b53ce-171">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="b53ce-172">Běžným scénářem je zřetězení parametru vázaného na data k prvku stránky ve výstupu komponenty.</span><span class="sxs-lookup"><span data-stu-id="b53ce-172">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="b53ce-173">Tento scénář se nazývá *zřetězené vazby,* protože více úrovní vazby dojít současně.</span><span class="sxs-lookup"><span data-stu-id="b53ce-173">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="b53ce-174">Zřetězená vazba nemůže `@bind` být implementována pomocí syntaxe v prvku stránky.</span><span class="sxs-lookup"><span data-stu-id="b53ce-174">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="b53ce-175">Obslužná rutina události a hodnota musí být zadány samostatně.</span><span class="sxs-lookup"><span data-stu-id="b53ce-175">The event handler and value must be specified separately.</span></span> <span data-ttu-id="b53ce-176">Nadřazená komponenta `@bind` však může použít syntaxi s parametrem komponenty.</span><span class="sxs-lookup"><span data-stu-id="b53ce-176">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="b53ce-177">Následující `PasswordField` komponenta (*PasswordField.razor*):</span><span class="sxs-lookup"><span data-stu-id="b53ce-177">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="b53ce-178">Nastaví `<input>` hodnotu prvku `Password` na vlastnost.</span><span class="sxs-lookup"><span data-stu-id="b53ce-178">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="b53ce-179">Zpřístupní změny `Password` vlastnosti nadřazené součásti pomocí [zpětného volání události](xref:blazor/event-handling#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="b53ce-179">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="b53ce-180">Používá `onclick` událost se používá `ToggleShowPassword` k aktivaci metody.</span><span class="sxs-lookup"><span data-stu-id="b53ce-180">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="b53ce-181">Další informace naleznete v tématu <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="b53ce-181">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

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
        _showPassword = !_showPassword;
    }
}
```

<span data-ttu-id="b53ce-182">Komponenta se `PasswordField` používá v jiné součásti:</span><span class="sxs-lookup"><span data-stu-id="b53ce-182">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="b53ce-183">Chcete-li provést kontroly nebo soutisk chyby na heslo v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b53ce-183">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="b53ce-184">Vytvořte záložní `Password` pole`_password` pro ( v následujícím příkladu kódu).</span><span class="sxs-lookup"><span data-stu-id="b53ce-184">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="b53ce-185">Proveďte kontroly nebo `Password` soutisk chyby v setter.</span><span class="sxs-lookup"><span data-stu-id="b53ce-185">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="b53ce-186">Následující příklad poskytuje okamžitou zpětnou vazbu pro uživatele, pokud je místo použito v hodnotě hesla:</span><span class="sxs-lookup"><span data-stu-id="b53ce-186">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
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
        _showPassword = !_showPassword;
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="b53ce-187">Přepínače</span><span class="sxs-lookup"><span data-stu-id="b53ce-187">Radio buttons</span></span>

<span data-ttu-id="b53ce-188">Informace o vazbě na přepínací tlačítka ve formuláři naleznete v tématu <xref:blazor/forms-validation#work-with-radio-buttons>.</span><span class="sxs-lookup"><span data-stu-id="b53ce-188">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
