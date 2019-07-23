---
title: Vytváření a používání komponent ASP.NET Core Razor
author: guardrex
description: Zjistěte, jak vytvořit a používat komponenty Razor, včetně jak vázat na data, zpracování událostí a spravovat životní cykly komponenty.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
uid: blazor/components
ms.openlocfilehash: 81100ebb0fdaf877df8c9e696c81b86324f8c0d0
ms.sourcegitcommit: 040aedca220ed24ee1726e6886daf6906f95a028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67892245"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Vytváření a používání komponent ASP.NET Core Razor

Podle [Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([stažení](xref:index#how-to-download-a-sample))

Blazor aplikace se vytvářejí pomocí *komponenty*. Komponenta je samostatná blok uživatelského rozhraní (UI), například stránky, dialogové okno nebo formuláře. Komponenta obsahuje kód HTML a zpracování logiku potřebnou k vložení dat nebo v reakci na události uživatelského rozhraní. Součásti jsou flexibilní a jednoduchý. Mohou být vnořené, znovu použít a sdílet mezi projekty.

## <a name="component-classes"></a>Třídy součásti

Součásti jsou implementovány v [Razor](xref:mvc/views/razor) soubory součástí ( *.razor*) pomocí kombinace C# a značka jazyka HTML. Součástí Blazor formálně označovány jako *Razor komponenty*.

Dají se vytvářet komponenty pomocí *.cshtml* příponu souboru, tak dlouho, dokud soubory jsou označeny jako soubory součástí Razor pomocí `_RazorComponentInclude` vlastnost MSBuild. Například aplikaci, která určuje, že všechny *.cshtml* soubory pod *stránky* složky mají být považována za soubory součástí Razor:

```xml
<_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
```

Uživatelské rozhraní pro součást je definován v jazyce HTML. Dynamické vykreslování logiku (například smyčky, podmíněné příkazy, výrazy) přidána pomocí vložený C# syntaxe volá [Razor](xref:mvc/views/razor). Při kompilaci aplikace, bude značka jazyka HTML a C# logiku vykreslení se převedou na třídu komponenty. Název generované třídy odpovídá názvu souboru.

Členy třídy komponenty jsou definovány v `@code` bloku. V `@code` bloku, stav komponent (vlastnosti, pole) zadán s parametrem metody pro zpracování událostí nebo definování dalších součástí logiky. Více než jeden `@code` blok je povolený.

> [!NOTE]
> V předchozích verzích technologie ASP.NET Core `@functions` bloky byly použity ke stejnému účelu jako `@code` bloky. `@functions` bloky i nadále fungovat, ale doporučujeme používat `@code` směrnice.

Komponenta členy můžete poté použita jako tato součást je vykreslování pomocí logiky C# výrazy, které začínají `@`. Například C# pole se vykreslí vložením prefixu `@` na název pole. Následující příklad vyhodnotí a vykreslí:

* `_headingFontStyle` Hodnota vlastnosti šablon stylů CSS pro `font-style`.
* `_headingText` k obsahu `<h1>` elementu.

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Po se zpočátku zobrazí komponentu obnoví komponenty jeho vykreslení stromu v reakci na události. Blazor poté porovnává větve vykreslení oproti předchozímu a použije všechny změny do prohlížeče Document Object Model (DOM).

Součásti jsou běžné C# třídy a může být umístěna kdekoli v rámci projektu. Součásti, které se obvykle vytvářejí webové stránky se nacházejí v *stránky* složky. Non stránka součásti jsou často umístěny do *Shared* nebo vlastní složky přidán do projektu. Pokud chcete použít vlastní složku, buď přidejte vlastní složky oboru názvů na nadřazenou komponentu nebo na aplikaci *_Imports.razor* souboru. Například následující obor názvů umožňuje součástí *součásti* složky, které jsou k dispozici, když je aplikace kořenový obor názvů `WebApplication`:

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>Integrovat komponenty do aplikace Razor Pages a MVC

Komponenty pomocí stávající aplikace Razor Pages a MVC. Není nutné pro přepsání existujících stránek nebo zobrazení Razor komponent. Při zobrazení stránky nebo zobrazení se komponenty jsou předkreslených ve stejnou dobu.

K vykreslení komponenty z stránku nebo zobrazení, použijte `RenderComponentAsync<TComponent>` metodu helper HTML:

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

Při zobrazení stránky a můžou používat komponenty, neplatí první. Součásti nelze použít zobrazení a stránky konkrétní scénáře, jako je částečná zobrazení a oddíly. Chcete-li použít logiku z částečného zobrazení v komponentě, faktor si logiky částečného zobrazení do komponenty.

Další informace o způsobu vykreslené a komponenty stav součásti je spravováno v Blazor serverové aplikace, najdete v článku <xref:blazor/hosting-models> článku.

## <a name="using-components"></a>Pomocí komponent

Součásti můžete zahrnout další součásti je deklarací pomocí syntaxe elementu HTML. Kód pro použití komponenty vypadá jako značku jazyka HTML, kde název značky je typ součásti.

Následující kód v *Index.razor* vykreslí `HeadingComponent` instance:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

*Components/HeadingComponent.razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

## <a name="component-parameters"></a>Parametry komponenty

Může mít komponenty *parametry komponenty*, které jsou definovány pomocí vlastnosti (obvykle *neveřejné*) na komponentní třída s `[Parameter]` atribut. Atributy můžete zadat argumenty pro komponentu v kódu.

*Components/ChildComponent.razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

V následujícím příkladu `ParentComponent` nastaví hodnotu vlastnosti `Title` vlastnost `ChildComponent`.

*Pages/ParentComponent.razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a>Podřízený obsah

Součásti můžete nastavit obsah jiné součásti. Přiřazení součásti najdete zde obsah mezi značky, které určují přijímající komponenty.

V následujícím příkladu `ChildComponent` má `ChildContent` vlastnost, která představuje `RenderFragment`. Hodnota `ChildContent` je umístěn ve značkách komponenty ve kterém má být vykreslen obsah. Hodnota `ChildContent` přijme od nadřazené komponenty a vykreslit v rámci panelu Bootstrap `panel-body`.

*Components/ChildComponent.razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Vlastnost příjmu `RenderFragment` obsahu musí mít název `ChildContent` konvencí.

Následující `ParentComponent` můžete zadat obsah pro vykreslování `ChildComponent` tak, že je obsah uvnitř `<ChildComponent>` značky.

*Pages/ParentComponent.razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="data-binding"></a>Datová vazba

Vazba dat na komponent a prvky modelu DOM se dosahuje pomocí `@bind` atribut. Následující příklad vytvoří vazbu `_italicsCheck` pole na zaškrtávací políčko zaškrtnuto, stav:

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

Při zaškrtnutí políčka je a zrušte zaškrtnutí, hodnota vlastnosti je aktualizována na `true` a `false`v uvedeném pořadí.

Zaškrtávací políčko se aktualizuje v uživatelském rozhraní, pouze v případě, že součást je vykresleno, ne v reakci na měnící se hodnota vlastnosti. Protože komponenty vykreslování sami po spuštění kódu obslužné rutiny události, aktualizace vlastností se obvykle projeví v uživatelském rozhraní ihned.

Pomocí `@bind` s `CurrentValue` vlastnosti (`<input @bind="CurrentValue" />`) je v podstatě ekvivalentní následujícímu:

```cshtml
<input value="@CurrentValue" 
    @onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

Při vykreslování komponentu `value` elementu input pochází z `CurrentValue` vlastnost. Když uživatel zadá v textovém poli `onchange` událost se aktivuje a `CurrentValue` je nastavena na hodnotu změněné. Ve skutečnosti je generování kódu poněkud složitější, protože `@bind` zpracovává několik případů, kdy jsou provedeny převody typu. V zásadě `@bind` přidruží aktuální hodnotu výrazu s `value` obslužné rutiny a atributu změny pomocí zaregistrovaná obslužná rutina.

Kromě zpracování `onchange` události s `@bind` syntaxi, vlastnosti nebo pole lze navázat tak, že určíte pomocí jiných událostí `@bind-value` atributem `event` parametru. Následující příklad vytvoří vazbu `CurrentValue` vlastnost `oninput` události:

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

Na rozdíl od `onchange`, což je vyvoláno, pokud prvek ztratí fokus, `oninput` je aktivována, když hodnota textovém poli změny.

**Formátovací řetězce**

Vytváření datových vazeb funguje s <xref:System.DateTime> řetězce formátu. V tuto chvíli nejsou k dispozici jiných výrazech formátu, například měny nebo číselných formátů.

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

`@bind:format` Atribut specifikuje formát data použít `value` z `<input>` elementu. Formát slouží také k analýze hodnotu při `onchange` dojde k události.

**Parametry komponenty**

Vazba také rozpozná parametry komponenty, kde `@bind-{property}` mohl vytvořit vazbu vlastnosti komponentami.

Následující podřízené součásti (`ChildComponent`) má `Year` parametr součásti a `YearChanged` zpětného volání:

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    private int Year { get; set; }

    [Parameter]
    private EventCallback<int> YearChanged { get; set; }
}
```

`EventCallback<T>` je podrobně [EventCallback](#eventcallback) oddílu.

Následující nadřazené komponenty používá `ChildComponent` a vytvoří vazbu `ParentYear` parametr z nadřazeného `Year` parametru u podřízené součásti:

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="@ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    private int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

Načítají `ParentComponent` vytváří následující značky:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Pokud hodnota `ParentYear` vlastnost se změní tak, že vyberete tlačítko v `ParentComponent`, `Year` vlastnost `ChildComponent` se aktualizuje. Nová hodnota `Year` se vykreslí v uživatelském rozhraní při `ParentComponent` je rerendered:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

`Year` Parametr je s možností vazby, protože má doprovodná `YearChanged` událost, která odpovídá typu používaného `Year` parametru.

Podle konvence `<ChildComponent @bind-Year="ParentYear" />` je v podstatě ekvivalentní zápisu:

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Obecně platí, vlastnost může být vázána na odpovídající obslužná rutina události pomocí `@bind-property:event` atribut. Například vlastnost `MyProp` může být vázaný na `MyEventHandler` pomocí následující dva atributy:

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a>Zpracování událostí

Součásti syntaxe Razor poskytují funkce zpracování událostí. Atribut HTML elementu s názvem `on<event>` (například `onclick` a `onsubmit`) s hodnotou typu delegáta Razor součásti považuje za hodnotu atributu obslužné rutiny události. Název atributu vždy začíná `@on`.

Následující kód volá `UpdateHeading` metodu po výběru tlačítka v uživatelském rozhraní:

```cshtml
<button class="btn btn-primary" @onclick="@UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

Následující kód volá `CheckChanged` metoda při změně zaškrtávacího políčka v uživatelském rozhraní:

```cshtml
<input type="checkbox" class="form-check-input" @onchange="@CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Obslužné rutiny událostí může být také asynchronní a zpět <xref:System.Threading.Tasks.Task>. Není nutné ručně volat `StateHasChanged()`. Výjimky se protokolují, když k nim dojde.

V následujícím příkladu `UpdateHeading` se asynchronně volá při výběru tlačítka:

```cshtml
<button class="btn btn-primary" @onclick="@UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

Pro některé události nejsou povoleny typy argumentů události specifické pro události. Pokud přístup k jednomu z těchto typů událostí není nezbytné, není potřeba ve volání metody.

Podporované [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/UIEventArgs.cs) jsou uvedeny v následující tabulce.

| Událost | Třída |
| ----- | ----- |
| Schránka | `UIClipboardEventArgs` |
| Přetažení  | `UIDragEventArgs` &ndash; `DataTransfer` slouží k ukládání dat Přetahované během operace přetažení a může obsahovat jeden nebo více `UIDataTransferItem`. `UIDataTransferItem` představuje jeden přetáhněte datovou položku. |
| Chyba | `UIErrorEventArgs` |
| fokus | `UIFocusEventArgs` &ndash; Neobsahuje podporu pro `relatedTarget`. |
| `<input>` Změna | `UIChangeEventArgs` |
| Klávesnice | `UIKeyboardEventArgs` |
| Myši | `UIMouseEventArgs` |
| Ukazatele myši | `UIPointerEventArgs` |
| Kolečko myši | `UIWheelEventArgs` |
| Průběh | `UIProgressEventArgs` |
| Dotykové ovládání | `UITouchEventArgs` &ndash; `UITouchPoint` představuje jediné kontaktní místo na dotyk zařízení. |

Informace o vlastnostech a zpracování událostí v předchozí tabulce chování událostí najdete v tématu [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/UIEventArgs.cs) ve zdroji odkazu.
  
Výrazy lambda lze také použít:

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Často je vhodné zavřít další hodnoty, například během iterace přes sadu elementů. Následující příklad vytvoří tři tlačítka, každý z který volá `UpdateHeading` předání argumentu události (`UIMouseEventArgs`) a tlačítko (`buttonNumber`) při výběru v uživatelském rozhraní:

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

    private void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> Proveďte **není** použít proměnnou smyčky (`i`) v `for` smyčky přímo ve výrazu lambda. V opačném případě se používá stejnou proměnnou všechny výrazy lambda způsobí `i`hodnotu být stejné ve všech výrazů lambda. Zachytit její hodnotu v místní proměnné (`buttonNumber` v předchozím příkladu) a pak přes ni.

### <a name="eventcallback"></a>EventCallback

Je běžným scénářem vnořených součástí chce spouštět nadřazenou metodu komponenty, při výskytu události podřízené součásti&mdash;například, když `onclick` v podřízeném dojde k události. Chcete-li zpřístupnit událostí mezi komponentami, použijte `EventCallback`. Nadřazené komponenty můžete přiřadit metody zpětného volání k podřízené součásti `EventCallback`.

`ChildComponent` v ukázce aplikace předvádí, jak tlačítka `onclick` přijímat je nastavena obslužná rutina `EventCallback` delegovat z tohoto příkladu `ParentComponent`. `EventCallback` Je zadán s `UIMouseEventArgs`, což je vhodné pro `onclick` událost z periferní zařízení:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent` Nastaví dítěte `EventCallback<T>` k jeho `ShowMessage` metody:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

Při výběru tlačítka `ChildComponent`:

* `ParentComponent`Společnosti `ShowMessage` metoda je volána. `messageText` se aktualizují a zobrazí v `ParentComponent`.
* Volání `StateHasChanged` není nutné v metodě zpětného volání (`ShowMessage`). `StateHasChanged` je volána automaticky k rerender `ParentComponent`, stejně jako podřízené události aktivovat součást rerendering v obslužných rutinách událostí, které jsou spuštěny v rámci podřízené.

`EventCallback` a `EventCallback<T>` povolit asynchronních delegátů. `EventCallback<T>` silně typované a vyžaduje konkrétní argument typu. `EventCallback` slabě typované a jakýkoli typ argumentu.

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

Vyvolání `EventCallback` nebo `EventCallback<T>` s `InvokeAsync` a operátoru await <xref:System.Threading.Tasks.Task>:

```csharp
await callback.InvokeAsync(arg);
```

Použití `EventCallback` a `EventCallback<T>` pro událost zpracování a vazby parametrů komponenty.

Preferovat silného typu `EventCallback<T>` přes `EventCallback`. `EventCallback<T>` poskytuje lepší zpětnou vazbu chyba uživatelům komponenty. Podobně jako jiné obslužné rutině události uživatelského rozhraní, zadání parametru události je volitelné. Použití `EventCallback` při neexistuje převáděná hodnota předaný zpětnému volání.

## <a name="capture-references-to-components"></a>Zachycení odkazy na komponenty

Komponenta odkazy poskytují způsob, jak odkazovat na instanci komponenty tak, že můžete použít příkazy do této instance, jako například `Show` nebo `Reset`. Chcete-li zachytit odkazem komponenty, přidejte `@ref` atribut podřízené součásti a pak definovat pole se stejným názvem a stejného typu jako podřízené součásti.

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

Při vykreslování komponentu `loginDialog` pole se vyplní `MyLoginDialog` podřízené instance komponenty. Potom můžete vyvolat metody rozhraní .NET na instanci komponenty.

> [!IMPORTANT]
> `loginDialog` Proměnná je vyplněný pouze poté, co se vykreslí komponentu a zahrnuje její výstup `MyLoginDialog` elementu. Až do bodu není nutné nic odkazovat. K manipulaci s odkazy na součásti po dokončení vykreslení komponentu, použijte `OnAfterRenderAsync` nebo `OnAfterRender` metody.

Při zachytávání odkazů na komponenty pomocí syntaxe podobné [zachytávání odkazy na prvky](xref:blazor/javascript-interop#capture-references-to-elements), není [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:blazor/javascript-interop) funkce. Nejsou součástí odkazy předané do kódu jazyka JavaScript&mdash;se použít jenom v kódu .NET.

> [!NOTE]
> Proveďte **není** mutovat stavu podřízenými komponentami pomocí odkazů na komponenty. Místo toho použijte normální deklarované parametry k předání dat podřízenými komponentami. Použijte normální deklarované parametry výsledku v podřízené součásti, které automaticky rerender ve správném čase.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Použití @key řídit zachování prvky a součásti

Při vykreslování seznamu elementů nebo komponenty prvky a součásti následně změnit, algoritmus rozdílování Blazor společnosti musíte rozhodnout, které z předchozí prvky nebo komponent se můžou ukládat a mapování objektů modelu do nich. Za normálních okolností tento proces probíhá automaticky a můžete ignorovat, ale existují případy, kde můžete chtít řízení procesu.

Vezměte v úvahu v následujícím příkladu:

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    private IEnumerable<Person> People { get; set; }
}
```

Obsah `People` kolekce může změnit s vložit, odstranit nebo měnit pořadí položek. Když rerenders komponentu `<DetailsEditor>` komponenta může změnit na jiný `Details` hodnoty parametrů. To může způsobit složitější rerendering, než se očekávalo. V některých případech rerendering může vést k rozdíly v chování viditelné, například ke ztrátě prvek fokus.

Proces mapování se dá řídit pomocí `@key` atribut direktivy. `@key` způsobí, že algoritmus rozdílování zaručit zachování elementy nebo komponenty založené na hodnotě klíče:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="@person" Details="@person.Details" />
}

@code {
    [Parameter]
    private IEnumerable<Person> People { get; set; }
}
```

Když `People` změny kolekce algoritmus rozdílování uchovává přidružení mezi `<DetailsEditor>` instancí a `person` instancí:

* Pokud `Person` je odstraněn z `People` seznamu pouze odpovídající `<DetailsEditor>` instance je odebrána z uživatelského rozhraní. Jindy jsou ponechány beze změny.
* Pokud `Person` se vloží některé pozici v seznamu, jeden nové `<DetailsEditor>` instance bude vložena na této pozici odpovídající. Jindy jsou ponechány beze změny.
* Pokud `Person` jsou položky měnit pořadí, odpovídající `<DetailsEditor>` instancí jsou zachovány a pořadí v uživatelském rozhraní.

V některých scénářích použití `@key` minimalizuje rerendering složitost a potenciální problémy s stavové části modelu DOM změna například pozici fokus se vyhnete.

> [!IMPORTANT]
> Klíče jsou místní pro každý kontejner prvku nebo komponenty. Klíče nejsou porovnávány globálně v dokumentu.

### <a name="when-to-use-key"></a>Kdy použít @key

Obvykle je vhodné použít `@key` vždy, když je vykreslen seznamu (například v `@foreach` bloku) a k definování existuje vhodnou hodnotu `@key`.

Můžete také použít `@key` zabránit Blazor zachování prvku nebo komponenty podstrom, když se změní objekt:

```cshtml
<div @key="@currentPerson">
    ... content that depends on @currentPerson ...
</div>
```

Pokud `@currentPerson` změny, `@key` atribut direktivy vynutí Blazor zahodíte celý `<div>` a její potomci a znovu sestavte podstrom uživatelského rozhraní pomocí nové prvky a součásti. To může být užitečné, pokud potřebujete zajistit, že žádný stav uživatelského rozhraní se zachová při `@currentPerson` změny.

### <a name="when-not-to-use-key"></a>Kdy nepoužívat @key

Výkon, když je rozdílování s `@key`. Není velké náklady na výkon, ale pouze zadat `@key` Pokud řízení zachování prvku nebo komponenty pravidla využívat aplikace.

I když `@key` není použit, Blazor zachová podřízený element a součást instance co největší míře. Jedinou výhodou používání `@key` je ovládací prvek průběhu *jak* instancí modelu jsou mapovány na instance zachovaných součástí, namísto rozdílování algoritmus výběru mapování.

### <a name="what-values-to-use-for-key"></a>Jaké hodnoty je třeba použít pro @key

Obecně je vhodné jej zadejte hodnoty pro následující typy `@key`:

* Model instance objektů (například `Person` instance jako v předchozím příkladu). Tím se zajistí zachování založené na objektu referenční rovnost.
* Jedinečné identifikátory (například hodnoty primárního klíče typu `int`, `string`, nebo `Guid`).

Vyhněte se zadání hodnoty, které mohou kolidovat neočekávaně. Pokud `@key="@someObject.GetHashCode()"` pochází, může dojít k neočekávaným došlo ke konfliktům protože kódů hash objektů nesouvisejících může být stejný. Pokud se kolidující `@key` hodnoty jsou požadovány v rámci stejné nadřazené `@key` nebude respektovat hodnoty.

## <a name="lifecycle-methods"></a>Životní cyklus metody

`OnInitAsync` a `OnInit` spouštění kódu se inicializovat komponentu. Chcete-li provádění asynchronní operace, použijte `OnInitAsync` a `await` – klíčové slovo v operaci:

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

Synchronní operace, použijte `OnInit`:

```csharp
protected override void OnInit()
{
    ...
}
```

`OnParametersSetAsync` a `OnParametersSet` se volá, když součást přijme parametry ze svého nadřazeného objektu a hodnoty jsou přiřazeny k vlastnostem. Tyto metody jsou provedeny po inicializaci součásti a pokaždé, když je vykreslen komponenty:

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

`OnAfterRenderAsync` a `OnAfterRender` se volá se po vykreslení komponentu. V tomto okamžiku se vyplní elementu a součást odkazy. Pomocí této fázi můžete provést další inicializaci postup pomocí vykreslovaný obsah, jako je aktivace JavaScript knihovny třetích stran, které pracují s vykreslené elementy modelu DOM.

```csharp
protected override async Task OnAfterRenderAsync()
{
    await ...
}
```

```csharp
protected override void OnAfterRender()
{
    ...
}
```

### <a name="handle-incomplete-async-actions-at-render"></a>Zpracování neúplné asynchronní akce v vykreslení

Asynchronní akce prováděné v události životního cyklu pravděpodobně nebyly dokončeny před vykreslením komponentu. Objekty mohou být `null` nebo neúplně naplněný daty při provádění metody životního cyklu. Poskytnout logiku vykreslování potvrďte, že objekty jsou inicializovány. Vykreslení zástupné prvky uživatelského rozhraní (například načítání zpráv) při objekty jsou `null`.

V `FetchData` součást šablony Blazor `OnInitAsync` je potlačena za účelem nekopírovat přijímat data předpovědi (`forecasts`). Když `forecasts` je `null`, uživateli se zobrazí zpráva načtení. Po `Task` vrácený `OnInitAsync` dokončí, komponenta je rerendered s aktualizovaný stav.

*Pages/FetchData.razor*:

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a>Spuštění kódu před parametry jsou nastavené.

`SetParameters` může být potlačena za účelem spouštění kódu předtím, než jsou nastavené parametry:

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

Pokud `base.SetParameters` není vyvolána, můžete vlastní kód interpretaci příchozí hodnoty parametrů v jakékoli požadované stejně, jako. Například příchozí parametry nejsou potřeba přiřadit k vlastnosti ve třídě.

### <a name="suppress-refreshing-of-the-ui"></a>Potlačit aktualizaci uživatelského rozhraní

`ShouldRender` může být potlačena za účelem potlačit aktualizaci uživatelského rozhraní. Implementace vrátí-li `true`, aktualizaci uživatelského rozhraní. I když `ShouldRender` je přepsána, komponenta je vždy Počáteční vykreslení.

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a>Vyřazení komponenty pomocí rozhraní IDisposable

Pokud komponenta implementuje <xref:System.IDisposable>, [metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) se volá, když je součást z uživatelského rozhraní. Používá následující komponenty `@implements IDisposable` a `Dispose` metody:

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

Směrování v Blazor se dosahuje tím, že poskytuje šablona trasy pro jednotlivé dostupné komponenty v aplikaci.

Když soubor Razor s `@page` – direktiva je zkompilován, dostane generované třídy <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadání šablonu trasy. Za běhu, směrovač hledá komponentní třídy s `RouteAttribute` a vykreslí podle toho, která komponenta má šablona trasy, která odpovídá požadovanou adresu URL.

Více šablon trasy můžete použít pro komponentu. Následující komponenty jsou reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a>Parametry trasy

Součásti mohou přijímat parametry trasy z šablonu trasy, které jsou součástí `@page` směrnice. Směrovač používá parametry trasy k naplnění odpovídající komponenta parametry.

*Komponenta parametr trasa*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

Volitelné parametry nejsou podporovány, tedy dvě `@page` direktivy se použijí v předchozím příkladu. První umožňuje přechod na komponenty bez parametrů. Druhá `@page` trvá – direktiva `{text}` parametr trasa a přiřadí hodnotu do proměnné `Text` vlastnost.

## <a name="base-class-inheritance-for-a-code-behind-experience"></a>Základní třída dědičnosti "použití modelu code-behind" prostředí

Soubory součástí kombinovat kód HTML a C# zpracování kódu ve stejném souboru. `@inherits` – Direktiva je možné poskytnout Blazor aplikace, který odděluje komponenty značek z kódu pro zpracování prostředí "použití modelu code-behind".

[Ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje, jak komponenty může dědit základní třídy `BlazorRocksBase`, aby vznikl komponenty vlastnosti a metody.

*Pages/BlazorRocks.razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

*BlazorRocksBase.cs*:

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

Základní třída musí být odvozený z: `ComponentBase`.

## <a name="import-components"></a>Importovat komponenty

Obor názvů součásti zleva doprava Razor podle:

* V projektu `RootNamespace`.
* Cesta z kořenového adresáře projektu do komponenty. Například `ComponentsSample/Pages/Index.razor` je v oboru názvů `ComponentsSample.Pages`. Postupujte podle součásti C# název pravidel vazby. V případě třídy *Index.razor*, všechny součásti ve stejné složce *stránky*a nadřazené složky *ComponentsSample*, jsou v oboru.

Součásti definované v jiný obor názvů může být přenesena do rozsahu pomocí syntaxe Razor pro [ \@pomocí](xref:mvc/views/razor#using) směrnice.

Pokud jiná komponenta, `NavMenu.razor`, existuje ve složce `ComponentsSample/Shared/`, součást je možné v `Index.razor` následujícím `@using` – příkaz:

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, která eliminuje potřebu [ \@pomocí](xref:mvc/views/razor#using) – direktiva:

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> `global::` Kvalifikace se nepodporuje.
>
> Import součásti s aliasem `using` příkazy (například `@using Foo = Bar`) se nepodporuje.
>
> Částečně kvalifikované názvy nejsou podporovány. Například přidáním `@using ComponentsSample` a odkazování na ně `NavMenu.razor` s `<Shared.NavMenu></Shared.NavMenu>` se nepodporuje.

## <a name="razor-support"></a>Podpora Razor

**Direktivy Razor**

V následující tabulce jsou uvedeny direktivy Razor.

| – Direktiva | Popis |
| --------- | ----------- |
| [\@code](xref:mvc/views/razor#section-5) | Přidá C# blok kódu na komponentu. `@code` je alias pro `@functions`. `@code` doporučuje se přes `@functions`. Více než jeden `@code` blok je povolený. |
| [\@Funkce](xref:mvc/views/razor#section-5) | Přidá C# blok kódu na komponentu. Zvolte `@code` přes `@functions` pro C# bloky kódu. |
| `@implements` | Implementuje rozhraní pro třídu vygenerované komponenty. |
| [\@inherits](xref:mvc/views/razor#section-3) | Poskytuje plnou kontrolu nad třídu, která dědí komponentu. |
| [\@Vložení](xref:mvc/views/razor#section-4) | Vkládání ze služby umožňuje [kontejneru služby](xref:fundamentals/dependency-injection). Další informace najdete v tématu [injektáž závislostí do zobrazení](xref:mvc/views/dependency-injection). |
| `@layout` | Určuje komponentu rozložení. Rozložení komponenty umožňují zabránit zdvojení kódu a nekonzistence. |
| [\@Stránka](xref:razor-pages/index#razor-pages) | Určuje, že by měla komponenta zpracování požadavků přímo. `@page` – Direktiva je možné zadat při trasy a volitelné parametry. Na rozdíl od Razor Pages `@page` – direktiva nemusí být první – direktiva v horní části souboru. Další informace najdete v tématu [směrování](xref:blazor/routing). |
| [\@použití](xref:mvc/views/razor#using) | Přidá C# `using` směrnice do třídy vygenerované komponenty. To přináší také všechny součásti, které jsou definované v tomto oboru názvů do oboru. |
| [\@namespace](xref:mvc/views/razor#section-6) | Nastaví obor názvů, třídy vygenerované komponenty. |
| [\@attribute](xref:mvc/views/razor#section-7) | Atribut se přidá do třídy vygenerované komponenty. |

**Podmíněné atributy prvků HTML**

Atributy prvků HTML jsou vykreslovány podmíněně na základě hodnoty .NET. Pokud je hodnota `false` nebo `null`, atribut není vykreslen. Pokud je hodnota `true`, atribut je vykreslen minimalizovaný.

V následujícím příkladu `IsCompleted` Určuje, zda `checked` se vykreslí v elementu značky:

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

Pokud `IsCompleted` je `true`, zaškrtněte políčko se vykreslí jako:

```html
<input type="checkbox" checked />
```

Pokud `IsCompleted` je `false`, zaškrtněte políčko se vykreslí jako:

```html
<input type="checkbox" />
```

**Další informace o syntaxi Razor**

Další informace o syntaxi Razor, najdete v článku [referenční příručka syntaxe Razor](xref:mvc/views/razor).

## <a name="raw-html"></a>Raw HTML

Řetězce jsou obvykle vykreslen pomocí modelu DOM textové uzly, což znamená, že všechny značky, které mohou obsahovat je ignorována a považována jako prostý text. Pokud chcete zobrazit nezpracovaný kód HTML, zabalit obsah HTML v `MarkupString` hodnotu. Hodnota je analyzovat ve formátu HTML nebo SVG a vložit do modelu DOM.

> [!WARNING]
> Vykreslování nezpracovaný kód HTML vytvořený z libovolného nedůvěryhodný zdroj je **bezpečnostní riziko** a mělo by se vyhnout!

Následující příklad ukazuje použití `MarkupString` typu přidáte blok statického obsahu HTML vykresleného výstupu součásti:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a>Bez vizuálního vzhledu součásti

Bez vizuálního vzhledu komponenty jsou součástí, které přijímají jeden nebo více šablon uživatelského rozhraní jako parametry, které lze použít jako součást logiky komponenty vykreslování. Bez vizuálního vzhledu komponenty umožňují vytvářet vyšší úrovně součásti, které jsou více než regulární komponenty opakovaně použitelné. Zahrnují několik příkladů:

* Komponenta tabulky, která umožňuje uživateli zadat šablony pro záhlaví tabulky, řádky a zápatí.
* Seznam součástí, která umožňuje uživateli zadat šablonu pro vykreslování položky v seznamu.

### <a name="template-parameters"></a>Parametry šablony

Bez vizuálního vzhledu součásti je definován tak, že zadáte jeden nebo více parametrů součást typu `RenderFragment` nebo `RenderFragment<T>`. Vykreslení fragment reprezentuje segment, který uživatelského rozhraní, které je vykresleno komponentou. Vykreslení fragment volitelně přebírá parametr, který lze zadat, pokud je vyvolána fragment vykreslení.

`TableTemplate` Komponenty:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

Při použití komponenty bez vizuálního vzhledu, parametry šablony lze pomocí podřízené prvky, které odpovídají názvům parametry (`TableHeader` a `RowTemplate` v následujícím příkladu):

```cshtml
<TableTemplate Items="@pets">
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

Komponenta argumenty typu `RenderFragment<T>` předaný jako elementy mají implicitní parametr s názvem `context` (například z předchozí příklad kódu `@context.PetId`), ale můžete změnit pomocí parametru název `Context` atribut na podřízené element. V následujícím příkladu `RowTemplate` elementu `Context` Určuje atribut `pet` parametr:

```cshtml
<TableTemplate Items="@pets">
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

Alternativně můžete zadat `Context` atribut na prvek součásti. Zadaný `Context` atributu platí pro všechny parametry určené šablony. To může být užitečné, pokud chcete zadat název obsahu parametru pro implicitní podřízený obsah (bez jakékoli zabalení podřízený element). V následujícím příkladu `Context` atributu se zobrazí na `TableTemplate` elementu a platí pro všechny parametry šablony:

```cshtml
<TableTemplate Items="@pets" Context="pet">
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

### <a name="generic-typed-components"></a>Obecné typy komponenty

Bez vizuálního vzhledu součásti jsou často obecně typu. Například obecný `ListViewTemplate` komponenty lze použít k vykreslení `IEnumerable<T>` hodnoty. K definování obecné součásti, použijte `@typeparam` směrnice a určete parametry typu:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

Při použití komponenty obecného typu, parametr typu je odvozený Pokud je to možné:

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

V opačném případě parametr typu musí být explicitně zadán pomocí atributu, který odpovídá názvu parametru typu. V následujícím příkladu `TItem="Pet"` Určuje typ:

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a>Kaskádové hodnoty a parametry

V některých případech je nevhodné toku dat z jako součást nadřazené komponenty potomka pomocí [parametry komponenty](#component-parameters), zvlášť pokud máte několik vrstev komponenty. Kaskádové hodnoty a parametry tento problém vyřešit tím, že poskytuje pohodlný způsob pro komponentu předchůdce k zadání hodnoty pro všechny jeho podřízené součásti. Kaskádové hodnoty a parametry také poskytnout přístup pro součásti pro koordinaci.

### <a name="theme-example"></a>Příklad motiv

V následujícím příkladu z ukázkové aplikace `ThemeInfo` třída určuje informace o motivech tok součástí hierarchií směrem dolů, aby všechna tlačítka v rámci dané části aplikace sdílet stejný styl.

*UIThemeClasses/ThemeInfo.cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Jako součást předchůdce může poskytnout kaskádové hodnotu pomocí komponenty kaskádové hodnotu. `CascadingValue` Komponenty zabalí podstrom součástí hierarchie a poskytuje jednu hodnotu pro všechny komponenty v rámci této podstrom.

Například ukázkové aplikace určuje informace o motivech (`ThemeInfo`) v jednom z rozložení aplikace jako parametr šablony pro všechny součásti, které tvoří rozložení textu `@Body` vlastnost. `ButtonClass` je přiřazena hodnota `btn-success` v komponentě rozložení. Všechny podřízené součásti mohou využívat tuto vlastnost prostřednictvím `ThemeInfo` šablony objektu.

`CascadingValuesParametersLayout` Komponenty:

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="@theme">
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

Chcete-li pomocí kaskádových hodnot, komponenty deklarovat kaskádové parametry s využitím `[CascadingParameter]` atribut nebo na základě hodnoty názvu řetězec:

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

Vazba s hodnotou řetězce názvu platí, pokud máte více kaskádových hodnot stejného typu a pro jejich odlišení v rámci stejné podstrom.

Kaskádové hodnoty se váží k parametrům šablony podle typu.

V ukázkové aplikaci `CascadingValuesParametersTheme` komponenta vytvoří vazbu `ThemeInfo` kaskádové hodnotu pro parametr šablony. Tento parametr se používá nastavení třídy šablony stylů CSS pro jedno z tlačítek zobrazí komponentou.

`CascadingValuesParametersTheme` Komponenty:

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="@IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="@IncrementCount">
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

### <a name="tabset-example"></a>Příklad TabSet

Parametry šablony také povolit součásti spolupracovat napříč hierarchií komponenty. Představte si třeba následující *TabSet* příklad v ukázkové aplikaci.

Ukázková aplikace má `ITab` rozhraní, které karty implementace:

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

`CascadingValuesParametersTabSet` Komponenta používá `TabSet` komponenta, která obsahuje několik `Tab` komponenty:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

Podřízené `Tab` součásti nejsou explicitně předány jako parametry `TabSet`. Místo toho podřízené `Tab` komponenty jsou součástí podřízený obsah `TabSet`. Ale `TabSet` stále je potřeba vědět o každém `Tab` komponentu tak, aby ho může mít za následek záhlaví a na aktivní kartě. Povolit koordinace bez potřeby dalšího kódu, `TabSet` komponenty *samotný můžete zadat jako hodnotu kaskádové* , který pak převezme potomka `Tab` komponenty.

`TabSet` Komponenty:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

Potomka `Tab` součásti capture obsahující `TabSet` jako parametr šablony, proto `Tab` součásti přidat samy o sobě `TabSet` a souřadnice, na které kartě je aktivní.

`Tab` Komponenty:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a>Šablony Razor

Vykreslení fragmenty lze definovat pomocí syntaxe šablon Razor. Šablony Razor představují způsob, jak definovat fragment kódu uživatelského rozhraní a předpokládají následující formát:

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

Následující příklad ukazuje, jak určit `RenderFragment` a `RenderFragment<T>` hodnoty a vykreslit šablon přímo v komponentě. Vykreslení fragmenty lze také předat jako argumenty, které mají [bez vizuálního vzhledu součásti](#templated-components).

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

Vykreslí výstup předcházejícího kódu:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a>Ruční RenderTreeBuilder logiky

`Microsoft.AspNetCore.Components.RenderTree` poskytuje metody pro manipulaci s komponentami a prvky, včetně sestavení součástí ručně v C# kódu.

> [!NOTE]
> Použití `RenderTreeBuilder` vytváření komponent je pokročilý scénář. Poškozená součásti (například značku neuzavřený značek) může způsobit nedefinované chování.

Vezměte v úvahu následující `PetDetails` komponenty, které ručně se dají do jiné součásti:

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    private string PetDetailsQuote { get; set; }
}
```

V následujícím příkladu, smyčky v `CreateComponent` metoda generuje tři `PetDetails` komponenty. Při volání metody `RenderTreeBuilder` metody vytvoření součásti (`OpenComponent` a `AddAttribute`), pořadová čísla jsou čísla řádků zdrojového kódu. Algoritmus rozdíl Blazor spoléhá na pořadová čísla odpovídající odlišné řádky kódu, není odlišné volání volání. Při vytváření komponent pomocí `RenderTreeBuilder` metody, pevně argumenty pořadová čísla. **Použití výpočtu nebo čítače k vygenerování pořadové číslo může vést ke špatnému výkonu.** Další informace najdete v tématu [pořadová čísla se týkají pořadí čísel a provádění není řádků kódu](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) oddílu.

`BuiltContent` Komponenty:

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="@RenderComponent">
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

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Pořadová čísla se týkají pořadí čísel a provádění není řádků kódu

Blazor `.razor` jsou vždycky soubory zkompilovány. Toto je potenciálně skvělé výhody pro `.razor` protože kompilační krok je možné vložit informace, které zvýšit výkon aplikace za běhu.

Klíče příklad tato vylepšení zahrnují *pořadí čísla*. Pořadová čísla oznámení modulu runtime, který výstupy pochází z různých a seřazené řádky kódu, které. Modul runtime používá tyto informace ke generování efektivní stromu rozdíly v lineárním čase, což je mnohem rychleji, než je obvykle možné pro algoritmus diff obecné stromu.

Vezměte v úvahu následující jednoduchý `.razor` souboru:

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

Předchozí kód se zkompiluje pro přibližně takto:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Když kód spustí poprvé, pokud `someFlag` je `true`, obdrží Tvůrce:

| Sequence | type      | Data   |
| :------: | --------- | :----: |
| 0        | Textový uzel | první  |
| 1        | Textový uzel | Sekunda |

Představte si, že `someFlag` stane `false`, a je znovu vykreslí značku. Tentokrát, obdrží Tvůrce:

| Sequence | type       | Data   |
| :------: | ---------- | :----: |
| 1        | Textový uzel  | Sekunda |

Když modul runtime provádí rozdílu, uvidí, která položka na pořadí `0` byla odebrána, takže generuje následující triviální *upravit skript*:

* Odeberte první textový uzel.

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a>Co dojde k chybě, když vygenerujete pořadová čísla prostřednictvím kódu programu

Představte si, že jste naprogramovali proto, že následující vykreslení stromu Tvůrce logiky:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Nyní první výstup je následující:

| Sequence | type      | Data   |
| :------: | --------- | :----: |
| 0        | Textový uzel | první  |
| 1        | Textový uzel | Sekunda |

Tento výsledek je stejný jako předchozí případ, takže neexistují žádné negativní problémy. `someFlag` je `false` v druhém vykreslování a výstup je:

| Sequence | type      | Data   |
| :------: | --------- | ------ |
| 0        | Textový uzel | Sekunda |

Tentokrát vidí diff algoritmus, který *dvě* změny došlo, a algoritmus generuje následující skript upravit:

* Změňte hodnotu na prvním uzlu text do `Second`.
* Druhý textový uzel odeberte.

Generování pořadová čísla došlo ke ztrátě všech užitečné informace o tom, kde `if/else` větve a smyčky, se vyskytoval v původní kód. Výsledkem je rozdíl **dvakrát tak dlouho,** stejně jako předtím.

Toto je příklad jednoduchého dotazu. Ve víc odpovídají realitě případů s komplexní a hluboce vnořené struktury a zejména s smyčky je závažnější snížení výkonu. Místo okamžitě identifikaci, které bloky smyčky nebo větve bylo vloženo nebo odebrat, rozdíl algoritmus využívající dlaždice má recurse hluboko do stromové struktury vykreslování a obvykle mnohem delší dobu upravit skripty sestavení, protože je chybné o staré a nové struktury vzhledem k sobě navzájem.

#### <a name="guidance-and-conclusions"></a>Pokyny a z něj závěry

* Výkon aplikace vyskytne-li dynamicky generované čísel.
* Rozhraní framework nelze vytvořit své vlastní pořadová čísla automaticky za běhu protože potřebné informace neexistuje. Pokud je zachycena v době kompilace.
* Nezapisujte dlouhé bloky ručně implementované `RenderTreeBuilder` logiku. Preferovat `.razor` soubory a umožňují kompilátoru řešit pořadová čísla.
* Pokud pořadová čísla jsou pevně zakódované, algoritmus diff pouze vyžaduje, že pořadová čísla zvýšení hodnoty. Výchozí hodnoty a mezery nejsou relevantní. Jednou z legitimní možností je používat jako pořadové číslo, číslo řádku kódu nebo začátek od nuly a zvýšit případů nebo stovky (nebo libovolný upřednostňované interval). 
* Blazor používá pořadová čísla, zatímco jiné architektury uživatelského rozhraní stromu rozdílování nepoužívejte. Rozdílování je mnohem rychlejší, když se používají pořadová čísla a Blazor nabízí výhodu v podobě kompilační krok, který se zabývá pořadová čísla automaticky pro vývojáře pro tvorbu `.razor` soubory.
