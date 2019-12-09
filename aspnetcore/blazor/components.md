---
title: Vytváření a používání ASP.NET Corech komponent Razor
author: guardrex
description: Naučte se vytvářet a používat komponenty Razor, včetně toho, jak navazovat na data, zpracovávat události a spravovat životní cykly komponent.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/components
ms.openlocfilehash: a79202565f45b4d26e280427892ea16b33f3f853
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943859"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Vytváření a používání ASP.NET Corech komponent Razor

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([stažení](xref:index#how-to-download-a-sample))

aplikace Blazor jsou sestaveny pomocí *komponent*. Součást je samostatně obsažený blok uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář. Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vkládání dat nebo reakci na události uživatelského rozhraní. Komponenty jsou flexibilní a odlehčené. Můžou být vnořené, opakovaně používané a sdílené mezi projekty.

## <a name="component-classes"></a>Třídy komponent

Komponenty jsou implementovány v souborech komponenty [Razor](xref:mvc/views/razor) ( *. Razor*) pomocí kombinace kódu C# a kódu HTML. Komponenta v Blazor je formálně označována jako *Komponenta Razor*.

Název součásti musí začínat velkým znakem. Například *MyCoolComponent. Razor* je platný a *MyCoolComponent. Razor* je neplatný.

Uživatelské rozhraní pro komponentu je definováno pomocí jazyka HTML. Dynamická logika vykreslování (například smyčky, podmíněné výrazy, výrazy) se přidá pomocí vložené C# syntaxe s názvem [Razor](xref:mvc/views/razor). Při kompilaci aplikace jsou značky kódu HTML a C# vykreslovací logiky převedeny na třídu součásti. Název generované třídy se shoduje s názvem souboru.

Členy třídy komponenty jsou definovány v `@code`ovém bloku. V bloku `@code` je stav součásti (vlastnosti, pole) zadán pomocí metod pro zpracování událostí nebo pro definování jiné logiky komponent. Je přípustný více než jeden blok `@code`.

> [!NOTE]
> V předchozích verzích Preview ASP.NET Core 3,0 byly `@functions` bloky použity pro stejný účel jako `@code` bloky v součástech Razor. bloky `@functions` nadále fungují v součástech Razor, ale doporučujeme použít blok `@code` v ASP.NET Core 3,0 Preview 6 nebo novějším.

Členy součásti lze použít jako součást logiky vykreslování komponenty pomocí C# výrazů, které začínají na `@`. Například C# pole se vykreslí pomocí předpony `@` názvu pole. Následující příklad vyhodnocuje a vykresluje:

* `_headingFontStyle` k hodnotě vlastnosti CSS pro `font-style`.
* `_headingText` k obsahu `<h1>` elementu.

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Po prvním vykreslení komponenty vygeneruje komponenta znovu svůj strom vykreslování v reakci na události. Blazor pak porovná nový strom vykreslování s předchozí a použije všechny úpravy v prohlížeči model DOM (Document Object Model) (DOM).

Komponenty jsou běžné C# třídy a lze je umístit kamkoli v rámci projektu. Komponenty, které tvoří webové stránky, jsou obvykle umístěny ve složce *stránky* . Komponenty mimo stránku jsou často umístěny ve *sdílené* složce nebo vlastní složce přidané do projektu. Chcete-li použít vlastní složku, přidejte obor názvů vlastní složky buď do nadřazené komponenty, nebo do souboru *_Imports. Razor* aplikace. Například následující obor názvů zpřístupňuje komponenty ve složce *Components* , když je kořenový obor názvů aplikace `WebApplication`:

```razor
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>Integrace součástí do aplikací Razor Pages a MVC

Použijte komponenty se stávajícími Razor Pages a MVC aplikacemi. Aby bylo možné použít součásti Razor, není nutné přepsat existující stránky ani zobrazení. Po vykreslení stránky nebo zobrazení jsou komponenty předem vygenerovány ve stejnou dobu.

::: moniker range=">= aspnetcore-3.1"

Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte pomocníka značky `Component`:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Předávání parametrů (například `IncrementAmount` v předchozím příkladu) je podporováno.

`RenderMode` nakonfiguruje, jestli součást:

* Je předem vykreslen na stránku.
* Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor od uživatelského agenta.

| `RenderMode`        | Popis |
| ------------------- | ----------- |
| `ServerPrerendered` | Vykreslí komponentu do statického HTML a obsahuje značku pro aplikaci Blazor Server. Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace. |
| `Server`            | Vykreslí značku pro aplikaci Blazor serveru. Výstup komponenty není zahrnutý. Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace. |
| `Static`            | Vykreslí komponentu do statického HTML. |

I když stránky a zobrazení mohou používat komponenty, není tato konverzace pravdivá. Komponenty nemůžou používat scénáře zobrazení a stránky, jako jsou například částečná zobrazení a oddíly. Chcete-li použít logiku z částečného zobrazení v komponentě, rozložte logiku částečného zobrazení do komponenty.

Vykreslování součástí serveru ze statické stránky HTML není podporováno.

Další informace o vykreslování komponent, stavu komponent a pomocníka značek `Component` naleznete v tématu <xref:blazor/hosting-models>.

::: moniker-end

::: moniker range="< aspnetcore-3.1"

Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte pomocnou metodu `RenderComponentAsync<TComponent>` HTML:

```cshtml
@(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
```

`RenderMode` nakonfiguruje, jestli součást:

* Je předem vykreslen na stránku.
* Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor od uživatelského agenta.

| `RenderMode`        | Popis |
| ------------------- | ----------- |
| `ServerPrerendered` | Vykreslí komponentu do statického HTML a obsahuje značku pro aplikaci Blazor Server. Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace. Parametry nejsou podporovány. |
| `Server`            | Vykreslí značku pro aplikaci Blazor serveru. Výstup komponenty není zahrnutý. Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace. Parametry nejsou podporovány. |
| `Static`            | Vykreslí komponentu do statického HTML. Jsou podporovány parametry. |

I když stránky a zobrazení mohou používat komponenty, není tato konverzace pravdivá. Komponenty nemůžou používat scénáře zobrazení a stránky, jako jsou například částečná zobrazení a oddíly. Chcete-li použít logiku z částečného zobrazení v komponentě, rozložte logiku částečného zobrazení do komponenty.

Vykreslování součástí serveru ze statické stránky HTML není podporováno.

Další informace o tom, jak se komponenty vykreslují, stav komponent a pomocníka `RenderComponentAsync` HTML, najdete v článku <xref:blazor/hosting-models>.

::: moniker-end

## <a name="use-components"></a>Použití komponent

Komponenty mohou zahrnovat další komponenty deklarováním pomocí syntaxe elementu HTML. Označení pro použití komponenty vypadá jako značka HTML, kde název značky je typ komponenty.

Vazba atributu rozlišuje velká a malá písmena. Například `@bind` je platný a `@Bind` je neplatný.

Následující kód v *indexu. Razor* vykresluje instanci `HeadingComponent`:

```razor
<HeadingComponent />
```

*Components/HeadingComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Pokud komponenta obsahuje element HTML s velkým prvním písmenem, které neodpovídá názvu komponenty, je vygenerováno upozornění označující, že element má neočekávaný název. Přidání příkazu `@using` pro obor názvů součásti zpřístupňuje komponentu, což odstraní upozornění.

## <a name="component-parameters"></a>Parametry součásti

Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí veřejných vlastností třídy komponenty s atributem `[Parameter]`. Použijte atributy k určení argumentů pro komponentu v kódu.

*Components/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=11-12)]

V následujícím příkladu z ukázkové aplikace `ParentComponent` nastaví hodnotu vlastnosti `Title` `ChildComponent`.

*Stránky/ParentComponent. Razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="child-content"></a>Podřízený obsah

Komponenty mohou nastavit obsah jiné součásti. Součást přiřazení poskytuje obsah mezi značkami, které určují přijímací komponentu.

V následujícím příkladu má `ChildComponent` vlastnost `ChildContent`, která představuje `RenderFragment`, která představuje segment uživatelského rozhraní pro vykreslení. Hodnota `ChildContent` je umístěna v označení komponenty, kde má být obsah vykreslen. Hodnota `ChildContent` je přijímána z nadřazené komponenty a vykreslena v `panel-body`panelu Bootstrap.

*Components/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Vlastnost, která přijímá obsah `RenderFragment`, musí mít název `ChildContent` podle konvence.

`ParentComponent` v ukázkové aplikaci může poskytovat obsah pro vykreslování `ChildComponent` umístěním obsahu do značek `<ChildComponent>`.

*Stránky/ParentComponent. Razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Seskupováním atributů a libovolné parametry

Komponenty mohou kromě deklarovaných parametrů komponenty zachytit a vykreslovat další atributy. Další atributy mohou být zachyceny ve slovníku a poté *splatted* na prvek při vykreslení komponenty pomocí direktivy [`@attributes`](xref:mvc/views/razor#attributes) Razor. Tento scénář je užitečný při definování komponenty, která vytváří prvek značky, který podporuje nejrůznější přizpůsobení. Například může být zdlouhavé definovat atributy samostatně pro `<input>`, které podporují mnoho parametrů.

V následujícím příkladu první `<input>` element (`id="useIndividualParams"`) používá jednotlivé parametry komponenty, zatímco druhý `<input>` element (`id="useAttributesDict"`) používá atribut seskupováním:

```razor
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

Vykreslené `<input>` prvky pomocí obou přístupů jsou identické:

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

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

Vlastnost `CaptureUnmatchedValues` v `[Parameter]` umožňuje, aby parametr odpovídal všem atributům, které se neshodují s žádným jiným parametrem. Komponenta může definovat pouze jeden parametr s `CaptureUnmatchedValues`. Typ vlastnosti používaný u `CaptureUnmatchedValues` musí být možné přiřadit z `Dictionary<string, object>` pomocí řetězcových klíčů. v tomto scénáři jsou také možnosti `IEnumerable<KeyValuePair<string, object>>` nebo `IReadOnlyDictionary<string, object>`.

Pozice `@attributes` relativní k pozici atributů elementu je důležitá. Když `@attributes` jsou splattedy na elementu, atributy jsou zpracovávány zprava doleva (poslední až první). Vezměte v úvahu následující příklad komponenty, která využívá `Child` komponentu:

*ParentComponent. Razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent. Razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Atribut `extra` `Child` komponenty je nastaven na hodnotu napravo od `@attributes`. `<div>` vykreslená komponenta `Parent` obsahuje `extra="5"` při předání prostřednictvím dodatečného atributu, protože atributy jsou zpracovávány zprava doleva (poslední až první):

```html
<div extra="5" />
```

V následujícím příkladu je pořadí `extra` a `@attributes` v `<div>``Child` komponenty obrácené:

*ParentComponent. Razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent. Razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Vykreslený `<div>` v komponentě `Parent` obsahuje `extra="10"` při předání prostřednictvím dalšího atributu:

```html
<div extra="10" />
```

## <a name="data-binding"></a>Datová vazba

Datové vazby na součásti a prvky modelu DOM jsou provedeny atributem [`@bind`](xref:mvc/views/razor#bind) . Následující příklad váže vlastnost `CurrentValue` k hodnotě v textovém poli:

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

Kromě zpracování `onchange`ch událostí pomocí syntaxe `@bind` lze vlastnost nebo pole svázat pomocí jiných událostí zadáním atributu [`@bind-value`](xref:mvc/views/razor#bind) s parametrem `event` ([`@bind-value:event`](xref:mvc/views/razor#bind)). Následující příklad váže vlastnost `CurrentValue` pro událost `oninput`:

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Na rozdíl od `onchange`, která je aktivována, když prvek ztratí fokus, `oninput` při změně hodnoty textového pole aktivována.

**Hodnoty, které nelze analyzovat**

Když uživatel poskytne neanalyzovatelné hodnoty prvku DataBound, hodnota neanalyzovat se automaticky vrátí na předchozí hodnotu, když se aktivuje událost BIND.

Uvažte následující příklady:

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

Ve výchozím nastavení se vazba vztahuje na událost `onchange` elementu (`@bind="{PROPERTY OR FIELD}"`). K nastavení jiné události použijte `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}`. Pro událost `oninput` (`@bind-value:event="oninput"`) dojde k reverzi po stisknutí klávesy, která zavádí neanalyzovatelné hodnoty. Při cílení na událost `oninput` s typem svázaným `int`je uživateli zabráněno v psaní `.`ho znaku. Znak `.` je okamžitě odebrán, takže uživatel obdrží okamžitou zpětnou vazbu, že jsou povolena pouze celá čísla. Existují situace, kdy se hodnota `oninput` události nedoporučuje, například pokud by měl být uživatel povolen k vymazání neanalyzovatelné `<input>` hodnoty. K alternativám patří:

* Nepoužívejte událost `oninput`. Použijte výchozí `onchange` událost (`@bind="{PROPERTY OR FIELD}"`), kde neplatnou hodnotu vrátí, dokud prvek neztratí fokus.
* Vytvořte propojení s typem s možnou hodnotou null, například `int?` nebo `string`, a poskytněte vlastní logiku pro zpracování neplatných položek.
* Použijte [komponentu ověřování formuláře](xref:blazor/forms-validation), například `InputNumber` nebo `InputDate`. Komponenty ověřování formuláře mají integrovanou podporu pro správu neplatných vstupů. Součásti pro ověření formuláře:
  * Povolí uživateli zadání neplatných vstupů a přijetí chyb ověřování na přidruženém `EditContext`.
  * Zobrazí chyby ověřování v uživatelském rozhraní, aniž by došlo ke konfliktu s uživatelem, který zadává další data z formuláře.

**Globalizace**

hodnoty `@bind` jsou formátovány pro zobrazení a analýzu pomocí pravidel aktuální jazykové verze.

K aktuální jazykové verzi je možné přistupovat z vlastnosti <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.

[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) se používá pro následující typy polí (`<input type="{TYPE}" />`):

* `date`
* `number`

Předchozí typy polí:

* Se zobrazují pomocí příslušných pravidel formátování založených na prohlížeči.
* Text s volným formulářem nemůže obsahovat.
* Poskytněte charakteristiky interakce s uživatelem na základě implementace prohlížeče.

Následující typy polí mají specifické požadavky na formátování a aktuálně nejsou podporovány Blazor, protože nejsou podporovány ve všech hlavních prohlížečích:

* `datetime-local`
* `month`
* `week`

`@bind` podporuje parametr `@bind:culture` k poskytnutí <xref:System.Globalization.CultureInfo?displayProperty=fullName> pro analýzu a formátování hodnoty. Při použití typů polí `date` a `number` se nedoporučuje zadat jazykovou verzi. `date` a `number` mají integrovanou Blazor podporu, která poskytuje požadovanou jazykovou verzi.

Informace o tom, jak nastavit jazykovou verzi uživatele, najdete v části [lokalizace](#localization) .

**Řetězce formátu**

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

**Parametry součásti**

Vazba rozpoznává parametry komponenty, kde `@bind-{property}` mohou svázat hodnotu vlastnosti napříč komponentami.

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

`EventCallback<T>` je vysvětleno v části [vnořenou eventCallback](#eventcallback) .

Následující nadřazená komponenta používá `ChildComponent` a váže `ParentYear` parametr z nadřazené položky k parametru `Year` pro podřízenou komponentu:

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

Obecně platí, že vlastnost může být svázána s odpovídající obslužnou rutinou události pomocí atributu `@bind-property:event`. Například vlastnost `MyProp` může být svázána s `MyEventHandler` pomocí následujících dvou atributů:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a>Zpracování událostí

Komponenty Razor poskytují funkce pro zpracování událostí. Pro atribut elementu HTML s názvem `on{EVENT}` (například `onclick` a `onsubmit`) s hodnotou delegovanou typem, komponenty Razor považují hodnotu atributu za obslužnou rutinu události. Název atributu je vždy formátován [`@on{EVENT}`](xref:mvc/views/razor#onevent).

Následující kód volá metodu `UpdateHeading`, pokud je tlačítko vybráno v uživatelském rozhraní:

```razor
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

Následující kód volá metodu `CheckChanged`, když je zaškrtávací políčko v uživatelském rozhraní změněno:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Obslužné rutiny událostí mohou být také asynchronní a vracet <xref:System.Threading.Tasks.Task>. Není nutné ručně volat [StateHasChanged](xref:blazor/lifecycle#state-changes). Výjimky jsou protokolovány, když k nim dojde.

V následujícím příkladu je `UpdateHeading` volána asynchronně, když je vybráno tlačítko:

```razor
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

| Událost            | Třída                | Události a poznámky modelu DOM |
| ---------------- | -------------------- | -------------------- |
| Schránka        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Přetažení             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer` a `DataTransferItem` uchování přetažených dat položky. |
| Chyba            | `ErrorEventArgs`     | `onerror` |
| Událost            | `EventArgs`          | *Obecné*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Schránka*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Vstup*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Média*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Vybrána            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Neobsahuje podporu pro `relatedTarget`. |
| Vstup            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Klávesnice         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Myš            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Ukazatele myši    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Kolečko myši      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| Průběh         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Dotykové ovládání            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint` představuje jeden kontaktní bod na zařízení citlivém na dotykové ovládání. |

Informace o vlastnostech a chování zpracování událostí událostí v předchozí tabulce naleznete v tématu [třídy EventArgs ve zdroji referencí (ASPNET/AspNetCore Release/3.0)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).

### <a name="lambda-expressions"></a>Výrazy lambda

Lambda výrazy lze také použít:

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Je často vhodné uzavřít další hodnoty, jako například při iteraci přes sadu prvků. Následující příklad vytvoří tři tlačítka, z nichž každá volá `UpdateHeading` předání argumentu události (`MouseEventArgs`) a jeho číslo tlačítka (`buttonNumber`), pokud je vybráno v uživatelském rozhraní:

```razor
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
> Nepoužívejte **proměnnou** smyčky (`i`) ve `for` smyčce přímo ve výrazu lambda. V opačném případě se stejná proměnná používá u všech výrazů lambda, které způsobují, že hodnota `i`být stejná ve všech výrazech lambda. Vždycky zachytit svou hodnotu v místní proměnné (`buttonNumber` v předchozím příkladu) a pak ji použít.

### <a name="eventcallback"></a>Vnořenou eventCallback

Běžný scénář s vnořenými komponentami je přáním spustit metodu nadřazené komponenty, když dojde k události podřízené součásti&mdash;například při výskytu události `onclick` v podřízeném objektu. Chcete-li zobrazit události napříč komponentami, použijte `EventCallback`. Nadřazená komponenta může přiřadit metodu zpětného volání podřízené `EventCallback`komponenty.

`ChildComponent` v ukázkové aplikaci (*Components/ChildComponent. Razor*) ukazuje, jak je nastavená obslužná rutina `onclick` tlačítka pro příjem `EventCallback`ho delegáta z `ParentComponent`ukázky. `EventCallback` se zadává pomocí `MouseEventArgs`, která je vhodná pro událost `onclick` z periferního zařízení:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent` nastaví `EventCallback<T>` podřízeného objektu na jeho metodu `ShowMessage`.

*Stránky/ParentComponent. Razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

Když je vybráno tlačítko v `ChildComponent`:

* Je volána metoda `ShowMessage` `ParentComponent`. `messageText` se aktualizuje a zobrazí v `ParentComponent`.
* Volání [StateHasChanged](xref:blazor/lifecycle#state-changes) není vyžadováno v metodě zpětného volání (`ShowMessage`). `StateHasChanged` se zavolá automaticky, aby se `ParentComponent`znovu vykreslila, stejně jako podřízené události spouštějí revykreslování komponenty v obslužných rutinách události, které se spouštějí v rámci podřízeného objektu

`EventCallback` a `EventCallback<T>` povolují asynchronní delegáty. `EventCallback<T>` je silného typu a vyžaduje konkrétní typ argumentu. `EventCallback` je slabě typované a umožňuje jakýkoli typ argumentu.

```razor
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

Preferovat `EventCallback<T>` silného typu přes `EventCallback`. `EventCallback<T>` poskytuje uživatelům součásti lepší zpětnou vazbu k chybám. Podobně jako u jiných obslužných rutin událostí uživatelského rozhraní je zadání parametru události volitelné. Použijte `EventCallback`, pokud není předána žádná hodnota zpětnému volání.

::: moniker range=">= aspnetcore-3.1"

### <a name="prevent-default-actions"></a>Zabránit výchozím akcím

Chcete-li zabránit výchozí akci pro událost, použijte atribut direktiva [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) .

Když je vybraný klíč na vstupním zařízení a fokus prvku je v textovém poli, prohlížeč normálně zobrazuje znak klíče v textovém poli. V následujícím příkladu je znemožněno výchozí chování zadáním atributu direktiva `@onkeypress:preventDefault`. Čítač zvýší a **+** klíč není zachycen do hodnoty `<input>` elementu:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

Zadání atributu `@on{EVENT}:preventDefault` bez hodnoty je ekvivalentem `@on{EVENT}:preventDefault="true"`.

Hodnotou atributu může být také výraz. V následujícím příkladu je `_shouldPreventDefault` `bool` pole nastaveno na hodnotu `true` nebo `false`:

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

Obslužná rutina události není nutná, aby se zabránilo výchozí akci. Obslužná rutina události a zabraňuje použití výchozích akcí, lze použít nezávisle.

### <a name="stop-event-propagation"></a>Zastavit šíření událostí

Pro zastavení šíření události použijte atribut direktiva [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) .

V následujícím příkladu zaškrtnutí políčka zabrání kliknutí na události z druhého podřízeného `<div>` z rozšiřování do nadřazené `<div>`:

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

::: moniker-end

## <a name="chained-bind"></a>Zřetězená vazba

Běžným scénářem je zřetězení parametru vázaného na data na element stránky ve výstupu součásti. Tento scénář se nazývá *zřetězená vazba* , protože k více úrovním vazby dochází současně.

Zřetězenou BIND nelze implementovat s syntaxí `@bind` v elementu stránky. Obslužná rutina události a hodnota se musí zadat samostatně. Nadřazená komponenta však může použít `@bind` syntaxe s parametrem komponenty.

Následující součást `PasswordField` (*PasswordField. Razor*):

* Nastaví hodnotu `<input>` elementu na vlastnost `Password`.
* Zpřístupňuje změny vlastnosti `Password` nadřazené komponentě pomocí [vnořenou eventCallback](#eventcallback).

```razor
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

```razor
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Chcete-li provést kontrolu nebo chyby depeše v předchozím příkladu:

* Vytvořte pole zálohování pro `Password` (`password` v následujícím ukázkovém kódu).
* Proveďte kontroly nebo chyby depeší ve `Password` setter.

Následující příklad poskytuje okamžitou zpětnou vazbu uživateli, pokud se v hodnotě hesla používá mezera:

```razor
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

Odkazy na komponenty poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance, například `Show` nebo `Reset`. Zachytit odkaz na komponentu:

* Přidejte atribut [`@ref`](xref:mvc/views/razor#ref) pro podřízenou komponentu.
* Definujte pole stejného typu jako podřízená komponenta.

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Při vykreslení komponenty je pole `loginDialog` vyplněno instancí `MyLoginDialog` podřízená komponenta. Pak můžete vyvolat metody .NET v instanci komponenty.

> [!IMPORTANT]
> Proměnná `loginDialog` je naplněna pouze po vykreslení komponenty a její výstup obsahuje prvek `MyLoginDialog`. Do tohoto okamžiku neexistuje žádný odkaz na. Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování komponenty, použijte [metody OnAfterRenderAsync nebo OnAfterRender](xref:blazor/lifecycle#after-component-render).

Při zachytávání odkazů na součásti použijte podobnou syntaxi pro [zachycení odkazů na prvky](xref:blazor/javascript-interop#capture-references-to-elements), není to funkce [interoperability JavaScriptu](xref:blazor/javascript-interop) . Odkazy na součásti nejsou předány kódu jazyka JavaScript&mdash;jsou používány pouze v kódu .NET.

> [!NOTE]
> Nepoužívejte odkazy na součásti **pro použití stavu** podřízených komponent. Místo toho použijte k předání dat podřízeným komponentám běžné deklarativní parametry. Použití běžných deklarativních parametrů má za následek podřízené komponenty, které jsou automaticky revykreslovány ve správný čas.

## <a name="invoke-component-methods-externally-to-update-state"></a>Vyvolat metody komponenty externě na stav aktualizace

Blazor používá `SynchronizationContext` k vykonání jediného logického vlákna provádění. [Metody životního cyklu](xref:blazor/lifecycle) komponenty a všechna zpětná volání událostí, která jsou aktivována nástrojem Blazor, jsou spouštěna v tomto `SynchronizationContext`. V případě, že komponenta musí být aktualizována na základě externí události, jako je například časovač nebo jiné oznámení, použijte metodu `InvokeAsync`, která se odešle do `SynchronizationContext`Blazor.

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

```razor
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

V předchozím příkladu `NotifierService` vyvolá metodu `OnNotify` komponenty mimo `SynchronizationContext`Blazor. `InvokeAsync` slouží k přepnutí do správného kontextu a vykreslení vykreslování do fronty.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Použití \@Key k řízení uchovávání prvků a komponent

Při vykreslování seznamu prvků nebo komponent a následné změny prvků nebo komponent musí být Blazorrozdílový algoritmus rozhodnout, které z předchozích prvků nebo komponent lze zachovat a jak se mají objekty modelu namapovat. Obvykle je tento proces automatický a může být ignorován, ale existují případy, kdy můžete chtít řídit proces.

Vezměte v úvahu v následujícím příkladu:

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

Obsah kolekce `People` se může změnit vloženými, odstraněnými nebo znovu seřazenými položkami. Při revykreslování komponenty se může komponenta `<DetailsEditor>` změnit, aby přijímala jiné hodnoty parametrů `Details`. To může způsobit složitější revykreslování, než se očekávalo. V některých případech může reprodukce vést k viditelným rozdílům v chování, jako je například ztracený fokus elementu.

Proces mapování lze řídit pomocí atributu direktivy `@key`. `@key` způsobí, že rozdílový algoritmus zaručuje uchovávání prvků nebo komponent na základě hodnoty klíče:

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

Když dojde ke změně kolekce `People`, rozdílový algoritmus zachovává přidružení mezi instancemi `<DetailsEditor>` a instancemi `person`:

* Pokud se ze seznamu `People` odstraní `Person`, z uživatelského rozhraní se odebere jenom odpovídající instance `<DetailsEditor>`. Ostatní instance zůstanou beze změny.
* Pokud je na některém místě v seznamu vložená `Person`, do příslušné pozice se vloží jedna nová instance `<DetailsEditor>`. Ostatní instance zůstanou beze změny.
* Pokud jsou položky `Person` znovu seřazeny, odpovídající instance `<DetailsEditor>` jsou zachovány a v uživatelském rozhraní znovu seřazeny.

V některých scénářích použití `@key` minimalizuje složitost reprodukce a vyhnete se potenciálním problémům se měnícími se stavovou částí modelu DOM, jako je například pozice fokusu.

> [!IMPORTANT]
> Klíče jsou místní pro každý prvek kontejneru nebo komponentu. Klíče nejsou v dokumentu globálně porovnány.

### <a name="when-to-use-key"></a>Kdy použít klíč \@

Obvykle má smysl použít `@key` vždy, když je vygenerován seznam (například v bloku `@foreach`), a existuje vhodná hodnota pro definování `@key`.

Můžete také použít `@key` k zabránění Blazor při zachování prvku nebo podstromu komponenty při změně objektu:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Pokud `@currentPerson` změny, direktiva `@key` atributu vynutí Blazor zahodit celý `<div>` a jeho následníky a znovu sestavit podstrom v uživatelském rozhraní s novými prvky a komponentami. To může být užitečné, pokud potřebujete zaručit, že při `@currentPerson` změny se nezachová žádný stav uživatelského rozhraní.

### <a name="when-not-to-use-key"></a>Kdy se \@klíč nepoužívá

Při rozdílech s `@key`se účtují náklady na výkon. Náklady na výkon nejsou velké, ale zadávejte `@key` jenom v případě, že řízení pravidel uchovávání prvků nebo součástí má aplikace výhodu.

I v případě, že `@key` nepoužívá, Blazor zachová podřízené prvky a instance komponent co nejvíce. Jedinou výhodou použití `@key` je řídit, *jak* jsou instance modelů mapovány na zachované instance komponent namísto rozdílového algoritmu výběru mapování.

### <a name="what-values-to-use-for-key"></a>Jaké hodnoty se mají použít pro klíč \@

Obecně je vhodné dodat jeden z následujících typů hodnoty pro `@key`:

* Instance objektů modelu (například instance `Person` jako v předchozím příkladu). To zajišťuje zachování v závislosti na rovnosti odkazů na objekty.
* Jedinečné identifikátory (například hodnoty primárního klíče typu `int`, `string`nebo `Guid`).

Zajistěte, aby hodnoty používané pro `@key` nekolidovat. Pokud jsou v rámci stejného nadřazeného prvku zjištěny hodnoty konfliktu, Blazor vyvolá výjimku, protože nemůže deterministickém mapovat staré prvky nebo komponenty na nové prvky nebo komponenty. Používejte pouze jedinečné hodnoty, například instance objektů nebo hodnoty primárního klíče.

## <a name="routing"></a>Směrování

Směrování v Blazor dosáhnete tak, že v aplikaci poskytnete šablonu směrování pro každou dostupnou součást.

Když je zkompilován soubor Razor s direktivou `@page`, vygenerovaná třída má <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> určující šablonu trasy. V době běhu směrovač vyhledá třídy komponent pomocí `RouteAttribute` a vykreslí, že každá komponenta má šablonu směrování, která odpovídá požadované adrese URL.

Pro komponentu lze použít více šablon směrování. Následující komponenta reaguje na požadavky na `/BlazorRoute` a `/DifferentBlazorRoute`.

*Stránky/BlazorRoute. Razor*:

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

## <a name="route-parameters"></a>Parametry směrování

Komponenty mohou přijímat parametry směrování z šablony směrování uvedené v direktivě `@page`. Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponent.

*Stránky/RouteParameter. Razor*:

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

Volitelné parametry nejsou podporované, takže se v předchozím příkladu použijí dvě direktivy `@page`. První umožňuje navigaci na součást bez parametru. Druhá direktiva `@page` přebírá parametr trasy `{text}` a přiřazuje hodnotu vlastnosti `Text`.

*Catch-All –* syntaxe parametru (`*`/`**`), která zachycuje cestu mezi více hranicemi složek, **není v** součástech Razor ( *. Razor*) podporována.

::: moniker range=">= aspnetcore-3.1"

## <a name="partial-class-support"></a>Podpora částečné třídy

Komponenty Razor jsou generovány jako částečné třídy. Komponenty Razor jsou vytvořeny některým z následujících přístupů:

* C#kód je definován v [`@code`](xref:mvc/views/razor#code) bloku pomocí značek HTML a kódu Razor v jednom souboru. šablony Blazor definují své komponenty Razor pomocí tohoto přístupu.
* C#kód je umístěn v souboru kódu na pozadí, který je definován jako částečná třída.

Následující příklad ukazuje výchozí komponentu `Counter` s blokem `@code` v aplikaci vygenerovanou šablonou Blazor. Značky HTML, kód Razor a C# kód jsou ve stejném souboru:

*Čítač. Razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

Komponentu `Counter` lze také vytvořit pomocí souboru kódu na pozadí s částečnou třídou:

*Čítač. Razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.Razor.cs*:

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

## <a name="specify-a-component-base-class"></a>Zadat základní třídu součásti

Direktivu `@inherits` lze použít k určení základní třídy pro komponentu.

[Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje, jak komponenta může dědit základní třídu, `BlazorRocksBase`, aby poskytovala vlastnosti a metody komponenty.

*Stránky/BlazorRocks. Razor*:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

*BlazorRocksBase.cs*:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

Základní třída by měla být odvozena od `ComponentBase`.

::: moniker-end

## <a name="import-components"></a>Importovat součásti

Obor názvů komponenty, která je vytvořená pomocí Razor, je založen na (v pořadí podle priority):

* [`@namespace`](xref:mvc/views/razor#namespace) označení v souboru Razor ( *. Razor*) značky (`@namespace BlazorSample.MyNamespace`).
* `RootNamespace` projektu v souboru projektu (`<RootNamespace>BlazorSample</RootNamespace>`).
* Název projektu, pořízený z názvu souboru projektu ( *. csproj*) a cesta z kořenového adresáře projektu ke komponentě. Například rozhraní řeší *{Project root}/pages/index.Razor* (*BlazorSample. csproj*) na obor názvů `BlazorSample.Pages`. Komponenty následují C# pravidla vazeb názvů. Pro komponentu `Index` v tomto příkladu komponenty v oboru jsou všechny komponenty:
  * Ve stejné složce *stránky*.
  * Komponenty v kořenu projektu, které explicitně neurčují jiný obor názvů.

Komponenty definované v jiném oboru názvů se přenesou do rozsahu pomocí direktivy [`@using`](xref:mvc/views/razor#using) Razor.

Pokud v *BlazorSample/Shared/* Folder existuje jiná komponenta, `NavMenu.razor`, lze komponentu použít v `Index.razor` pomocí následujícího příkazu `@using`:

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Na součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, které nevyžadují direktivu [`@using`](xref:mvc/views/razor#using) :

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> Kvalifikace `global::` není podporována.
>
> Import komponent s aliasy `using` příkazy (například `@using Foo = Bar`) není podporován.
>
> Částečně kvalifikované názvy nejsou podporovány. Například přidání `@using BlazorSample` a odkazování `NavMenu.razor` pomocí `<Shared.NavMenu></Shared.NavMenu>` není podporováno.

## <a name="conditional-html-element-attributes"></a>Podmíněné atributy elementu HTML

Atributy elementu HTML jsou podmíněně vykresleny na základě hodnoty .NET. Pokud je hodnota `false` nebo `null`, není atribut vykreslen. Pokud je hodnota `true`, vykreslí se atribut jako minimalizovaný.

V následujícím příkladu `IsCompleted` určuje, zda je `checked` vykreslen v kódu elementu:

```razor
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
> Některé atributy HTML, jako je například [Standard ARIA](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), nebudou fungovat správně, pokud je typ .NET `bool`. V těchto případech použijte místo `bool``string` typ.

## <a name="raw-html"></a>Nezpracovaný kód HTML

Řetězce jsou obvykle vykreslovány pomocí textových uzlů modelu DOM, což znamená, že všechny značky, které mohou obsahovat, se ignorují a považují se za text literálu. Chcete-li vykreslit nezpracovaný kód HTML, zabalte obsah HTML do `MarkupString` hodnoty. Hodnota je analyzována jako HTML nebo SVG a vložena do modelu DOM.

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

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Při použití komponenty se šablonou lze parametry šablony zadat pomocí podřízených prvků, které odpovídají názvům parametrů (`TableHeader` a `RowTemplate` v následujícím příkladu):

```razor
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

Argumenty součásti typu `RenderFragment<T>` předány jako elementy mají implicitní parametr s názvem `context` (například z předchozí ukázky kódu `@context.PetId`), ale můžete změnit název parametru pomocí atributu `Context` u podřízeného elementu. V následujícím příkladu atribut `Context` elementu `RowTemplate` určuje parametr `pet`:

```razor
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

Alternativně lze zadat atribut `Context` prvku komponenty. Zadaný atribut `Context` se vztahuje na všechny zadané parametry šablony. To může být užitečné, pokud chcete zadat název parametru obsahu pro implicitní podřízený obsah (bez nutnosti zalamování podřízeného elementu). V následujícím příkladu se atribut `Context` zobrazí u elementu `TableTemplate` a vztahuje se na všechny parametry šablony:

```razor
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

Komponenty se šablonami jsou často typu obecně typované. Například obecná `ListViewTemplate` komponenta může být použita pro vykreslení `IEnumerable<T>`ch hodnot. Chcete-li definovat obecné komponenty, použijte direktivu [`@typeparam`](xref:mvc/views/razor#typeparam) pro určení parametrů typu:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Pokud používáte komponenty s obecným typem, je parametr typu odvozený, pokud je to možné:

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

V opačném případě musí být parametr typu explicitně zadán pomocí atributu, který odpovídá názvu parametru typu. V následujícím příkladu `TItem="Pet"` určuje typ:

```razor
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

Komponenta předchůdce může poskytnout kaskádovou hodnotu pomocí komponenty kaskádová hodnota. Komponenta `CascadingValue` zabalí podstrom hierarchie komponent a poskytne jednu hodnotu všem součástem v rámci daného podstromu.

Například ukázková aplikace určuje informace o motivu (`ThemeInfo`) v jednom z rozložení aplikace jako kaskádový parametr pro všechny komponenty, které tvoří text rozložení `@Body` vlastnosti. `ButtonClass` je v komponentě rozložení přiřazena hodnota `btn-success`. Jakákoli následná komponenta může tuto vlastnost spotřebovat prostřednictvím `ThemeInfo` kaskádového objektu.

součást `CascadingValuesParametersLayout`:

```razor
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

V ukázkové aplikaci `CascadingValuesParametersTheme` komponenta váže kaskádovou hodnotu `ThemeInfo` na kaskádový parametr. Parametr slouží k nastavení třídy CSS pro jedno z tlačítek zobrazených komponentou.

součást `CascadingValuesParametersTheme`:

```razor
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

Pro kaskádování více hodnot stejného typu v rámci stejného podstromu zadejte jedinečný `Name` řetězec pro každou `CascadingValue` komponentu a odpovídající `CascadingParameter`. V následujícím příkladu se dvě `CascadingValue` komponenty zanášejí do různých instancí `MyCascadingType` podle názvu:

```razor
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

```razor
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

Ukázková aplikace má `ITab` rozhraní, které tyto karty implementují:

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

Komponenta `CascadingValuesParametersTabSet` používá součást `TabSet`, která obsahuje několik `Tab` komponent:

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

Podřízené `Tab` komponenty nejsou explicitně předány jako parametry `TabSet`. Místo toho jsou podřízené součásti `Tab` součástí podřízeného obsahu `TabSet`. `TabSet` však stále potřebuje znát každou součást `Tab`, aby mohla vykreslovat hlavičky a aktivní kartu. Chcete-li povolit tuto koordinaci bez nutnosti dalšího kódu, komponenta `TabSet` *může poskytnout sebe sama jako kaskádovou hodnotu* , která je poté odebrána pomocí podřízených `Tab` komponent.

součást `TabSet`:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Podřízené `Tab` komponenty zachytí obsahující `TabSet` jako kaskádový parametr, takže součásti `Tab` přidají sebe do `TabSet` a koordinují, na které kartě je aktivní.

součást `Tab`:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Šablony Razor

Fragmenty vykreslování lze definovat pomocí syntaxe šablony Razor. Šablony Razor představují způsob, jak definovat fragment uživatelského rozhraní a předpokládat následující formát:

```razor
@<{HTML tag}>...</{HTML tag}>
```

Následující příklad ukazuje, jak určit hodnoty `RenderFragment` a `RenderFragment<T>` a vykreslit šablony přímo v komponentě. Fragmenty vykreslování mohou být také předány jako argumenty [součástem šablon](#templated-components).

```razor
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

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` poskytuje metody pro práci s komponentami a prvky, včetně sestavování C# komponent ručně v kódu.

> [!NOTE]
> Použití `RenderTreeBuilder` k vytváření komponent je pokročilý scénář. Poškozená komponenta (například značka neuzavřeného označení) může mít za následek nedefinované chování.

Vezměte v úvahu následující součást `PetDetails`, kterou je možné ručně vytvořit do jiné komponenty:

```razor
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

```razor
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

> ! Upozornění Typy v `Microsoft.AspNetCore.Components.RenderTree` umožňují zpracování *výsledků* operací vykreslování. Toto jsou interní podrobnosti o implementaci rozhraní Blazor Framework. Tyto typy by měly být považovány za *nestabilní* a mohou se změnit v budoucích verzích.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Pořadová čísla se vztahují na čísla řádků kódu a nikoli na pořadí provádění.

soubory `.razor` Blazor jsou vždy kompilovány. Tato možnost je potenciálně výhodná pro `.razor`, protože krok kompilace lze použít k vložení informací, které zlepšují výkon aplikace za běhu.

Hlavní příklad těchto vylepšení zahrnuje *pořadová čísla*. Pořadová čísla označují modul runtime, ze kterého výstupy pocházejí, ze kterých se liší a seřazené řádky kódu. Modul runtime používá tyto informace k vygenerování efektivních rozdílů stromu v lineárním čase, což je mnohem rychlejší než obvykle pro obecný rozdílový algoritmus stromu.

Vezměte v úvahu následující soubor součásti Razor ( *. Razor*):

```razor
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

Při prvním spuštění kódu, pokud je `someFlag` `true`, tvůrce obdrží:

| Sequence | Type      | Datové   |
| :------: | --------- | :----: |
| 0        | Textový uzel | První  |
| 1        | Textový uzel | Druhá |

Představte si, že `someFlag` se budou `false`a kód se znovu vykreslí. Tentokrát Tvůrce získá:

| Sequence | Type       | Datové   |
| :------: | ---------- | :----: |
| 1        | Textový uzel  | Druhá |

Pokud modul runtime provede rozdíl, uvidí, že položka v sekvenci `0` byla odebrána, takže generuje následující skript triviálního *úprav*:

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

| Sequence | Type      | Datové   |
| :------: | --------- | :----: |
| 0        | Textový uzel | První  |
| 1        | Textový uzel | Druhá |

Tento výsledek je stejný jako předchozí případ, takže neexistují žádné negativní problémy. `someFlag` je `false` při druhém vykreslování a výstup je:

| Sequence | Type      | Datové   |
| :------: | --------- | ------ |
| 0        | Textový uzel | Druhá |

Tentokrát rozdílový algoritmus uvidí, že došlo ke *dvěma* změnám, a algoritmus generuje následující skript pro úpravy:

* Změňte hodnotu prvního textového uzlu na `Second`.
* Odeberte druhý textový uzel.

Generování pořadových čísel ztratilo všechny užitečné informace o tom, kde `if/else` větve a smyčky byly obsaženy v původním kódu. To vede ke rozdílu **dvakrát, jak dlouho** chcete.

Toto je triviální příklad. Ve složitějších případech se složitými a hluboce vnořenými strukturami, a to hlavně pomocí smyček, náklady na výkon jsou závažnější. Místo toho, aby se ihned identifikovaly, které bloky nebo větve smyček byly vloženy nebo odebrány, je třeba rozdílový algoritmus přepracovat hluboko do stromů vykreslování a obvykle sestavovat mnohem delší úpravu skriptů, protože je nepřehledně neinformován o tom, jak staré a nové struktury vzájemná vazba.

#### <a name="guidance-and-conclusions"></a>Doprovodné materiály a závěry

* Pokud jsou automaticky generována pořadová čísla, je výkon aplikace zhoršen.
* Rozhraní nemůže automaticky vytvořit vlastní pořadová čísla za běhu, protože potřebné informace neexistují, pokud není zachycena v době kompilace.
* Nepište dlouhé bloky manuálně implementované `RenderTreeBuilder` logiky. Preferovat `.razor` soubory a umožněte kompilátoru, aby se zabývat pořadovým číslem. Nemůžete-li se vyhnout ruční logice `RenderTreeBuilder`, rozdělte dlouhé bloky kódu do menších částí zabalených do `OpenRegion`/`CloseRegion` volání. Každá oblast má vlastní oddělený prostor pořadových čísel, takže v každé oblasti můžete restartovat z nuly (nebo jakéhokoli jiného libovolného čísla).
* Pokud jsou pořadová čísla pevně zakódované, rozdílový algoritmus vyžaduje pouze zvýšení hodnoty pořadových čísel. Počáteční hodnota a mezery jsou nepodstatné. Jednou z oprávněných možností je použít číslo řádku kódu jako pořadové číslo nebo začít od nuly a zvýšit podle hodnoty nebo stovky (případně z upřednostňovaného intervalu). 
* Blazor používá pořadová čísla, zatímco jiné architektury uživatelského rozhraní rozdílového stromu je nepoužívají. Rozdíly jsou mnohem rychlejší, když se používají pořadová čísla, a Blazor má výhodu kompilačního kroku, který se automaticky zabývá pořadovým číslem pro vývojáře vytvářející soubory *. Razor* .

## <a name="localization"></a>Lokalizace

aplikace Blazor serveru jsou lokalizovány pomocí [middlewaru pro lokalizaci](xref:fundamentals/localization#localization-middleware). Middleware vybere vhodnou jazykovou verzi pro uživatele, kteří žádají o prostředky z aplikace.

Tuto jazykovou verzi lze nastavit pomocí jednoho z následujících přístupů:

* [Soubory cookie](#cookies)
* [Poskytnutí uživatelského rozhraní pro výběr jazykové verze](#provide-ui-to-choose-the-culture)

Další informace a příklady naleznete v tématu <xref:fundamentals/localization>.

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a>Konfigurace linkeru pro mezinárodní navýšení (Blazor WebAssembly)

Ve výchozím nastavení Blazorkonfigurace linkeru pro Blazor aplikace pro WebAssembly odříznout informace o mezinárodním prostředí s výjimkou výslovně požadovaných místních hodnot. Další informace a pokyny k řízení chování linkeru naleznete v tématu <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

### <a name="cookies"></a>Cookies

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
1. Relace Blazor serveru začíná správnou jazykovou verzí.

### <a name="provide-ui-to-choose-the-culture"></a>Poskytnutí uživatelského rozhraní pro výběr jazykové verze

K poskytnutí uživatelského rozhraní, které uživateli umožní vybrat jazykovou verzi, se doporučuje *přístup založený na přesměrování* . Proces se podobá tomu, co se stane ve webové aplikaci, když se uživatel pokusí o přístup k zabezpečenému prostředku&mdash;uživatel je přesměrován na přihlašovací stránku a pak se přesměruje zpátky na původní prostředek. 

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
> Použijte `LocalRedirect` výsledek akce, aby se zabránilo otevřeným útokům přes přesměrování. Další informace najdete v tématu <xref:security/preventing-open-redirects>.

Následující komponenta ukazuje příklad, jak provést počáteční přesměrování, když uživatel vybere jazykovou verzi:

```razor
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

### <a name="use-net-localization-scenarios-in-opno-locblazor-apps"></a>Použití scénářů lokalizace rozhraní .NET v aplikacích Blazor

V rámci Blazor aplikací jsou k dispozici následující scénáře lokalizace a globalizace rozhraní .NET:

* . Systém prostředků netto
* Formátování čísla a data specifické pro jazykovou verzi

funkce `@bind` Blazorprovádí globalizaci na základě aktuální jazykové verze uživatele. Další informace najdete v části [vázání dat](#data-binding) .

V současné době se podporuje omezená sada scénářů lokalizace ASP.NET Core:

* `IStringLocalizer<>` *se* v aplikacích Blazor podporuje.
* lokalizace `IHtmlLocalizer<>`, `IViewLocalizer<>`a datových poznámek jsou ASP.NET Core scénáře MVC a **nejsou podporovány** v aplikacích Blazor.

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

Vložené značky SVG se však ve všech scénářích nepodporují. Pokud značku `<svg>` umístíte přímo do souboru komponenty ( *. Razor*), podporuje se základní vykreslování obrázků, ale mnoho pokročilých scénářů se ještě nepodporuje. Například `<use>` značky nejsou aktuálně dodrženy a `@bind` nelze použít s některými značkami SVG. Očekáváme, že tato omezení vyřešíme v budoucí verzi.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:security/blazor/server> &ndash; obsahuje pokyny k vytváření Blazor serverových aplikací, které se musí soupeří s vyčerpáním prostředků.
