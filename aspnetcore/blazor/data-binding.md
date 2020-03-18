---
title: Datová vazba ASP.NET Core Blazor
author: guardrex
description: Přečtěte si o funkcích datových vazeb pro komponenty a prvky modelu DOM v aplikacích Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: 5b49d2598a451ee607e034913bd1aeaa03f941c6
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511194"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a>Datová vazba ASP.NET Core Blazor

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

Komponenty Razor poskytují funkce datových vazeb prostřednictvím atributu elementu HTML s názvem [`@bind`](xref:mvc/views/razor#bind) s hodnotou výrazu pole, vlastnosti nebo Razor.

Následující příklad váže vlastnost `CurrentValue` k hodnotě v textovém poli:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Pokud textové pole ztratí fokus, je hodnota vlastnosti aktualizována.

Textové pole je aktualizováno v uživatelském rozhraní pouze v případě, že je komponenta vykreslena, nikoli v reakci na změnu hodnoty vlastnosti. Vzhledem k tomu, že se komponenty vykreslují po spuštění kódu obslužné rutiny události, se aktualizace vlastností *obvykle* projeví v uživatelském rozhraní hned po aktivaci obslužné rutiny události.

Použití `@bind` s vlastností `CurrentValue` (`<input @bind="CurrentValue" />`) je v podstatě ekvivalentem následujícího:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Při vykreslení komponenty se `value` vstupního elementu dostane z vlastnosti `CurrentValue`. Pokud uživatel zadá do textového pole a změní fokus prvku, je vyvolána událost `onchange` a vlastnost `CurrentValue` je nastavena na změněnou hodnotu. Ve skutečnosti je generování kódu složitější, protože `@bind` zpracovává případy, kde jsou prováděny převody typů. V zásadě `@bind` přidruží aktuální hodnotu výrazu k atributu `value` a zpracovává změny pomocí registrované obslužné rutiny.

Navázání vlastnosti nebo pole na jiné události zahrnutím atributu `@bind:event` s parametrem `event`. Následující příklad váže vlastnost `CurrentValue` v události `oninput`:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Na rozdíl od `onchange`, která je aktivována, když prvek ztratí fokus, `oninput` při změně hodnoty textového pole aktivována.

Použijte `@bind-{ATTRIBUTE}` se syntaxí `@bind-{ATTRIBUTE}:event` pro svázání atributů elementu jiné než `value`. V následujícím příkladu je styl odstavce aktualizován při změně hodnoty `_paragraphStyle`:

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

Vazba atributu rozlišuje velká a malá písmena. Například `@bind` je platný a `@Bind` je neplatný.

## <a name="unparsable-values"></a>Hodnoty, které nelze analyzovat

Když uživatel poskytne neanalyzovatelné hodnoty prvku DataBound, hodnota neanalyzovat se automaticky vrátí na předchozí hodnotu, když se aktivuje událost BIND.

Představte si následující scénář:

* Element `<input>` je vázán na typ `int` s počáteční hodnotou `123`:

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* Uživatel aktualizuje hodnotu prvku tak, aby `123.45` na stránce a změnil fokus prvku.

V předchozím scénáři je hodnota elementu vrácena na `123`. Pokud je hodnota `123.45` zamítnuta ve prospěch původní hodnoty `123`, uživateli se rozumí, že jejich hodnota nebyla přijata.

Ve výchozím nastavení se vazba vztahuje na událost `onchange` elementu (`@bind="{PROPERTY OR FIELD}"`). K aktivaci vazby pro jinou událost použijte `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`. Pro událost `oninput` (`@bind:event="oninput"`) dojde k reverzi po stisknutí klávesy, která zavádí neanalyzovatelné hodnoty. Při cílení na událost `oninput` s typem svázaným `int`je uživateli zabráněno v psaní `.`ho znaku. Znak `.` je okamžitě odebrán, takže uživatel obdrží okamžitou zpětnou vazbu, že jsou povolena pouze celá čísla. Existují situace, kdy se hodnota `oninput` události nedoporučuje, například pokud by měl být uživatel povolen k vymazání neanalyzovatelné `<input>` hodnoty. K alternativám patří:

* Nepoužívejte událost `oninput`. Použijte výchozí událost `onchange` (zadejte pouze `@bind="{PROPERTY OR FIELD}"`), kde neplatnou hodnotu vrátí, dokud prvek neztratí fokus.
* Vytvořte propojení s typem s možnou hodnotou null, například `int?` nebo `string`, a poskytněte vlastní logiku pro zpracování neplatných položek.
* Použijte [komponentu ověřování formuláře](xref:blazor/forms-validation), například `InputNumber` nebo `InputDate`. Komponenty ověřování formuláře mají integrovanou podporu pro správu neplatných vstupů. Součásti pro ověření formuláře:
  * Povolí uživateli zadání neplatných vstupů a přijetí chyb ověřování na přidruženém `EditContext`.
  * Zobrazí chyby ověřování v uživatelském rozhraní, aniž by došlo ke konfliktu s uživatelem, který zadává další data z formuláře.

## <a name="format-strings"></a>Řetězce formátu

Datové vazby fungují s <xref:System.DateTime> formátovacími řetězci pomocí [`@bind:format`](xref:mvc/views/razor#bind). Jiné formátovací výrazy, jako je například Měna nebo formáty čísel, nejsou v tuto chvíli k dispozici.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

V předchozím kódu je výchozí hodnota typu pole `<input>` elementu (`type`) `text`. `@bind:format` se podporuje pro vázání následujících typů .NET:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

Atribut `@bind:format` určuje formát data, který má být použit pro `value` elementu `<input>`. Formát se používá také k analýze hodnoty při výskytu události `onchange`.

Zadání formátu pro typ pole `date` se nedoporučuje, protože Blazor obsahuje integrovanou podporu pro formátování dat. Navzdory doporučení použijte formát data `yyyy-MM-dd`, aby vazba fungovala správně, pokud je zadán typ `date` pole:

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Vazba nadřazený-to-podřízený s parametry komponenty

Vazba rozpoznává parametry komponenty, kde `@bind-{PROPERTY}` mohou svázat hodnotu vlastnosti z nadřazené komponenty dolů až k podřízené součásti. Vazba z podřízeného na nadřazenou položku je popsaná v tématu [vazba typu podřízená k nadřazenému s zřetězenou](#child-to-parent-binding-with-chained-bind) vazbou.

Následující podřízená komponenta (`ChildComponent`) má parametr `Year` součásti a `YearChanged` zpětného volání:

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

`EventCallback<T>` je vysvětleno v <xref:blazor/event-handling#eventcallback>.

Následující nadřazená komponenta používá:

* `ChildComponent` a váže parametr `ParentYear` z nadřazené položky k parametru `Year` pro podřízenou komponentu.
* Událost `onclick` slouží ke spuštění metody `ChangeTheYear`. Další informace naleznete v tématu <xref:blazor/event-handling>.

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

Načtení `ParentComponent` vytvoří následující kód:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Pokud je hodnota vlastnosti `ParentYear` změněna výběrem tlačítka v `ParentComponent`, je aktualizována vlastnost `Year` `ChildComponent`. Nová hodnota `Year` se vykreslí v uživatelském rozhraní, když se `ParentComponent` znovu vykreslí:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Parametr `Year` je svázán, protože má doprovodnou událost `YearChanged`, která odpovídá typu `Year` parametru.

Podle konvence `<ChildComponent @bind-Year="ParentYear" />` v podstatě ekvivalentem zápisu:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Obecně platí, že vlastnost může být vázána na odpovídající obslužnou rutinu události zahrnutím atributu `@bind-{PROPRETY}:event`. Například vlastnost `MyProp` může být svázána s `MyEventHandler` pomocí následujících dvou atributů:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Vazba podřízenosti k nadřazené položce s zřetězenou vazbou

Běžným scénářem je zřetězení parametru vázaného na data na element stránky ve výstupu součásti. Tento scénář se nazývá *zřetězená vazba* , protože k více úrovním vazby dochází současně.

Zřetězenou BIND nelze implementovat s syntaxí `@bind` v elementu stránky. Obslužná rutina události a hodnota se musí zadat samostatně. Nadřazená komponenta však může použít `@bind` syntaxe s parametrem komponenty.

Následující součást `PasswordField` (*PasswordField. Razor*):

* Nastaví hodnotu `<input>` elementu na vlastnost `Password`.
* Zpřístupňuje změny vlastnosti `Password` nadřazené komponentě pomocí [vnořenou eventCallback](xref:blazor/event-handling#eventcallback).
* Používá událost `onclick` slouží ke spuštění metody `ToggleShowPassword`. Další informace naleznete v tématu <xref:blazor/event-handling>.

```razor
<h1>Child Component</h2>

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

Komponenta `PasswordField` se používá v jiné součásti:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

Chcete-li provést kontrolu nebo chyby depeše v předchozím příkladu:

* Vytvořte pole zálohování pro `Password` (`_password` v následujícím ukázkovém kódu).
* Proveďte kontroly nebo chyby depeší ve `Password` setter.

Následující příklad poskytuje okamžitou zpětnou vazbu uživateli, pokud se v hodnotě hesla používá mezera:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

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

## <a name="radio-buttons"></a>Přepínací tlačítka

Informace o vazbě na přepínače ve formuláři naleznete v tématu <xref:blazor/forms-validation#work-with-radio-buttons>.
