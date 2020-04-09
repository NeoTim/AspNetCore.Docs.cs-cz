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
# <a name="aspnet-core-opno-locblazor-data-binding"></a>ASP.NET Blazor základní datové vazby

[Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)

Komponenty razor poskytují funkce datové vazby [`@bind`](xref:mvc/views/razor#bind) prostřednictvím atributu elementu HTML pojmenovaného s hodnotou pole, vlastnosti nebo výrazu Razor.

Následující příklad sváže `CurrentValue` vlastnost s hodnotou textového pole:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Když textové pole ztratí fokus, hodnota vlastnosti se aktualizuje.

Textové pole je aktualizováno v unovém počítači pouze v případě, že je komponenta vykreslena, nikoli v reakci na změnu hodnoty vlastnosti. Vzhledem k tomu, že komponenty vykreslují samy po spuštění kódu obslužné rutiny události, aktualizace vlastností se *obvykle* projeví v unovém uzly ihned po aktivaci obslužné rutiny události.

Použití `@bind` s `CurrentValue` vlastností (`<input @bind="CurrentValue" />`) je v podstatě ekvivalentní následujícímu:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Při vykreslení komponenty `value` vstupní prvek pochází z `CurrentValue` vlastnosti. Když uživatel zadá do textového pole `onchange` a změní fokus prvku, událost je aktivována a `CurrentValue` vlastnost je nastavena na změněnou hodnotu. Ve skutečnosti generování kódu je `@bind` složitější, protože zpracovává případy, kde jsou prováděny převody typu. V zásadě `@bind` přidruží aktuální hodnotu `value` výrazu s atributem a zpracovává změny pomocí registrované obslužné rutiny.

Svázat vlastnost nebo pole na jiné `@bind:event` události `event` také včetně atribut s parametrem. Následující příklad váže `CurrentValue` vlastnost na `oninput` událost:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Na `onchange`rozdíl od , který se `oninput` aktivuje, když prvek ztratí fokus, aktivuje při změně hodnoty textového pole.

Použití `@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` se syntaxí k vazbě jiných atributů elementu než `value`. V následujícím příkladu je styl odstavce aktualizován `_paragraphStyle` při změně hodnoty:

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

Vazba atributů rozlišuje malá a velká písmena. Například `@bind` je platný `@Bind` a je neplatný.

## <a name="unparsable-values"></a>Neanalyzovatelné hodnoty

Když uživatel poskytuje neanalyzovatelnou hodnotu prvku vázanému na data, neanalyzovatelná hodnota se automaticky vrátí na předchozí hodnotu při aktivaci události vazby.

Představte si následující scénář:

* Prvek `<input>` je vázán `int` na typ s `123`počáteční hodnotou :

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* Uživatel aktualizuje hodnotu prvku `123.45` na stránce a změní fokus prvku.

V předchozím scénáři je hodnota prvku vrácena `123`na . Když je `123.45` hodnota odmítnuta ve prospěch `123`původní hodnoty , uživatel pochopí, že jejich hodnota nebyla přijata.

Ve výchozím nastavení se vazba `onchange` vztahuje`@bind="{PROPERTY OR FIELD}"`na událost prvku ( ). Slouží `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` k aktivaci vazby na jinou událost. Pro `oninput` událost`@bind:event="oninput"`( ), dojde k reverzi po každém stisknutí klávesy, která zavádí neopravitelnou hodnotu. Při cílení `oninput` na `int`událost pomocí typu -bound nemůže uživatel `.` zadat znak. Znak `.` je okamžitě odebrán, takže uživatel obdrží okamžitou zpětnou vazbu, že jsou povolena pouze celá čísla. Existují scénáře, kde vrácení hodnoty `oninput` události není ideální, například když by mělo být uživateli `<input>` povoleno vymazat neopravitelnou hodnotu. Alternativy zahrnují:

* Nepoužívejte `oninput` událost. Použijte výchozí `onchange` událost (pouze určete), `@bind="{PROPERTY OR FIELD}"`kde neplatná hodnota není vrácena, dokud prvek neztratí fokus.
* Vazba na typ s `int?` možnou hodnotou null, například nebo `string`, a poskytnout vlastní logiku pro zpracování neplatných položek.
* Použijte [komponentu ověření](xref:blazor/forms-validation)formuláře `InputNumber` `InputDate`, například nebo . Součásti ověření formuláře mají integrovanou podporu pro správu neplatných vstupů. Součásti ověření formuláře:
  * Povolit uživateli zadat neplatný vstup a `EditContext`přijímat chyby ověření na přidružené .
  * Zobrazení chyb ověření v uživatelském rozhraní bez zasahování do zadání dalších dat webového formuláře uživatelem.

## <a name="format-strings"></a>Formátovací řetězce

Datová vazba funguje <xref:System.DateTime> [`@bind:format`](xref:mvc/views/razor#bind)s formátovacími řetězci pomocí . Jiné formátové výrazy, například formáty měny nebo čísel, nejsou v tuto chvíli k dispozici.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

V předchozím kódu je `<input>` výchozí typ pole`type`prvku ( `text`) . `@bind:format`je podporovánpro vazbu následující typy .NET:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

Atribut `@bind:format` určuje formát data, který `value` se `<input>` má použít pro prvek. Formát se také používá k analýzě `onchange` hodnoty, když dojde k události.

Určení formátu pro `date` typ pole se nedoporučuje, protože Blazor má integrovanou podporu pro formátování kalendářních dat. Navzdory doporučení používejte formát data `yyyy-MM-dd` pro vazbu ke správnému fungování, `date` pokud je formát dodán s typem pole:

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Vazba nadřazenou dítě s parametry komponenty

Vazba rozpozná parametry `@bind-{PROPERTY}` komponenty, kde můžete vázat hodnotu vlastnosti z nadřazené součásti dolů na podřízenou komponentu. Vazba z podřízeného nadřazeného objektu je zahrnuta ve [vazbě Child-to-parent s oddílem zřetězené vazby.](#child-to-parent-binding-with-chained-bind)

Následující podřízená`ChildComponent`komponenta `Year` ( `YearChanged` ) má parametr komponenty a zpětné volání:

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

`EventCallback<T>`je vysvětleno v <xref:blazor/event-handling#eventcallback>.

Následující nadřazená komponenta používá:

* `ChildComponent`a váže `ParentYear` parametr z nadřazeného na `Year` parametr na podřízené součásti.
* Událost `onclick` se používá k `ChangeTheYear` aktivaci metody. Další informace naleznete v tématu <xref:blazor/event-handling>.

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

Vložením `ParentComponent` vytvoříte následující značky:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Pokud se hodnota `ParentYear` vlastnosti změní výběrem tlačítka `ParentComponent`v `Year` , `ChildComponent` vlastnost je aktualizována. Nová hodnota `Year` je vykreslen v ui `ParentComponent` při rerendered:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Parametr `Year` je bindable, protože `YearChanged` má doprovodnou událost, `Year` která odpovídá typu parametru.

Podle konvence, `<ChildComponent @bind-Year="ParentYear" />` je v podstatě ekvivalentní psaní:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Obecně platí, že vlastnost může být vázána `@bind-{PROPRETY}:event` na odpovídající obslužnou rutinu události zahrnutím atributu. Vlastnost `MyProp` může být například `MyEventHandler` vázána na použití následujících dvou atributů:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Vazba mezi dětmi s řetězovou vazbou

Běžným scénářem je zřetězení parametru vázaného na data k prvku stránky ve výstupu komponenty. Tento scénář se nazývá *zřetězené vazby,* protože více úrovní vazby dojít současně.

Zřetězená vazba nemůže `@bind` být implementována pomocí syntaxe v prvku stránky. Obslužná rutina události a hodnota musí být zadány samostatně. Nadřazená komponenta `@bind` však může použít syntaxi s parametrem komponenty.

Následující `PasswordField` komponenta (*PasswordField.razor*):

* Nastaví `<input>` hodnotu prvku `Password` na vlastnost.
* Zpřístupní změny `Password` vlastnosti nadřazené součásti pomocí [zpětného volání události](xref:blazor/event-handling#eventcallback).
* Používá `onclick` událost se používá `ToggleShowPassword` k aktivaci metody. Další informace naleznete v tématu <xref:blazor/event-handling>.

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

Komponenta se `PasswordField` používá v jiné součásti:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

Chcete-li provést kontroly nebo soutisk chyby na heslo v předchozím příkladu:

* Vytvořte záložní `Password` pole`_password` pro ( v následujícím příkladu kódu).
* Proveďte kontroly nebo `Password` soutisk chyby v setter.

Následující příklad poskytuje okamžitou zpětnou vazbu pro uživatele, pokud je místo použito v hodnotě hesla:

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

Informace o vazbě na přepínací tlačítka ve formuláři naleznete v tématu <xref:blazor/forms-validation#work-with-radio-buttons>.
