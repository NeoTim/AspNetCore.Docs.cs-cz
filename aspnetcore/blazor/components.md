---
title: Vytváření a používání ASP.NET Corech komponent Razor
author: guardrex
description: Naučte se vytvářet a používat komponenty Razor, včetně toho, jak navazovat na data, zpracovávat události a spravovat životní cykly komponent.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: blazor/components
ms.openlocfilehash: a71bbf3921417cbd23aeb14d0d78ad8354d6e93a
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378692"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Vytváření a používání ASP.NET Corech komponent Razor

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Aplikace Blazor jsou sestavené pomocí *komponent*. Součást je samostatně obsažený blok uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář. Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vkládání dat nebo reakci na události uživatelského rozhraní. Komponenty jsou flexibilní a odlehčené. Můžou být vnořené, opakovaně používané a sdílené mezi projekty.

## <a name="component-classes"></a>Třídy komponent

Komponenty jsou implementovány v souborech komponenty [Razor](xref:mvc/views/razor) ( *. Razor*) pomocí kombinace kódu C# a kódu HTML. Komponenta v Blazor je formálně označována jako *Komponenta Razor*.

Název součásti musí začínat velkým znakem. Například *MyCoolComponent. Razor* je platný a *MyCoolComponent. Razor* je neplatný.

Uživatelské rozhraní pro komponentu je definováno pomocí jazyka HTML. Dynamická logika vykreslování (například smyčky, podmíněné výrazy, výrazy) se přidá pomocí vložené C# syntaxe s názvem [Razor](xref:mvc/views/razor). Při kompilaci aplikace jsou značky kódu HTML a C# vykreslovací logiky převedeny na třídu součásti. Název generované třídy se shoduje s názvem souboru.

Členy třídy komponenty jsou definovány v bloku `@code`. V bloku `@code` je stav součásti (vlastnosti, pole) zadán pomocí metod pro zpracování událostí nebo pro definování jiné logiky komponent. Je přípustný více než jeden blok `@code`.

> [!NOTE]
> V předchozích verzích Preview ASP.NET Core 3,0 byly `@functions` bloků použity pro stejný účel jako bloky `@code` v součástech Razor. bloky `@functions` nadále fungují v součástech Razor, ale doporučujeme použít blok `@code` v ASP.NET Core 3,0 Preview 6 nebo novějším.

Členy součásti lze použít jako součást logiky vykreslování komponenty pomocí C# výrazů, které začínají na `@`. Například C# pole se vykreslí pomocí prefixování `@` na název pole. Následující příklad vyhodnocuje a vykresluje:

* `_headingFontStyle` pro hodnotu vlastnosti CSS pro `font-style`.
* `_headingText` k obsahu prvku `<h1>`.

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Po prvním vykreslení komponenty vygeneruje komponenta znovu svůj strom vykreslování v reakci na události. Blazor pak porovná nový strom vykreslování s předchozí a použije všechny změny v model DOM (Document Object Model) v prohlížeči (DOM).

Komponenty jsou běžné C# třídy a lze je umístit kamkoli v rámci projektu. Komponenty, které tvoří webové stránky, jsou obvykle umístěny ve složce *stránky* . Komponenty mimo stránku jsou často umístěny ve *sdílené* složce nebo vlastní složce přidané do projektu. Chcete-li použít vlastní složku, přidejte obor názvů vlastní složky do nadřazené komponenty nebo do souboru *_Imports. Razor* aplikace. Například následující obor názvů zpřístupňuje komponenty ve složce *Components* , když je kořenový obor názvů aplikace `WebApplication`:

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>Integrace součástí do aplikací Razor Pages a MVC

Použijte komponenty se stávajícími Razor Pages a MVC aplikacemi. Aby bylo možné použít součásti Razor, není nutné přepsat existující stránky ani zobrazení. Po vykreslení stránky nebo zobrazení jsou komponenty předem vygenerovány ve stejnou dobu.

Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte pomocnou metodu HTML `RenderComponentAsync<TComponent>`:

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

I když stránky a zobrazení mohou používat komponenty, není tato konverzace pravdivá. Komponenty nemůžou používat scénáře zobrazení a stránky, jako jsou například částečná zobrazení a oddíly. Chcete-li použít logiku z částečného zobrazení v komponentě, rozložte logiku částečného zobrazení do komponenty.

Další informace o tom, jak se komponenty vykreslují a stav komponenty se spravuje v aplikacích Blazor Server, najdete v článku o @no__t 0.

## <a name="use-components"></a>Použití komponent

Komponenty mohou zahrnovat další komponenty deklarováním pomocí syntaxe elementu HTML. Označení pro použití komponenty vypadá jako značka HTML, kde název značky je typ komponenty.

Vazba atributu rozlišuje velká a malá písmena. Například `@bind` je platný a `@Bind` je neplatné.

Následující kód v *indexu. Razor* vykreslí instanci `HeadingComponent`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

*Components/HeadingComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

Pokud komponenta obsahuje element HTML s velkým prvním písmenem, které neodpovídá názvu komponenty, je vygenerováno upozornění označující, že element má neočekávaný název. Přidání příkazu `@using` pro obor názvů součásti zpřístupňuje komponentu, což odstraní upozornění.

## <a name="component-parameters"></a>Parametry součásti

Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí veřejných vlastností třídy komponenty s atributem `[Parameter]`. Použijte atributy k určení argumentů pro komponentu v kódu.

*Components/ChildComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

V následujícím příkladu `ParentComponent` nastaví hodnotu vlastnosti `Title` `ChildComponent`.

*Stránky/ParentComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a>Podřízený obsah

Komponenty mohou nastavit obsah jiné součásti. Součást přiřazení poskytuje obsah mezi značkami, které určují přijímací komponentu.

V následujícím příkladu má `ChildComponent` vlastnost `ChildContent`, která představuje `RenderFragment`, která představuje segment uživatelského rozhraní, které se má vykreslit. Hodnota `ChildContent` je umístěna v označení komponenty, kde má být obsah vykreslen. Hodnota `ChildContent` je přijímána z nadřazené komponenty a vykreslena v @no__t panelu zaváděcího programu-1.

*Components/ChildComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Vlastnost, která přijímá obsah `RenderFragment`, musí mít název `ChildContent` podle konvence.

Následující `ParentComponent` může poskytnout obsah pro vykreslování `ChildComponent` umístěním obsahu uvnitř značek `<ChildComponent>`.

*Stránky/ParentComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Seskupováním atributů a libovolné parametry

