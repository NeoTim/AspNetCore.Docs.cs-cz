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
ms.openlocfilehash: 0884b0bedd9ed31b8c85790c6950c7c5d63bdf44
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653903"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a>ASP.NET Core Blazor datové vazby

Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Steve Sanderson](https://github.com/SteveSandersonMS)

Razor komponenty poskytují funkce datových vazeb prostřednictvím atributu elementu HTML s názvem [`@bind`](xref:mvc/views/razor#bind) pole, vlastnosti nebo Razor hodnoty výrazu.

Následující příklad vytvoří vazby `<input>` elementu k `currentValue` poli a `<input>` elementu na `CurrentValue` vlastnost:

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

Pokud jeden z prvků ztratí fokus, je jeho vázané pole nebo vlastnost aktualizováno.

Textové pole je aktualizováno v uživatelském rozhraní pouze v případě, že je komponenta vykreslena, nikoli v reakci na změnu hodnoty pole nebo vlastnosti. Vzhledem k tomu, že se komponenty vykreslují po spuštění kódu obslužné rutiny události, se aktualizace polí a vlastností *obvykle* projeví v uživatelském rozhraní hned po aktivaci obslužné rutiny události.

Použití [`@bind`](xref:mvc/views/razor#bind) s `CurrentValue` vlastností ( `<input @bind="CurrentValue" />` ) je v podstatě ekvivalentem následujícího:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

Při vykreslení komponenty `value` z vlastnosti input element přichází `CurrentValue` . Když uživatel zadá do textového pole a změní fokus prvku, `onchange` událost je aktivována a `CurrentValue` vlastnost je nastavena na změněnou hodnotu. Ve skutečnosti je generování kódu složitější než, protože [`@bind`](xref:mvc/views/razor#bind) zpracovává případy, kde jsou prováděny převody typu. V zásadě [`@bind`](xref:mvc/views/razor#bind) přidruží aktuální hodnotu výrazu k `value` atributu a zpracovává změny pomocí registrované obslužné rutiny.

Navázání vlastnosti nebo pole na jiné události také zahrnutím `@bind:event` atributu s `event` parametrem. Následující příklad váže `CurrentValue` vlastnost na `oninput` událost:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Na rozdíl od `onchange` , která je aktivována, když prvek ztratí fokus, `oninput` je aktivována při změně hodnoty textového pole.

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

Vazba atributů rozlišuje velká a malá písmena:

* `@bind` je platný.
* `@Bind` a `@BIND` jsou neplatné.

## <a name="unparsable-values"></a>Hodnoty, které nelze analyzovat

Když uživatel poskytne neanalyzovatelné hodnoty prvku DataBound, hodnota neanalyzovat se automaticky vrátí na předchozí hodnotu, když se aktivuje událost BIND.

Představte si následující scénář:

* `<input>`Element je svázán `int` s typem s počáteční hodnotou `123` :

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* Uživatel aktualizuje hodnotu prvku na `123.45` stránce a změní fokus prvku.

V předchozím scénáři je hodnota elementu vrácena na `123` . Pokud je hodnota `123.45` zamítnuta ve prospěch původní hodnoty `123` , uživateli se rozumí, že jejich hodnota nebyla přijata.

Ve výchozím nastavení se vazba vztahuje na událost elementu `onchange` ( `@bind="{PROPERTY OR FIELD}"` ). Slouží `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` ke spuštění vazby pro jinou událost. Pro `oninput` událost ( `@bind:event="oninput"` ) se reverze provádí po stisknutí klávesy, která zavádí neanalyzovatelné hodnoty. Při cílení `oninput` události s `int` typem vázaného na uživatele je znemožněno zadání `.` znaku. `.`Znak je okamžitě odstraněn, takže uživatel obdrží okamžitou zpětnou vazbu, že jsou povolena pouze celá čísla. K dispozici jsou situace, kdy vrácení hodnoty na `oninput` událost není ideální, například pokud by měl uživatel povoleno vymazat neanalyzovatelné `<input>` hodnoty. K alternativám patří:

* Nepoužívejte `oninput` událost. Použijte výchozí `onchange` událost (pouze zadat `@bind="{PROPERTY OR FIELD}"` ), kde není platná hodnota vrácena, dokud prvek neztratí fokus.
* Umožňuje vytvořit propojení s typem s možnou hodnotou null, například `int?` nebo `string` a poskytnout vlastní logiku pro zpracování neplatných položek.
* Použijte [součást pro ověření formuláře](xref:blazor/forms-validation), jako je například <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> nebo <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> . Komponenty ověřování formuláře mají integrovanou podporu pro správu neplatných vstupů. Další informace naleznete v tématu <xref:blazor/forms-validation>. Součásti pro ověření formuláře:
  * Povolí uživateli zadání neplatných vstupů a přijetí chyb ověřování na přidruženém <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .
  * Zobrazí chyby ověřování v uživatelském rozhraní, aniž by došlo ke konfliktu s uživatelem, který zadává další data z formuláře.

## <a name="format-strings"></a>Řetězce formátu

Datové vazby fungují s <xref:System.DateTime> řetězci formátu pomocí `@bind:format` . Jiné formátovací výrazy, jako je například Měna nebo formáty čísel, nejsou v tuto chvíli k dispozici.

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

V předchozím kódu je `<input>` výchozí typ pole ( `type` ) elementu `text` . `@bind:format` je podporováno pro vázání následujících typů rozhraní .NET:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

`@bind:format`Atribut určuje formát data, který se má použít pro `value` `<input>` element. Formát je také použit k analýze hodnoty při `onchange` výskytu události.

Zadání formátu pro `date` Typ pole se nedoporučuje, protože Blazor má vestavěnou podporu pro formátování kalendářních dat. Navzdory doporučení použijte pouze `yyyy-MM-dd` formát data pro vazbu, aby správně fungoval, pokud je zadán `date` Typ pole:

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Vazba nadřazený-to-podřízený s parametry komponenty

Parametry součásti umožňují svázat vlastnosti a pole nadřazené komponenty se `@bind-{PROPERTY OR FIELD}` syntaxí.

Následující `Child` součást ( `Shared/Child.razor` ) má `Year` parametr komponenty a `YearChanged` zpětné volání:

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

Zpětné volání ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ) musí být pojmenováno jako název parametru součásti následovaný `Changed` příponou ( `{PARAMETER NAME}Changed` ). V předchozím příkladu má zpětné volání název `YearChanged` . Další informace o <xref:Microsoft.AspNetCore.Components.EventCallback%601> naleznete v tématu <xref:blazor/components/event-handling#eventcallback> .

V následující `Parent` součásti ( `Parent.razor` ) `year` je pole svázáno s `Year` parametrem podřízené komponenty:

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

`Year`Parametr je svázán, protože má doprovodnou `YearChanged` událost, která odpovídá typu `Year` parametru.

Podle konvence může být vlastnost vázána na odpovídající obslužnou rutinu události zahrnutím `@bind-{PROPERTY}:event` atributu přiřazeného k obslužné rutině. `<Child @bind-Year="year" />` je ekvivalentem zápisu:

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Vazba podřízenosti k nadřazené položce s zřetězenou vazbou

Běžným scénářem je zřetězení parametru vázaného na data na element stránky ve výstupu součásti. Tento scénář se nazývá *zřetězená vazba* , protože k více úrovním vazby dochází současně.

Zřetězenou BIND nelze implementovat s [`@bind`](xref:mvc/views/razor#bind) syntaxí v podřízené součásti. Obslužná rutina události a hodnota se musí zadat samostatně. Nadřazená komponenta však může použít [`@bind`](xref:mvc/views/razor#bind) syntaxi s parametrem podřízené komponenty.

Následující `PasswordField` součást ( `PasswordField.razor` ):

* Nastaví `<input>` hodnotu prvku na `password` pole.
* Zpřístupňuje změny `Password` vlastnosti nadřazené komponentě [`EventCallback`](xref:blazor/components/event-handling#eventcallback) , která se předává do aktuální hodnoty `password` pole dítěte jako argument.
* Používá `onclick` událost k aktivaci `ToggleShowPassword` metody. Další informace naleznete v tématu <xref:blazor/components/event-handling>.

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

`PasswordField`Komponenta se používá v jiné součásti:

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Provede kontroly nebo chyby depeší v metodě, která vyvolá delegáta vazby. Následující příklad poskytuje okamžitou zpětnou vazbu uživateli, pokud se v hodnotě hesla používá mezera:

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
        password = e.Value.ToString();
        
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

## <a name="bind-across-more-than-two-components"></a>Vazba napříč více než dvěma komponentami

Můžete vytvořit propojení s libovolným počtem vnořených komponent, ale musíte se s ním zacházet s jednosměrným tokem dat:

* Průběh oznámení změn *v hierarchii*.
* Nové hodnoty parametrů *flowují hierarchii*.

Běžným a doporučeným přístupem je ukládat pouze podkladová data v nadřazené komponentě, aby nedocházelo k nejasnostem o tom, jaký stav se musí aktualizovat.

Následující komponenty ukazují předchozí koncepty:

`ParentComponent.razor`:

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

`ChildComponent.razor`:

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

`GrandchildComponent.razor`:

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

Alternativní postup, který je vhodný pro sdílení dat v paměti napříč komponentami, které nejsou nutně vnořené, najdete v tématu <xref:blazor/state-management#in-memory-state-container-service> .

## <a name="additional-resources"></a>Další zdroje informací

* [Vazba na přepínače ve formuláři](xref:blazor/forms-validation#radio-buttons)
* [Vazba `<select>` možností elementu na hodnoty objektu C# `null` ve formuláři](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
