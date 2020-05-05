---
title: ASP.NET Core Blazor datové vazby
author: guardrex
description: Přečtěte si o funkcích datových vazeb pro komponenty a prvky Blazor modelu DOM v aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: 73e73869d58e4a22e9dbee059f69fd15605ce2ce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767548"
---
# <a name="aspnet-core-blazor-data-binding"></a>ASP.NET Core Blazor datové vazby

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

Razorkomponenty poskytují funkce datových vazeb prostřednictvím atributu elementu HTML s názvem [`@bind`](xref:mvc/views/razor#bind) pole, vlastnosti nebo Razor hodnoty výrazu.

Následující příklad váže `CurrentValue` vlastnost k hodnotě v textovém poli:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Pokud textové pole ztratí fokus, je hodnota vlastnosti aktualizována.

Textové pole je aktualizováno v uživatelském rozhraní pouze v případě, že je komponenta vykreslena, nikoli v reakci na změnu hodnoty vlastnosti. Vzhledem k tomu, že se komponenty vykreslují po spuštění kódu obslužné rutiny události, se aktualizace vlastností *obvykle* projeví v uživatelském rozhraní hned po aktivaci obslužné rutiny události.

Použití `@bind` s `CurrentValue` vlastností (`<input @bind="CurrentValue" />`) je v podstatě ekvivalentem následujícího:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Při vykreslení `value` komponenty z `CurrentValue` vlastnosti input element přichází. Když uživatel zadá do textového pole a změní fokus prvku, `onchange` událost je aktivována a `CurrentValue` vlastnost je nastavena na změněnou hodnotu. Ve skutečnosti je generování kódu složitější, protože `@bind` zpracovává případy, kde jsou prováděny převody typů. V zásadě `@bind` přidruží aktuální hodnotu výrazu k `value` atributu a zpracovává změny pomocí registrované obslužné rutiny.

Navázání vlastnosti nebo pole na jiné události také zahrnutím `@bind:event` atributu s `event` parametrem. Následující příklad váže `CurrentValue` vlastnost na `oninput` událost:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Na rozdíl `onchange`od, která je aktivována, když prvek ztratí `oninput` fokus, je aktivována při změně hodnoty textového pole.

Použijte `@bind-{ATTRIBUTE}` s `@bind-{ATTRIBUTE}:event` syntaxí pro svázání atributů elementu jiné `value`než. V následujícím příkladu je styl odstavce aktualizován při změně `_paragraphStyle` hodnoty:

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

* `<input>` Element je svázán s `int` typem s počáteční hodnotou `123`:

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* Uživatel aktualizuje hodnotu prvku na `123.45` stránce a změní fokus prvku.

V předchozím scénáři je hodnota elementu vrácena na `123`. Pokud je hodnota `123.45` zamítnuta ve prospěch původní hodnoty `123`, uživateli se rozumí, že jejich hodnota nebyla přijata.

Ve výchozím nastavení se vazba vztahuje na `onchange` událost elementu (`@bind="{PROPERTY OR FIELD}"`). Slouží `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` ke spuštění vazby pro jinou událost. Pro `oninput` událost (`@bind:event="oninput"`) se reverze provádí po stisknutí klávesy, která zavádí neanalyzovatelné hodnoty. Při cílení `oninput` události s `int`typem vázaného na uživatele je znemožněno zadání `.` znaku. `.` Znak je okamžitě odstraněn, takže uživatel obdrží okamžitou zpětnou vazbu, že jsou povolena pouze celá čísla. K dispozici jsou situace, kdy vrácení hodnoty na `oninput` událost není ideální, například pokud by měl uživatel povoleno vymazat neanalyzovatelné `<input>` hodnoty. K alternativám patří:

* Nepoužívejte `oninput` událost. Použijte výchozí `onchange` událost (pouze zadat `@bind="{PROPERTY OR FIELD}"`), kde není platná hodnota vrácena, dokud prvek neztratí fokus.
* Vytvořte vazby na typ s možnou hodnotou `int?` null `string`, například nebo, a poskytněte vlastní logiku pro zpracování neplatných položek.
* Použijte [součást pro ověření formuláře](xref:blazor/forms-validation), jako je `InputNumber` například `InputDate`nebo. Komponenty ověřování formuláře mají integrovanou podporu pro správu neplatných vstupů. Součásti pro ověření formuláře:
  * Povolí uživateli zadání neplatných vstupů a přijetí chyb ověřování na přidruženém `EditContext`.
  * Zobrazí chyby ověřování v uživatelském rozhraní, aniž by došlo ke konfliktu s uživatelem, který zadává další data z formuláře.

## <a name="format-strings"></a>Řetězce formátu

Datové vazby fungují s <xref:System.DateTime> řetězci formátu pomocí [`@bind:format`](xref:mvc/views/razor#bind). Jiné formátovací výrazy, jako je například Měna nebo formáty čísel, nejsou v tuto chvíli k dispozici.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

V předchozím kódu je výchozí typ `<input>` pole (`type`) elementu `text`. `@bind:format`je podporováno pro vázání následujících typů rozhraní .NET:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

`@bind:format` Atribut určuje formát data, který se má použít pro `value` `<input>` element. Formát je také použit k analýze hodnoty při výskytu `onchange` události.

Zadání formátu pro typ `date` pole se nedoporučuje, protože Blazor má vestavěnou podporu pro formátování kalendářních dat. Navzdory doporučení použijte pouze formát `yyyy-MM-dd` data, aby vazba fungovala správně, pokud je zadán typ `date` pole:

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Vazba nadřazený-to-podřízený s parametry komponenty

Vazba rozpoznává parametry komponenty, `@bind-{PROPERTY}` kde může vytvořit vazbu hodnoty vlastnosti z nadřazené komponenty dolů na podřízenou komponentu. Vazba z podřízeného na nadřazenou položku je popsaná v tématu [vazba typu podřízená k nadřazenému s zřetězenou](#child-to-parent-binding-with-chained-bind) vazbou.

Následující podřízená komponenta (`ChildComponent`) má parametr `Year` komponenty a `YearChanged` zpětné volání:

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

`EventCallback<T>`je vysvětleno <xref:blazor/event-handling#eventcallback>v tématu.

Následující nadřazená komponenta používá:

* `ChildComponent`a váže `ParentYear` parametr z nadřazené položky k `Year` parametru v podřízené komponentě.
* `onclick` Událost se používá ke spuštění `ChangeTheYear` metody. Další informace naleznete v tématu <xref:blazor/event-handling>.

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

Načítání kódu `ParentComponent` generuje následující značky:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

`ParentYear` Pokud je hodnota vlastnosti změněna výběrem tlačítka `ParentComponent`v, `Year` vlastnost `ChildComponent` je aktualizována. Nová hodnota `Year` se vykreslí v uživatelském rozhraní, když `ParentComponent` se znovu vykreslí:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

`Year` Parametr je svázán, protože má doprovodnou `YearChanged` událost, která odpovídá typu `Year` parametru.

Podle konvence `<ChildComponent @bind-Year="ParentYear" />` má v podstatě ekvivalent zápisu:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Obecně platí, že vlastnost může být vázána na odpovídající obslužnou rutinu události zahrnutím `@bind-{PROPRETY}:event` atributu. Vlastnost `MyProp` může být například svázána s `MyEventHandler` použitím následujících dvou atributů:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Vazba podřízenosti k nadřazené položce s zřetězenou vazbou

Běžným scénářem je zřetězení parametru vázaného na data na element stránky ve výstupu součásti. Tento scénář se nazývá *zřetězená vazba* , protože k více úrovním vazby dochází současně.

Zřetězenou BIND nelze implementovat s `@bind` syntaxí v elementu stránky. Obslužná rutina události a hodnota se musí zadat samostatně. Nadřazená komponenta však může použít `@bind` syntaxi s parametrem komponenty.

Následující `PasswordField` součást (*PasswordField. Razor*):

* Nastaví hodnotu `<input>` elementu na `Password` vlastnost.
* Zpřístupňuje změny `Password` vlastnosti nadřazené komponentě pomocí [vnořenou eventCallback](xref:blazor/event-handling#eventcallback).
* Používá `onclick` událost, která slouží ke spuštění `ToggleShowPassword` metody. Další informace naleznete v tématu <xref:blazor/event-handling>.

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

`PasswordField` Komponenta se používá v jiné součásti:

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
* Proveďte kontroly nebo chyby depeší v metodě `Password` setter.

Následující příklad poskytuje okamžitou zpětnou vazbu uživateli, pokud se v hodnotě hesla používá mezera:

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

## <a name="radio-buttons"></a>Přepínače

Informace o vazbě na přepínače ve formuláři naleznete v tématu <xref:blazor/forms-validation#work-with-radio-buttons>.