Komponenty mohou kromě deklarovaných parametrů komponenty zachytit a vykreslovat další atributy. Další atributy mohou být zachyceny ve slovníku a poté *splatted* na prvek při vykreslení komponenty pomocí direktivy [@attributes](xref:mvc/views/razor#attributes) Razor. Tento scénář je užitečný při definování komponenty, která vytváří prvek značky, který podporuje nejrůznější přizpůsobení. Například může být zdlouhavé definovat atributy samostatně pro `<input>`, který podporuje mnoho parametrů.

V následujícím příkladu první prvek `<input>` (`id="useIndividualParams"`) používá jednotlivé parametry komponenty, zatímco druhý prvek `<input>` (`id="useAttributesDict"`) používá atribut seskupováním:

```cshtml
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

Typ parametru musí implementovat `IEnumerable<KeyValuePair<string, object>>` s klíči řetězce. Použití `IReadOnlyDictionary<string, object>` je také možností v tomto scénáři.

Vykreslené prvky `<input>` pomocí obou přístupů jsou identické:

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

Chcete-li přijmout libovolné atributy, definujte parametr komponenty pomocí atributu `[Parameter]` s vlastností `CaptureUnmatchedValues` nastavenou na `true`:

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

Vlastnost `CaptureUnmatchedValues` u `[Parameter]` umožňuje, aby parametr odpovídal všem atributům, které se neshodují s žádným jiným parametrem. Komponenta může definovat pouze jeden parametr s `CaptureUnmatchedValues`. Typ vlastnosti používaný s `CaptureUnmatchedValues` musí být možné přiřadit z `Dictionary<string, object>` s klíči řetězce. v tomto scénáři jsou také možnosti `IEnumerable<KeyValuePair<string, object>>` nebo `IReadOnlyDictionary<string, object>`.

## <a name="data-binding"></a>Datová vazba

Datové vazby na součásti a prvky modelu DOM jsou provedeny atributem [@bind](xref:mvc/views/razor#bind) . Následující příklad váže vlastnost `CurrentValue` na hodnotu textového pole:

```cshtml
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Pokud textové pole ztratí fokus, je hodnota vlastnosti aktualizována.

Textové pole je aktualizováno v uživatelském rozhraní pouze v případě, že je komponenta vykreslena, nikoli v reakci na změnu hodnoty vlastnosti. Vzhledem k tomu, že se komponenty vykreslují po spuštění kódu obslužné rutiny události, se aktualizace vlastností *obvykle* projeví v uživatelském rozhraní hned po aktivaci obslužné rutiny události.

Použití `@bind` s vlastností `CurrentValue` (`<input @bind="CurrentValue" />`) je v podstatě ekvivalentní následujícímu:

```cshtml
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Když je komponenta vykreslena, `value` vstupního prvku pochází z vlastnosti `CurrentValue`. Když uživatel zadá do textového pole a změní fokus prvku, aktivuje se událost `onchange` a vlastnost `CurrentValue` je nastavena na změněnou hodnotu. Ve skutečnosti je generování kódu složitější, protože `@bind` zpracovává případy, kdy jsou prováděny převody typu. V zásadě `@bind` přidruží aktuální hodnotu výrazu k atributu `value` a zpracovává změny pomocí registrované obslužné rutiny.

Kromě zpracování událostí `onchange` se syntaxí `@bind` může být vlastnost nebo pole svázána s jinými událostmi, a to zadáním atributu [@bind-value](xref:mvc/views/razor#bind) s parametrem `event` ([@bind-value:event](xref:mvc/views/razor#bind)). Následující příklad váže vlastnost `CurrentValue` pro událost `oninput`:

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Na rozdíl od `onchange`, která je aktivována, když prvek ztratí fokus, `oninput` je aktivována při změně hodnoty textového pole.

**Hodnoty, které nelze analyzovat**

Když uživatel poskytne neanalyzovatelné hodnoty prvku DataBound, hodnota neanalyzovat se automaticky vrátí na předchozí hodnotu, když se aktivuje událost BIND.

Vezměte v úvahu následující scénář:

* Element `<input>` je vázán na typ `int` s počáteční hodnotou `123`:

  ```cshtml
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* Uživatel aktualizuje hodnotu prvku tak, aby na stránce `123.45` a změnil fokus prvku.

V předchozím scénáři je hodnota elementu vrácena na `123`. Pokud je hodnota `123.45` zamítnuta ve prospěch původní hodnoty `123`, uživateli se rozumí, že jejich hodnota nebyla přijata.

Ve výchozím nastavení se vazba vztahuje na událost `onchange` elementu (`@bind="{PROPERTY OR FIELD}"`). K nastavení jiné události použijte `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}`. U události `oninput` (`@bind-value:event="oninput"`) dojde k opětovnému nastavování po stisku klávesových zkratek, které zavádí neanalyzovatelné hodnoty. Při cílení na událost `oninput` s typem vázaným @no__t -1 může uživatel zabránit psaní znaku `.`. Znak `.` je okamžitě odebrán, takže uživatel obdrží okamžitou zpětnou vazbu, že jsou povolena pouze celá čísla. K dispozici jsou situace, kdy vrácení hodnoty na událost `oninput` není ideální, například pokud by měl uživatel povoleno vymazat neanalyzovatelné hodnoty `<input>`. K alternativám patří:

* Nepoužívejte událost `oninput`. Použijte výchozí událost `onchange` (`@bind="{PROPERTY OR FIELD}"`), kde neplatnou hodnotu vrátí, dokud prvek neztratí fokus.
* Vytvořte propojení s typem s možnou hodnotou null, například `int?` nebo `string` a poskytněte vlastní logiku pro zpracování neplatných položek.
* Použijte [součást ověřování formuláře](xref:blazor/forms-validation), například `InputNumber` nebo `InputDate`. Komponenty ověřování formuláře mají integrovanou podporu pro správu neplatných vstupů. Součásti pro ověření formuláře:
  * Povolí uživateli zadání neplatných vstupů a přijetí chyb ověřování na přidružených `EditContext`.
  * Zobrazí chyby ověřování v uživatelském rozhraní, aniž by došlo ke konfliktu s uživatelem, který zadává další data z formuláře.

**Globalizace**

hodnoty `@bind` jsou formátovány pro zobrazení a analýzu pomocí pravidel aktuální jazykové verze.

K aktuální jazykové verzi lze přistupovat z vlastnosti <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.

[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) se používá pro následující typy polí (`<input type="{TYPE}" />`):

* `date`
* `number`

Předchozí typy polí:

* Se zobrazují pomocí příslušných pravidel formátování založených na prohlížeči.
* Text s volným formulářem nemůže obsahovat.
* Poskytněte charakteristiky interakce s uživatelem na základě implementace prohlížeče.

Následující typy polí mají specifické požadavky na formátování a aktuálně nejsou podporovány v Blazor, protože nejsou podporovány ve všech hlavních prohlížečích:

* `datetime-local`
* `month`
* `week`

`@bind` podporuje parametr `@bind:culture` k poskytnutí <xref:System.Globalization.CultureInfo?displayProperty=fullName> pro analýzu a formátování hodnoty. Zadání jazykové verze se nedoporučuje při použití typů polí `date` a `number`. `date` a `number` mají integrovanou podporu Blazor, která poskytuje požadovanou jazykovou verzi.

Informace o tom, jak nastavit jazykovou verzi uživatele, najdete v části [lokalizace](#localization) .

**Řetězce formátu**

Datové vazby fungují s řetězci formátu <xref:System.DateTime> pomocí [@bind:format](xref:mvc/views/razor#bind). Jiné formátovací výrazy, jako je například Měna nebo formáty čísel, nejsou v tuto chvíli k dispozici.

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

V předchozím kódu je typ pole elementu `<input>` (`type`) standardně `text`. `@bind:format` se podporuje pro vazby následujících typů .NET:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

Atribut `@bind:format` určuje formát data, který má být použit pro `value` prvku `<input>`. Formát se používá také k analýze hodnoty při výskytu události `onchange`.

Zadání formátu pro typ pole `date` se nedoporučuje, protože Blazor má vestavěnou podporu pro formátování kalendářních dat.

**Parametry součásti**

Vazba rozpoznává parametry komponenty, kde `@bind-{property}` může svázat hodnotu vlastnosti napříč komponentami.

Následující podřízená komponenta (`ChildComponent`) má parametr komponenty `Year` a zpětné volání `YearChanged`:

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

`EventCallback<T>` je vysvětleno v části [vnořenou eventCallback](#eventcallback) .

Následující nadřazená komponenta používá `ChildComponent` a váže parametr `ParentYear` z nadřazené položky k parametru `Year` v podřízené komponentě:

```cshtml
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

Pokud se hodnota vlastnosti `ParentYear` změní výběrem tlačítka v `ParentComponent`, bude aktualizována vlastnost `Year` `ChildComponent`. Nová hodnota `Year` se vykreslí v uživatelském rozhraní, když se `ParentComponent` znovu vykreslí:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Parametr `Year` je svázán, protože obsahuje doprovodnou událost `YearChanged`, která odpovídá typu parametru `Year`.

Podle konvence `<ChildComponent @bind-Year="ParentYear" />` v podstatě ekvivalentem zápisu:

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Obecně platí, že vlastnost může být svázána s odpovídající obslužnou rutinou události pomocí atributu `@bind-property:event`. Například vlastnost `MyProp` může být svázána s `MyEventHandler` pomocí následujících dvou atributů:

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a>Zpracování událostí

Komponenty Razor poskytují funkce pro zpracování událostí. Pro atribut elementu HTML s názvem `on{event}` (například `onclick` a `onsubmit`) s hodnotou delegovanou pomocí komponenty Razor se hodnota atributu považuje za obslužnou rutinu události. Název atributu má vždycky formát [@on {Event}](xref:mvc/views/razor#onevent).

Následující kód volá metodu `UpdateHeading`, pokud je vybráno tlačítko v uživatelském rozhraní:

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

Následující kód volá metodu `CheckChanged`, pokud je zaškrtávací políčko v uživatelském rozhraní změněno:

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Obslužné rutiny událostí mohou být také asynchronní a vracet <xref:System.Threading.Tasks.Task>. Není nutné ručně volat `StateHasChanged()`. Výjimky jsou protokolovány, když k nim dojde.

V následujícím příkladu je `UpdateHeading` volána asynchronně, když je vybráno tlačítko:

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a>Typy argumentů události

U některých událostí jsou povoleny typy argumentů události. Pokud přístup k některému z těchto typů událostí není nezbytný, není nutné ve volání metody.

Podporované `EventArgs` jsou uvedeny v následující tabulce.

| Událost | Třída |
| ----- | ----- |
| Schránka        | `ClipboardEventArgs` |
| Myší             | `DragEventArgs` &ndash; `DataTransfer` a `DataTransferItem` blokování dat přetažených položek. |
| Chyba            | `ErrorEventArgs` |
| Vybrána            | `FocusEventArgs` &ndash; nezahrnuje podporu pro `relatedTarget`. |
| Změna `<input>` | `ChangeEventArgs` |
| Klávesnice         | `KeyboardEventArgs` |
| Stisknut            | `MouseEventArgs` |
| Ukazatel myši    | `PointerEventArgs` |
| Kolečko myši      | `WheelEventArgs` |
| Průběh         | `ProgressEventArgs` |
| Dotykové ovládání            | `TouchEventArgs` &ndash; `TouchPoint` představuje jeden kontaktní bod na zařízení citlivém na dotykové ovládání. |

Informace o vlastnostech a chování zpracování událostí událostí v předchozí tabulce naleznete v tématu [třídy EventArgs ve zdroji referencí (ASPNET/AspNetCore Release/3.0)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).

### <a name="lambda-expressions"></a>Výrazy lambda

Lambda výrazy lze také použít:

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Je často vhodné uzavřít další hodnoty, jako například při iteraci přes sadu prvků. Následující příklad vytvoří tři tlačítka, z nichž každá volá `UpdateHeading`, která předá argument události (`MouseEventArgs`) a číslo jeho tlačítka (`buttonNumber`), pokud je vybrána v uživatelském rozhraní:

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> Nepoužívejte **proměnnou** smyčky (`i`) ve smyčce `for` přímo ve výrazu lambda. Jinak je stejná proměnná používána všemi výrazy lambda, což způsobí, že hodnota `i` bude stejná ve všech výrazech lambda. Vždycky zachytit svou hodnotu v místní proměnné (`buttonNumber` v předchozím příkladu) a pak ji použít.

### <a name="eventcallback"></a>Vnořenou eventCallback

Běžný scénář s vnořenými komponentami je přáním spustit metodu nadřazené komponenty, když dojde k události podřízené komponenty @ no__t-0for, když dojde k události `onclick` v podřízeném objektu. Chcete-li zobrazit události napříč komponentami, použijte `EventCallback`. Nadřazená komponenta může přiřadit metodu zpětného volání podřízené součásti `EventCallback`.

@No__t-0 v ukázkové aplikaci ukazuje, jak je nastavená obslužná rutina `onclick` tlačítka pro příjem delegáta `EventCallback` z `ParentComponent` ukázky. @No__t-0 se zadává pomocí `MouseEventArgs`, který je vhodný pro událost `onclick` z periferního zařízení:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

@No__t-0 nastaví `EventCallback<T>` dítěte na jeho metodu `ShowMessage`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

Když je vybráno tlačítko v `ChildComponent`:

* Je volána metoda `ParentComponent` `ShowMessage`. `messageText` se aktualizuje a zobrazí v `ParentComponent`.
* Volání metody `StateHasChanged` není vyžadováno v metodě zpětného volání (`ShowMessage`). `StateHasChanged` se zavolá automaticky a znovu vykreslí `ParentComponent` stejně jako podřízené události, které aktivují revykreslování komponenty v obslužných rutinách událostí, které se spouštějí v rámci podřízeného objektu.

`EventCallback` a `EventCallback<T>` povolí asynchronní delegáty. `EventCallback<T>` je silného typu a vyžaduje konkrétní typ argumentu. `EventCallback` je slabě typované a umožňuje jakýkoli typ argumentu.

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

Vyvolat `EventCallback` nebo `EventCallback<T>` s `InvokeAsync` a očekávat <xref:System.Threading.Tasks.Task>:

```csharp
await callback.InvokeAsync(arg);
```

Pro zpracování událostí a parametry komponenty vazby použijte `EventCallback` a `EventCallback<T>`.

Dáváte přednost silnému typu `EventCallback<T>` přes `EventCallback`. `EventCallback<T>` poskytuje lepší odezvu na chyby uživatelů součásti. Podobně jako u jiných obslužných rutin událostí uživatelského rozhraní je zadání parametru události volitelné. Pokud zpětnému volání není předáno žádné číslo, použijte `EventCallback`.

## <a name="chained-bind"></a>Zřetězená vazba

Běžným scénářem je zřetězení parametru vázaného na data na element stránky ve výstupu součásti. Tento scénář se nazývá *zřetězená vazba* , protože k více úrovním vazby dochází současně.

Zřetězenou BIND nelze implementovat s syntaxí `@bind` v elementu stránky. Obslužná rutina události a hodnota se musí zadat samostatně. Nadřazená komponenta však může použít syntaxi `@bind` s parametrem součásti.

Následující součást `PasswordField` (*PasswordField. Razor*):

* Nastaví hodnotu elementu `<input>` na vlastnost `Password`.
* Zveřejňuje změny vlastnosti `Password` u nadřazené komponenty pomocí [vnořenou eventCallback](#eventcallback).

```cshtml
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

Komponenta `PasswordField` se používá v jiné součásti:

```cshtml
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Chcete-li provést kontrolu nebo chyby depeše v předchozím příkladu:

* Vytvořte pole pro zálohování pro `Password` (`password` v následujícím ukázkovém kódu).
* Proveďte kontroly nebo chyby depeší v metodě setter `Password`.

Následující příklad poskytuje okamžitou zpětnou vazbu uživateli, pokud se v hodnotě hesla používá mezera:

```cshtml
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

## <a name="capture-references-to-components"></a>Zachytit odkazy na komponenty

Odkazy na součásti poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance, například `Show` nebo `Reset`. Zachytit odkaz na komponentu:

* Přidejte atribut [@ref](xref:mvc/views/razor#ref) do podřízené součásti.
* Definujte pole stejného typu jako podřízená komponenta.

```cshtml
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Při vykreslení komponenty se v poli `loginDialog` naplní instance podřízené komponenty `MyLoginDialog`. Pak můžete vyvolat metody .NET v instanci komponenty.

> [!IMPORTANT]
> Proměnná `loginDialog` je naplněna pouze po vykreslení komponenty a její výstup obsahuje prvek `MyLoginDialog`. Do tohoto okamžiku neexistuje žádný odkaz na. Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování komponenty, použijte [metody OnAfterRenderAsync nebo OnAfterRender](#lifecycle-methods).

Při zachytávání odkazů na součásti použijte podobnou syntaxi pro [zachycení odkazů na prvky](xref:blazor/javascript-interop#capture-references-to-elements), není to funkce [interoperability JavaScriptu](xref:blazor/javascript-interop) . Odkazy na součásti nejsou předány kódu JavaScriptu @ no__t-0they're, který se používá pouze v kódu .NET.

> [!NOTE]
> Nepoužívejte odkazy na součásti **pro použití stavu** podřízených komponent. Místo toho použijte k předání dat podřízeným komponentám běžné deklarativní parametry. Použití běžných deklarativních parametrů má za následek podřízené komponenty, které jsou automaticky revykreslovány ve správný čas.

## <a name="invoke-component-methods-externally-to-update-state"></a>Vyvolat metody komponenty externě na stav aktualizace

Blazor používá `SynchronizationContext` k vykonání jednoho logického vlákna provádění. Metody životního cyklu komponenty a všechna zpětná volání událostí, která jsou aktivována nástrojem Blazor, jsou spouštěna v tomto `SynchronizationContext`. V případě, že komponenta musí být aktualizována na základě externí události, jako je například časovač nebo jiné oznámení, použijte metodu `InvokeAsync`, která se odešle do `SynchronizationContext` Blazor.

Představte si například *službu* pro upozorňování, která může oznámit všechny součásti, které jsou v aktualizovaném stavu:

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

Použití `NotifierService` k aktualizaci součásti:

```cshtml
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

V předchozím příkladu `NotifierService` vyvolá metodu `OnNotify` komponenty mimo Blazor `SynchronizationContext`. `InvokeAsync` slouží k přepnutí do správného kontextu a vykreslení vykreslování do fronty.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Použití \@key k řízení uchovávání prvků a komponent

Při vykreslování seznamu prvků nebo komponent a následné změny prvků nebo komponent musí být Blazor rozdílový algoritmus rozhodnout, které z předchozích prvků nebo komponent lze zachovat a jak se mají objekty modelu namapovat. Obvykle je tento proces automatický a může být ignorován, ale existují případy, kdy můžete chtít řídit proces.

Vezměte v úvahu následující příklad:

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Obsah kolekce `People` se může změnit vloženými, odstraněnými nebo znovu seřazenými položkami. Když se komponenta znovu vykreslí, součást `<DetailsEditor>` se může změnit, aby přijímala různé hodnoty parametrů `Details`. To může způsobit složitější revykreslování, než se očekávalo. V některých případech může reprodukce vést k viditelným rozdílům v chování, jako je například ztracený fokus elementu.

Proces mapování lze řídit pomocí atributu direktivy `@key`. `@key` způsobí, že rozdílový algoritmus zaručuje zachování prvků nebo komponent na základě hodnoty klíče:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Když se změní kolekce `People`, rozdílový algoritmus zachová přidružení mezi instancemi `<DetailsEditor>` a instancemi `person`:

* Pokud se ze seznamu `People` odstraní `Person`, z uživatelského rozhraní se odebere jenom odpovídající instance `<DetailsEditor>`. Ostatní instance zůstanou beze změny.
* Pokud je na některém místě v seznamu vložená `Person`, do příslušné pozice se vloží jedna nová instance `<DetailsEditor>`. Ostatní instance zůstanou beze změny.
* Pokud jsou položky `Person` znovu objednány, odpovídající instance `<DetailsEditor>` jsou zachovány a znovu uspořádány v uživatelském rozhraní.

V některých scénářích použití `@key` minimalizuje složitost opětovného vykreslování a vyhnete se potenciálním problémům se měnícími se stavovou částí modelu DOM, jako je například pozice fokusu.

> [!IMPORTANT]
> Klíče jsou místní pro každý prvek kontejneru nebo komponentu. Klíče nejsou v dokumentu globálně porovnány.

### <a name="when-to-use-key"></a>Kdy použít @no__t – 0key

Obvykle má smysl použít `@key` při každém vykreslení seznamu (například v bloku `@foreach`) a existuje vhodná hodnota pro definování `@key`.

@No__t-0 můžete také použít, chcete-li zabránit Blazor v zachování prvku nebo podstromu komponenty při změně objektu:

```cshtml
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Pokud se změní `@currentPerson`, direktiva Attribute `@key` vynutí, aby Blazor zahození celého `<div>` a jeho následníků a znovu sestaví podstrom v uživatelském rozhraní s novými prvky a komponentami. To může být užitečné, pokud potřebujete zaručit, že při změně `@currentPerson` se nezachová žádný stav uživatelského rozhraní.

### <a name="when-not-to-use-key"></a>Kdy použít @no__t – 0key

Při rozdílech s `@key` se účtují náklady na výkon. Náklady na výkon nejsou velké, ale zadávejte pouze `@key`, pokud řízení pravidel uchovávání prvků nebo komponent těží z aplikace.

I v případě, že `@key` se nepoužívá, Blazor zachová podřízený element a instance komponenty co nejvíce. Jedinou výhodou použití `@key` je řízení způsobu, *jakým* jsou instance modelů mapovány na zachované instance komponent namísto rozdílového algoritmu výběru mapování.

### <a name="what-values-to-use-for-key"></a>Jaké hodnoty se mají použít pro \@key

Obecně dává smysl, aby jeden z následujících typů hodnot `@key`:

* Instance objektů modelu (například instance `Person` jako v předchozím příkladu). To zajišťuje zachování v závislosti na rovnosti odkazů na objekty.
* Jedinečné identifikátory (například hodnoty primárního klíče typu `int`, `string` nebo `Guid`).

Zajistěte, aby hodnoty používané pro `@key` nekolidují. Pokud jsou v rámci stejného nadřazeného prvku zjištěny hodnoty střetu, Blazor vyvolá výjimku, protože nemůže deterministické namapovat staré prvky nebo komponenty na nové prvky nebo komponenty. Používejte pouze jedinečné hodnoty, například instance objektů nebo hodnoty primárního klíče.

## <a name="lifecycle-methods"></a>Metody životního cyklu

`OnInitializedAsync` a `OnInitialized` spustí kód pro inicializaci komponenty. K provedení asynchronní operace použijte `OnInitializedAsync` a klíčové slovo `await` pro operaci:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

> [!NOTE]
> Asynchronní práce během inicializace komponenty se musí vyskytnout během události `OnInitializedAsync` životního cyklu.

Pro synchronní operaci použijte `OnInitialized`:

```csharp
protected override void OnInitialized()
{
    ...
}
```

`OnParametersSetAsync` a `OnParametersSet` jsou volány, když komponenta přijímá parametry z nadřazené položky a hodnoty jsou přiřazeny vlastnostem. Tyto metody jsou spouštěny po inicializaci komponenty a pokaždé, když je vykreslena komponenta:

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Asynchronní práce při aplikování parametrů a hodnot vlastností musí nastat během události `OnParametersSetAsync` životního cyklu.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

`OnAfterRenderAsync` a `OnAfterRender` jsou volány po dokončení vykreslování součásti. V tuto chvíli se naplní odkazy na element a komponentu. Tuto fázi použijte k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace knihoven JavaScript třetích stran, které pracují s vykreslenými prvky modelu DOM.

*při předvykreslování na serveru se nevolá* `OnAfterRender`.

Parametr `firstRender` pro `OnAfterRenderAsync` a `OnAfterRender` je:

* Nastavte na `true` při prvním vyvolání instance komponenty.
* Zajistí, že se práce s inicializací provádí jenom jednou.

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> Asynchronní práce ihned po vykreslení musí nastat během události životního cyklu `OnAfterRenderAsync`.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

### <a name="handle-incomplete-async-actions-at-render"></a>Zpracovat nedokončené asynchronní akce při vykreslení

Asynchronní akce provedené v událostech životního cyklu se možná nedokončily, než se komponenta vykreslí. Objekty mohou být `null` nebo kompletně naplněna daty při provádění metody životního cyklu. Poskytněte logiku vykreslování pro potvrzení, že jsou objekty inicializovány. Vykreslí zástupné prvky uživatelského rozhraní (například zprávu o načítání), zatímco objekty jsou `null`.

V komponentě `FetchData` v šablonách Blazor `OnInitializedAsync` bude přepsáno, aby asynchronně přijímat data předpovědi (`forecasts`). Pokud je `forecasts` `null`, uživateli se zobrazí zpráva o načítání. Po dokončení `Task` vráceného `OnInitializedAsync` se komponenta znovu vykreslí s aktualizovaným stavem.

*Stránky/FetchData. Razor*:

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a>Spustit kód před nastavením parametrů

`SetParameters` může být přepsáno, aby se spustil kód před nastavením parametrů:

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

Pokud není vyvoláno `base.SetParameters`, vlastní kód může interpretovat hodnotu příchozích parametrů jakýmkoli způsobem, který je vyžadován. Například příchozí parametry nemusejí být přiřazeny vlastnostem třídy.

### <a name="suppress-refreshing-of-the-ui"></a>Potlačit aktualizaci uživatelského rozhraní

`ShouldRender` se dá přepsat, aby se potlačila aktualizace uživatelského rozhraní. Pokud implementace vrátí `true`, uživatelské rozhraní se aktualizuje. I když je přepsána hodnota `ShouldRender`, komponenta je vždy zpočátku vykreslena.

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a>Vyřazení komponent pomocí IDisposable

Pokud komponenta implementuje <xref:System.IDisposable>, [Metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) se zavolá, když se komponenta odebere z uživatelského rozhraní. Následující komponenta používá `@implements IDisposable` a metodu `Dispose`:

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a>Směrování

Směrování v Blazor se dosahuje tak, že poskytuje šablonu směrování pro každou dostupnou součást aplikace.

Když je zkompilován soubor Razor s direktivou `@page`, vygenerovaná Třída předává <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> určující šablonu trasy. V době běhu Směrovač vyhledává třídy komponent s `RouteAttribute` a vykreslí, že každá komponenta má šablonu směrování, která odpovídá požadované adrese URL.

Pro komponentu lze použít více šablon směrování. Následující komponenta reaguje na požadavky na `/BlazorRoute` a `/DifferentBlazorRoute`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a>Parametry směrování

Komponenty mohou přijímat parametry tras z šablony trasy uvedené v direktivě `@page`. Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponent.

*Komponenta parametru směrování*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

Volitelné parametry nejsou podporované, takže se v předchozím příkladu použijí dvě direktivy `@page`. První umožňuje navigaci na součást bez parametru. Druhá direktiva `@page` přijímá parametr trasy `{text}` a přiřazuje hodnotu vlastnosti `Text`.

## <a name="base-class-inheritance-for-a-code-behind-experience"></a>Dědičnost základní třídy pro prostředí s kódem na pozadí

Soubory součástí kombinují značky HTML C# a zpracovávají kód ve stejném souboru. Direktiva `@inherits` se dá použít k poskytování aplikací Blazor s prostředím "kódu" na pozadí, které odděluje značku komponenty od zpracování kódu.

[Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje, jak komponenta může dědit základní třídu, `BlazorRocksBase`, k poskytnutí vlastností a metod součásti.

*Stránky/BlazorRocks. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

*BlazorRocksBase.cs*:

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

Základní třída by měla být odvozena od `ComponentBase`.

## <a name="import-components"></a>Importovat součásti

Obor názvů komponenty, která je vytvořená pomocí Razor, je založen na (v pořadí podle priority):

* [@namespace](xref:mvc/views/razor#namespace) označení v souboru Razor ( *. Razor*) značky (`@namespace BlazorSample.MyNamespace`).
* Projektový `RootNamespace` v souboru projektu (`<RootNamespace>BlazorSample</RootNamespace>`).
* Název projektu, pořízený z názvu souboru projektu ( *. csproj*) a cesta z kořenového adresáře projektu ke komponentě. Například rozhraní řeší *{Project root}/pages/index.Razor* (*BlazorSample. csproj*) na obor názvů `BlazorSample.Pages`. Komponenty následují C# pravidla vazeb názvů. V tomto příkladu komponenty `Index` jsou součástí oboru všechny komponenty:
  * Ve stejné složce *stránky*.
  * Komponenty v kořenu projektu, které explicitně neurčují jiný obor názvů.

Komponenty definované v jiném oboru názvů se přenesou do rozsahu pomocí direktivy [@using](xref:mvc/views/razor#using) Razor.

Pokud je v *BlazorSample/Shared/* Folder jiná komponenta `NavMenu.razor`, součást se dá použít v `Index.razor` s následujícím příkazem `@using`:

```cshtml
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Na součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, které nevyžadují direktivu [@using](xref:mvc/views/razor#using) :

```cshtml
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> Kvalifikace `global::` není podporována.
>
> Import komponent s aliasem `using` příkazy-0 (například `@using Foo = Bar`) se nepodporuje.
>
> Částečně kvalifikované názvy nejsou podporovány. Například přidání `@using BlazorSample` a odkazování `NavMenu.razor` s `<Shared.NavMenu></Shared.NavMenu>` se nepodporuje.

## <a name="conditional-html-element-attributes"></a>Podmíněné atributy elementu HTML

Atributy elementu HTML jsou podmíněně vykresleny na základě hodnoty .NET. Pokud je hodnota `false` nebo `null`, atribut se nevykresluje. Pokud je hodnota `true`, atribut se vykreslí jako minimalizovaný.

V následujícím příkladu `IsCompleted` určuje, zda je v označení prvku vykreslena `checked`:

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Pokud je `IsCompleted` `true`, zaškrtávací políčko se vykreslí jako:

```html
<input type="checkbox" checked />
```

Pokud je `IsCompleted` `false`, zaškrtávací políčko se vykreslí jako:

```html
<input type="checkbox" />
```

Další informace najdete v tématu <xref:mvc/views/razor>.

> [!WARNING]
> Některé atributy HTML, jako je například [Standard ARIA](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), nebudou fungovat správně, pokud je typ .NET `bool`. V těchto případech použijte místo `bool` typ @no__t 0.

## <a name="raw-html"></a>Nezpracovaný kód HTML

Řetězce jsou obvykle vykreslovány pomocí textových uzlů modelu DOM, což znamená, že všechny značky, které mohou obsahovat, se ignorují a považují se za text literálu. Chcete-li vykreslit nezpracovaný kód HTML, zabalte obsah HTML do hodnoty @no__t 0. Hodnota je analyzována jako HTML nebo SVG a vložena do modelu DOM.

> [!WARNING]
> Vykreslování nezpracovaného HTML vytvořeného z jakéhokoli nedůvěryhodného zdroje je **bezpečnostní riziko** a mělo by se jim vyhnout!

Následující příklad ukazuje použití typu `MarkupString` pro přidání bloku statického obsahu HTML do vykresleného výstupu komponenty:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a>Komponenty se šablonami

Komponenty se šablonami jsou komponenty, které přijímají jednu nebo více šablon uživatelského rozhraní jako parametry, které lze poté použít jako součást logiky vykreslování komponenty. Komponenty založené na šablonách umožňují vytvářet komponenty vyšší úrovně, které jsou opakovaně použitelné než běžné součásti. Mezi několik příkladů patří:

* Tabulková komponenta, která uživateli umožňuje zadat šablony pro záhlaví, řádky a zápatí tabulky.
* Komponenta seznamu, která umožňuje uživateli určit šablonu pro vykreslování položek v seznamu.

### <a name="template-parameters"></a>Parametry šablony

Komponenta se šablonou je definována zadáním jednoho nebo více parametrů součásti typu `RenderFragment` nebo `RenderFragment<T>`. Fragment vykreslování představuje segment uživatelského rozhraní, které se má vykreslit. `RenderFragment<T>` převezme parametr typu, který lze zadat při vyvolání fragmentu vykreslování.

součást `TableTemplate`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

Při použití komponenty se šablonou lze parametry šablony zadat pomocí podřízených prvků, které odpovídají názvům parametrů (`TableHeader` a `RowTemplate` v následujícím příkladu):

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="template-context-parameters"></a>Kontextové parametry šablony

Argumenty součásti typu `RenderFragment<T>` předané jako elementy mají implicitní parametr s názvem `context` (například z předchozí ukázky kódu `@context.PetId`), ale můžete změnit název parametru pomocí atributu `Context` v podřízeném elementu. V následujícím příkladu atribut `Context` elementu `RowTemplate` určuje parametr `pet`:

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

Alternativně lze zadat atribut `Context` u prvku součásti. Zadaný atribut `Context` se vztahuje na všechny zadané parametry šablony. To může být užitečné, pokud chcete zadat název parametru obsahu pro implicitní podřízený obsah (bez nutnosti zalamování podřízeného elementu). V následujícím příkladu se atribut `Context` zobrazuje u prvku `TableTemplate` a vztahuje se na všechny parametry šablony:

```cshtml
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="generic-typed-components"></a>Komponenty s obecným typem

Komponenty se šablonami jsou často typu obecně typované. Například obecná komponenta `ListViewTemplate` může být použita k vykreslování hodnot `IEnumerable<T>`. Chcete-li definovat obecné komponenty, použijte direktivu [@typeparam](xref:mvc/views/razor#typeparam) pro určení parametrů typu:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

Pokud používáte komponenty s obecným typem, je parametr typu odvozený, pokud je to možné:

```cshtml
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

V opačném případě musí být parametr typu explicitně zadán pomocí atributu, který odpovídá názvu parametru typu. V následujícím příkladu `TItem="Pet"` určuje typ:

```cshtml
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a>Kaskádové hodnoty a parametry

V některých scénářích je nevhodné přesměrovat data z komponenty předchůdce na dceřinou komponentu pomocí [parametrů komponenty](#component-parameters), zejména pokud je k dispozici několik vrstev komponent. Kaskádové hodnoty a parametry řeší tento problém tím, že poskytují pohodlný způsob, jak komponenta předchůdce poskytne hodnotu všem jejím následným součástem. Kaskádové hodnoty a parametry také poskytují přístup ke koordinaci komponent.

### <a name="theme-example"></a>Příklad motivu

V následujícím příkladu z ukázkové aplikace určuje třída `ThemeInfo` informace o motivu pro přetečení hierarchie komponent tak, aby všechna tlačítka v dané části aplikace sdílela stejný styl.

*UIThemeClasses/ThemeInfo. cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Komponenta předchůdce může poskytnout kaskádovou hodnotu pomocí komponenty kaskádová hodnota. Komponenta `CascadingValue` balí podstrom hierarchie komponent a poskytuje jednu hodnotu všem součástem v rámci daného podstromu.

Například ukázková aplikace určuje informace o motivu (`ThemeInfo`) v jednom z rozložení aplikace jako kaskádový parametr pro všechny komponenty, které tvoří text rozložení vlastnosti `@Body`. `ButtonClass` je v komponentě rozložení přiřazena hodnota `btn-success`. Jakákoli odvozená komponenta může tuto vlastnost spotřebovat prostřednictvím kaskádového objektu `ThemeInfo`.

součást `CascadingValuesParametersLayout`:

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Chcete-li použít kaskádové hodnoty, komponenty deklaruje kaskádové parametry pomocí atributu `[CascadingParameter]`. Kaskádové hodnoty jsou vázány na kaskádové parametry podle typu.

V ukázkové aplikaci váže komponenta `CascadingValuesParametersTheme` kaskádovou hodnotu `ThemeInfo` k kaskádovým parametrům. Parametr slouží k nastavení třídy CSS pro jedno z tlačítek zobrazených komponentou.

součást `CascadingValuesParametersTheme`:

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

Chcete-li do stejného podstromu přenést více hodnot stejného typu, zadejte jedinečný řetězec `Name` ke každé komponentě `CascadingValue` a odpovídající `CascadingParameter`. V následujícím příkladu jsou dvě komponenty `CascadingValue` kaskádovitě odlišné instance `MyCascadingType` podle názvu:

```cshtml
<CascadingValue Value=@ParentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType ParentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

V komponentě následníka tyto parametry přebírají své hodnoty z odpovídajících kaskádových hodnot v komponentě předchůdce podle názvu:

```cshtml
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>Příklad TabSet

Kaskádové parametry také umožňují komponentám spolupracovat napříč hierarchií součástí. Podívejte se například na následující příklad *TabSet* v ukázkové aplikaci.

Ukázková aplikace obsahuje rozhraní `ITab`, které karty implementují:

[!code-csharp[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

Komponenta `CascadingValuesParametersTabSet` používá komponentu `TabSet`, která obsahuje několik součástí `Tab`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

Podřízené součásti `Tab` nejsou explicitně předány jako parametry `TabSet`. Místo toho jsou podřízené součásti `Tab` součástí podřízeného obsahu `TabSet`. @No__t-0 však stále potřebuje znát každou součást `Tab`, aby mohla vykreslovat hlavičky a aktivní kartu. Chcete-li povolit tuto koordinaci bez nutnosti dalšího kódu, komponenta `TabSet` *může poskytnout sebe samu jako kaskádovou hodnotu* , kterou pak vybraly podřízené komponenty `Tab`.

součást `TabSet`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

Podřízené komponenty `Tab` zachytí obsahující `TabSet` jako kaskádový parametr, takže součásti `Tab` přidají sebe sama do `TabSet` a koordinují, na které kartě je aktivní.

součást `Tab`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a>Šablony Razor

Fragmenty vykreslování lze definovat pomocí syntaxe šablony Razor. Šablony Razor představují způsob, jak definovat fragment uživatelského rozhraní a předpokládat následující formát:

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

Následující příklad ukazuje, jak zadat hodnoty `RenderFragment` a `RenderFragment<T>` a vykreslit šablony přímo v komponentě. Fragmenty vykreslování mohou být také předány jako argumenty [součástem šablon](#templated-components).

```cshtml
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = 
        (pet) => @<p>Your pet's name is @pet.Name.</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Vykreslený výstup předchozího kódu:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a>Ruční logika RenderTreeBuilder

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` poskytuje metody pro manipulaci s komponentami a prvky, včetně sestavování C# komponent ručně v kódu.

> [!NOTE]
> Použití `RenderTreeBuilder` pro vytváření komponent je pokročilý scénář. Poškozená komponenta (například značka neuzavřeného označení) může mít za následek nedefinované chování.

Vezměte v úvahu následující součást `PetDetails`, kterou je možné ručně vytvořit do jiné součásti:

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

V následujícím příkladu smyčka v metodě `CreateComponent` generuje tři komponenty `PetDetails`. Při volání metod `RenderTreeBuilder` pro vytvoření komponent (`OpenComponent` a `AddAttribute`) jsou pořadová čísla čísla řádků zdrojového kódu. Algoritmus rozdílu Blazor spoléhá na pořadová čísla, která odpovídají jedinečným řádkům kódu, a neliší vyvolání volání. Při vytváření komponenty s metodami `RenderTreeBuilder` nekódujte pevně argumenty pro pořadová čísla. **Použití výpočtu nebo čítače k vygenerování pořadového čísla může vést k špatnému výkonu.** Další informace naleznete v části [pořadové číslo se vztahuje na čísla řádků kódu a nikoli na oddíl pořadí provádění](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .

součást `BuiltContent`:

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> ! Upozornění Typy v `Microsoft.AspNetCore.Components.RenderTree` umožňují zpracování *výsledků* operací vykreslování. Jedná se o interní podrobnosti implementace Blazor Framework. Tyto typy by měly být považovány za *nestabilní* a mohou se změnit v budoucích verzích.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Pořadová čísla se vztahují na čísla řádků kódu a nikoli na pořadí provádění.

Soubory Blazor `.razor` jsou vždy kompilovány. To je potenciálně skvělý přínos pro `.razor`, protože krok kompilace lze použít k vložení informací, které zlepšují výkon aplikace za běhu.

Hlavní příklad těchto vylepšení zahrnuje *pořadová čísla*. Pořadová čísla označují modul runtime, ze kterého výstupy pocházejí, ze kterých se liší a seřazené řádky kódu. Modul runtime používá tyto informace k vygenerování efektivních rozdílů stromu v lineárním čase, což je mnohem rychlejší než obvykle pro obecný rozdílový algoritmus stromu.

Vezměte v úvahu následující jednoduchý soubor `.razor`:

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

Předchozí kód se zkompiluje jako následující:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Při prvním spuštění kódu, pokud `someFlag` je `true`, tvůrce obdrží:

| Sequence | Typ      | Data   |
| :------: | --------- | :----: |
| 0,8        | Textový uzel | První  |
| první        | Textový uzel | Sekunda |

Představte si, že `someFlag` se naplní `false` a kód se znovu vykreslí. Tentokrát Tvůrce získá:

| Sequence | Typ       | Data   |
| :------: | ---------- | :----: |
| první        | Textový uzel  | Sekunda |

Pokud modul runtime provede rozdíl, uvidí, že položka v sekvenci @no__t 0 byla odebrána, takže generuje následující skript triviálního *úprav*:

* Odeberte první textový uzel.

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a>Co je špatné, pokud generujete pořadová čísla programově

Představte si, že jste napsali následující logiku tvůrce stromu vykreslování:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Teď je první výstup:

| Sequence | Typ      | Data   |
| :------: | --------- | :----: |
| 0,8        | Textový uzel | První  |
| první        | Textový uzel | Sekunda |

Tento výsledek je stejný jako předchozí případ, takže neexistují žádné negativní problémy. `someFlag` je ve druhém vykreslování `false` a výstup je následující:

| Sequence | Typ      | Data   |
| :------: | --------- | ------ |
| 0,8        | Textový uzel | Sekunda |

Tentokrát rozdílový algoritmus uvidí, že došlo ke *dvěma* změnám, a algoritmus generuje následující skript pro úpravy:

* Změňte hodnotu prvního textového uzlu na `Second`.
* Odeberte druhý textový uzel.

Generování pořadových čísel ztratilo všechny užitečné informace o tom, kde se v původním kódu objevily větve `if/else` a smyčky. To vede ke rozdílu **dvakrát, jak dlouho** chcete.

Toto je triviální příklad. Ve složitějších případech se složitými a hluboce vnořenými strukturami, a to hlavně pomocí smyček, náklady na výkon jsou závažnější. Místo toho, aby se ihned identifikovaly, které bloky nebo větve smyček byly vloženy nebo odebrány, je třeba rozdílový algoritmus přepracovat hluboko do stromů vykreslování a obvykle sestavovat mnohem delší úpravu skriptů, protože je nepřehledně neinformován o tom, jak staré a nové struktury vzájemná vazba.

#### <a name="guidance-and-conclusions"></a>Doprovodné materiály a závěry

* Pokud jsou automaticky generována pořadová čísla, je výkon aplikace zhoršen.
* Rozhraní nemůže automaticky vytvořit vlastní pořadová čísla za běhu, protože potřebné informace neexistují, pokud není zachycena v době kompilace.
* Nepište dlouhé bloky ručně implementovaného `RenderTreeBuilder` logiky. Preferovat `.razor` souborů a umožňuje kompilátoru zabývat se čísly sekvence. Pokud se nemůžete vyhnout ruční logice `RenderTreeBuilder`, rozdělte dlouhé bloky kódu na menší části zabalené v `OpenRegion` @ no__t-2 @ no__t-3 volání. Každá oblast má vlastní oddělený prostor pořadových čísel, takže v každé oblasti můžete restartovat z nuly (nebo jakéhokoli jiného libovolného čísla).
* Pokud jsou pořadová čísla pevně zakódované, rozdílový algoritmus vyžaduje pouze zvýšení hodnoty pořadových čísel. Počáteční hodnota a mezery jsou nepodstatné. Jednou z oprávněných možností je použít číslo řádku kódu jako pořadové číslo nebo začít od nuly a zvýšit podle hodnoty nebo stovky (případně z upřednostňovaného intervalu). 
* Blazor používá pořadová čísla, zatímco jiné architektury uživatelského rozhraní rozdílového stromu je nepoužívají. Rozdíly jsou mnohem rychlejší při použití pořadových čísel a Blazor má výhodu kompilačního kroku, který se automaticky zabývá pořadovým číslem pro vývojáře vytvářející `.razor` souborů.

## <a name="localization"></a>Lokalizace

Aplikace Blazor serveru jsou lokalizovány pomocí [middleware pro lokalizaci](xref:fundamentals/localization#localization-middleware). Middleware vybere vhodnou jazykovou verzi pro uživatele, kteří žádají o prostředky z aplikace.

Tuto jazykovou verzi lze nastavit pomocí jednoho z následujících přístupů:

* [Soubory cookie](#cookies)
* [Poskytnutí uživatelského rozhraní pro výběr jazykové verze](#provide-ui-to-choose-the-culture)

Další informace a příklady najdete v tématu <xref:fundamentals/localization>.

### <a name="cookies"></a>Soubory cookie

Soubor cookie lokalizační kultury může zachovat jazykovou verzi uživatele. Soubor cookie je vytvořen metodou `OnGet` stránky hostitele aplikace (*Pages/Host. cshtml. cs*). Middleware lokalizace přečte soubor cookie při následných požadavcích na nastavení jazykové verze uživatele. 

Použití souboru cookie zajistí, že připojení protokolu WebSocket dokáže správně rozšířit jazykovou verzi. Pokud jsou schémata lokalizací založena na cestě URL nebo řetězci dotazu, nemusí být schopná pracovat s objekty WebSockets, takže nepůjde zachovat jazykovou verzi. Proto je doporučený přístup použití souboru cookie lokalizační kultury.

Pokud je jazyková verze uložena v souboru cookie lokalizace, je možné použít jakoukoli techniku k přiřazení jazykové verze. Pokud už aplikace má zavedené lokalizační schéma pro ASP.NET Core na straně serveru, pokračujte v používání stávající infrastruktury lokalizace a nastavte soubor cookie lokalizační kultury v rámci schématu aplikace.

Následující příklad ukazuje, jak nastavit aktuální jazykovou verzi v souboru cookie, který lze přečíst pomocí middleware Localization. Vytvořte soubor *Pages/Host. cshtml. cs* s následujícím obsahem v aplikaci Blazor Server:

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

Lokalizace se zpracovává v aplikaci:

1. Prohlížeč pošle do aplikace počáteční požadavek HTTP.
1. Jazyková verze je přiřazena pomocí middleware Localization.
1. Metoda `OnGet` v *_Host. cshtml. cs* uchovává v rámci odpovědi jazykovou verzi v souboru cookie.
1. Prohlížeč otevře připojení pomocí protokolu WebSocket a vytvoří interaktivní relaci serveru Blazor.
1. Middleware lokalizace přečte soubor cookie a přiřadí jazykovou verzi.
1. Relace serveru Blazor začíná správnou jazykovou verzí.

## <a name="provide-ui-to-choose-the-culture"></a>Poskytnutí uživatelského rozhraní pro výběr jazykové verze

K poskytnutí uživatelského rozhraní, které uživateli umožní vybrat jazykovou verzi, se doporučuje *přístup založený na přesměrování* . Proces se podobá tomu, co se stane ve webové aplikaci, když se uživatel pokusí o přístup k zabezpečenému prostředku. uživatel @ no__t-0the se přesměruje na přihlašovací stránku a pak se přesměruje zpátky na původní prostředek. 

Aplikace zachovává vybranou jazykovou verzi uživatele prostřednictvím přesměrování na kontroler. Řadič Nastaví vybranou jazykovou verzi uživatele na soubor cookie a přesměruje uživatele zpět na původní identifikátor URI.

Vytvořte koncový bod HTTP na serveru pro nastavení vybrané jazykové verze uživatele v souboru cookie a proveďte přesměrování zpátky na původní identifikátor URI:

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> Pomocí akce `LocalRedirect` zabráníte útokům v otevřeném přesměrování. Další informace najdete v tématu <xref:security/preventing-open-redirects>.

Následující komponenta ukazuje příklad, jak provést počáteční přesměrování, když uživatel vybere jazykovou verzi:

```cshtml
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-blazor-apps"></a>Použití scénářů lokalizace .NET v aplikacích Blazor

V aplikacích Blazor jsou k dispozici následující scénáře lokalizace a globalizace rozhraní .NET:

* . Systém prostředků netto
* Formátování čísla a data specifické pro jazykovou verzi

Funkce Blazor `@bind` provádí globalizaci na základě aktuální jazykové verze uživatele. Další informace najdete v části [vázání dat](#data-binding) .

V současné době se podporuje omezená sada scénářů lokalizace ASP.NET Core:

* @no__t – 0 *se* v aplikacích Blazor podporuje.
* `IHtmlLocalizer<>`, `IViewLocalizer<>` a lokalizace datových poznámek jsou ASP.NET Core scénáře MVC a **nejsou podporovány** v aplikacích Blazor.

Další informace najdete v tématu <xref:fundamentals/localization>.

## <a name="scalable-vector-graphics-svg-images"></a>Obrázky ve formátu SVG (Scalable Vector Graphics)

Vzhledem k tomu, že Blazor vykresluje HTML, obrázky podporované prohlížečem, včetně obrázků SVG (Scalable Vector Graphics) ( *. SVG*), jsou podporovány prostřednictvím značky `<img>`:

```html
<img alt="Example image" src="some-image.svg" />
```

Podobně jsou obrázky SVG podporovány v pravidlech CSS souboru šablony stylů ( *. CSS*):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Vložené značky SVG se však ve všech scénářích nepodporují. Pokud značku `<svg>` umístíte přímo do souboru komponenty ( *. Razor*), podporuje se základní vykreslování obrázků, ale mnoho pokročilých scénářů se ještě nepodporuje. Například značky `<use>` nejsou aktuálně dodrženy a `@bind` nelze použít s některými značkami SVG. Očekáváme, že tato omezení vyřešíme v budoucí verzi.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/blazor/server> &ndash; obsahuje pokyny k vytváření aplikací serveru Blazor, které musí soupeří s vyčerpáním prostředků.
