---
title: Vytváření a používání komponent Razor
author: guardrex
description: Zjistěte, jak vytvořit a používat komponenty Razor, včetně jak vázat na data, zpracování událostí a spravovat životní cykly komponenty.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: razor-components/components
ms.openlocfilehash: d1e8075ff1ac6695c98973e6abd8480a78761d92
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410421"
---
# <a name="create-and-use-razor-components"></a>Vytváření a používání komponent Razor

Podle [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), a [Morné Zaayman](https://github.com/MorneZaayman)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ([stažení](xref:index#how-to-download-a-sample)). Najdete v článku [Začínáme](xref:razor-components/get-started) tématu pro požadavky.

Razor komponenty aplikace jsou sestaveny na základě *komponenty*. Komponenta je samostatná blok uživatelského rozhraní (UI), například stránky, dialogové okno nebo formuláře. Komponenta obsahuje kód HTML a zpracování logiku potřebnou k vložení dat nebo v reakci na události uživatelského rozhraní. Součásti jsou flexibilní a jednoduchý. Mohou být vnořené, znovu použít a sdílet mezi projekty.

## <a name="component-classes"></a>Třídy součásti

Součásti jsou zpravidla implementovaní v *.cshtml* soubory pomocí kombinace C# a značka jazyka HTML. Uživatelské rozhraní pro součást je definován v jazyce HTML. Dynamické vykreslování logiku (například smyčky, podmíněné příkazy, výrazy) přidána pomocí vložený C# syntaxe volá [Razor](xref:mvc/views/razor). Když je kompilován Razor součásti aplikace, bude značka jazyka HTML a C# logiku vykreslení se převedou na třídu komponenty. Název generované třídy odpovídá názvu souboru.

Členy třídy komponenty jsou definovány v `@functions` blok (více než jeden `@functions` blok je povolený). V `@functions` bloku, stav komponent (vlastnosti, pole) je zadaný společně s metody pro zpracování událostí nebo definování dalších součástí logiky.

Komponenta členy můžete poté použita jako tato součást je vykreslování pomocí logiky C# výrazy, které začínají `@`. Například C# pole se vykreslí vložením prefixu `@` na název pole. Následující příklad vyhodnotí a vykreslí:

* `_headingFontStyle` Hodnota vlastnosti šablon stylů CSS pro `font-style`.
* `_headingText` k obsahu `<h1>` elementu.

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@functions {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Po se zpočátku zobrazí komponentu obnoví komponenty jeho vykreslení stromu v reakci na události. Součásti Razor poté porovnává větve vykreslení oproti předchozímu a platí všechny změny do prohlížeče Document Object Model (DOM).

## <a name="using-components"></a>Pomocí komponent

Součásti můžete zahrnout další součásti je deklarací pomocí syntaxe elementu HTML. Kód pro použití komponenty vypadá jako značku jazyka HTML, kde název značky je typ součásti.

Následující kód vykreslí `HeadingComponent` (*HeadingComponent.cshtml*) instance:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.cshtml?name=snippet_HeadingComponent)]

## <a name="component-parameters"></a>Parametry komponenty

Může mít komponenty *parametry komponenty*, které jsou definovány pomocí *neveřejné* vlastnosti komponentní třída upravené pomocí `[Parameter]`. Atributy můžete zadat argumenty pro komponentu v kódu.

V následujícím příkladu `ParentComponent` nastaví hodnotu vlastnosti `Title` vlastnost `ChildComponent`:

*ParentComponent.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?name=snippet_ParentComponent&highlight=5)]

*ChildComponent.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=7-8)]

## <a name="child-content"></a>Podřízený obsah

Součásti můžete nastavit obsah jiné součásti. Přiřazení součásti najdete zde obsah mezi značky, které určují přijímající komponenty. Například `ParentComponent` můžete zadat obsah pro vykreslování podle podřízené součásti tak, že je obsah uvnitř `<ChildComponent>` značky.

*ParentComponent.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?name=snippet_ParentComponent&highlight=6-7)]

Obsahuje podřízené součásti `ChildContent` vlastnost, která představuje `RenderFragment`. Hodnota `ChildContent` je umístěn ve značkách podřízené součásti, kde má být vykreslen obsah. V následujícím příkladu, hodnota `ChildContent` přijme od nadřazené komponenty a vykreslit v rámci panelu Bootstrap `panel-body`.

*ChildComponent.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=3,10-11)]

> [!NOTE]
> Vlastnost příjmu `RenderFragment` obsahu musí mít název `ChildContent` konvencí.

## <a name="data-binding"></a>Vytváření datových vazeb

Vazba dat na komponent a prvky modelu DOM se dosahuje pomocí `bind` atribut. Následující příklad vytvoří vazbu `ItalicsCheck` vlastnost na zaškrtávací políčko zaškrtnuto, stav:

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    bind="@_italicsCheck" />
```

Při zaškrtnutí políčka je a zrušte zaškrtnutí, hodnota vlastnosti je aktualizována na `true` a `false`v uvedeném pořadí.

Zaškrtávací políčko se aktualizuje v uživatelském rozhraní, pouze v případě, že součást je vykresleno, ne v reakci na měnící se hodnota vlastnosti. Protože komponenty vykreslování sami po spuštění kódu obslužné rutiny události, aktualizace vlastností se obvykle projeví v uživatelském rozhraní ihned.

Pomocí `bind` s `CurrentValue` vlastnosti (`<input bind="@CurrentValue" />`) je v podstatě ekvivalentní následujícímu:

```cshtml
<input value="@CurrentValue" 
    onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

Při vykreslování komponentu `value` elementu input pochází z `CurrentValue` vlastnost. Když uživatel zadá v textovém poli `onchange` událost se aktivuje a `CurrentValue` je nastavena na hodnotu změněné. Ve skutečnosti je generování kódu poněkud složitější, protože `bind` zpracovává několik případů, kdy jsou provedeny převody typu. V zásadě `bind` přidruží aktuální hodnotu výrazu s `value` obslužné rutiny a atributu změny pomocí zaregistrovaná obslužná rutina.

**Formátovací řetězce**

Vytváření datových vazeb funguje s <xref:System.DateTime> řetězce formátu. V tuto chvíli nejsou k dispozici jiných výrazech formátu, například měny nebo číselných formátů.

```cshtml
<input bind="@StartDate" format-value="yyyy-MM-dd" />

@functions {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

`format-value` Atribut specifikuje formát data použít `value` z `input` elementu. Formát slouží také k analýze hodnotu při `onchange` dojde k události.

**Parametry komponenty**

Vazba také rozpozná parametry komponenty, kde `bind-{property}` mohl vytvořit vazbu vlastnosti komponentami.

Používá následující komponenty `ChildComponent` a vytvoří vazbu `ParentYear` parametr z nadřazeného `Year` parametru u podřízené součásti:

Nadřazené komponenty:

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent bind-Year="@ParentYear" />

<button class="btn btn-primary" onclick="@ChangeTheYear">
    Change Year to 1986
</button>

@functions {
    [Parameter]
    private int ParentYear { get; set; } = 1978;

    void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

Podřízené součásti:

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@functions {
    [Parameter]
    private int Year { get; set; }

    [Parameter]
    private Action<int> YearChanged { get; set; }
}
```

`Year` Parametr je s možností vazby, protože má doprovodná `YearChanged` událost, která odpovídá typu používaného `Year` parametru.

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

## <a name="event-handling"></a>Zpracování událostí

Součásti syntaxe Razor poskytují funkce zpracování událostí. Atribut HTML elementu s názvem `on<event>` (například `onclick`, `onsubmit`) s hodnotou typu delegáta Razor součásti považuje za hodnotu atributu obslužné rutiny události. Název atributu vždy začíná `on`.

Následující kód volá `UpdateHeading` metodu po výběru tlačítka v uživatelském rozhraní:

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

Následující kód volá `CheckboxChanged` metoda při změně zaškrtávacího políčka v uživatelském rozhraní:

```cshtml
<input type="checkbox" class="form-check-input" onchange="@CheckboxChanged" />

@functions {
    void CheckboxChanged()
    {
        ...
    }
}
```

Obslužné rutiny událostí může být také asynchronní a zpět <xref:System.Threading.Tasks.Task>. Není nutné ručně volat `StateHasChanged()`. Výjimky se protokolují, když k nim dojde.

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

Pro některé události nejsou povoleny typy argumentů události specifické pro události. Pokud přístup k jednomu z těchto typů událostí není nezbytné, není potřeba ve volání metody.

Seznam podporovaných událostí argumentů je:

* UIEventArgs
* UIChangeEventArgs
* UIKeyboardEventArgs
* UIMouseEventArgs

Výrazy lambda lze také použít:

```cshtml
<button onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Často je vhodné zavřít další hodnoty, například během iterace přes sadu elementů. Následující příklad vytvoří tři tlačítka, každý z který volá `UpdateHeading` předání argumentu události (`UIMouseEventArgs`) a tlačítko (`buttonNumber`) při výběru v uživatelském rozhraní:

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary" 
            onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@functions {
    string message = "Select a button to learn its position.";

    void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            "mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

## <a name="capture-references-to-components"></a>Zachycení odkazy na komponenty

Komponenta odkazy poskytují způsob, jak získat odkaz na instanci komponenty tak, aby příkazy do této instance, můžete vydat, jako `Show` nebo `Reset`. Chcete-li zachytit odkazem komponenty, přidejte `ref` atribut podřízené součásti a pak definovat pole se stejným názvem a stejného typu jako podřízené součásti.

```cshtml
<MyLoginDialog ref="loginDialog" ... />

@functions {
    MyLoginDialog loginDialog;

    void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Při vykreslování komponentu `loginDialog` pole se vyplní `MyLoginDialog` podřízené instance komponenty. Potom můžete vyvolat metody rozhraní .NET na instanci komponenty.

> [!IMPORTANT]
> `loginDialog` Proměnná je vyplněný pouze poté, co se vykreslí komponentu a zahrnuje její výstup `MyLoginDialog` elementu. Až do bodu není nutné nic odkazovat. K manipulaci s odkazy na součásti po dokončení vykreslení komponentu, použijte `OnAfterRenderAsync` nebo `OnAfterRender` metody.

Při zachytávání odkazů na komponenty používá podobné syntaxi k [zachytávání odkazy na prvky](xref:razor-components/javascript-interop#capture-references-to-elements), není [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:razor-components/javascript-interop) funkce. Nejsou součástí odkazy předané do kódu jazyka JavaScript; se používá pouze v kódu .NET.

> [!NOTE]
> Proveďte **není** mutovat stavu podřízenými komponentami pomocí odkazů na komponenty. Místo toho použijte normální deklarované parametry k předání dat podřízenými komponentami. To způsobí, že podřízené součásti rerender ve správném čase automaticky.

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

`OnParametersSetAsync` a `OnParametersSet` se volá, když součást přijme parametry ze svého nadřazeného objektu a hodnoty jsou přiřazeny k vlastnostem. Tyto metody jsou provedeny po inicializaci součásti a poté je vykreslen komponentu pokaždé, když:

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

`SetParameters` může být potlačena za účelem spouštění kódu předtím, než jsou nastavené parametry:

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

Pokud `base.SetParameters` není vyvolána, můžete vlastní kód interpretaci příchozí hodnoty parametrů v jakékoli požadované stejně, jako. Například příchozí parametry nejsou potřeba přiřadit k vlastnosti ve třídě.

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

@functions {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a>Směrování

Směrování v součástech Razor se dosahuje tím, že poskytuje šablona trasy pro jednotlivé dostupné komponenty v aplikaci.

Při *.cshtml* soubor s `@page` – direktiva je zkompilován, dostane generované třídy <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadání šablonu trasy. Za běhu, směrovač hledá komponentní třídy s `RouteAttribute` a vykreslí podle toho, která komponenta má šablona trasy, která odpovídá požadovanou adresu URL.

Více šablon trasy můžete použít pro komponentu. Následující komponenty jsou reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a>Parametry trasy

Součásti mohou přijímat parametry trasy z šablonu trasy, které jsou součástí `@page` směrnice. Směrovač používá parametry trasy k naplnění odpovídající komponenta parametry.

*RouteParameter.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?name=snippet_RouteParameter)]

Volitelné parametry nejsou podporovány, tedy dvě `@page` direktivy se použijí v předchozím příkladu. První umožňuje přechod na komponenty bez parametrů. Druhá `@page` trvá – direktiva `{text}` parametr trasa a přiřadí hodnotu do proměnné `Text` vlastnost.

## <a name="base-class-inheritance-for-a-code-behind-experience"></a>Základní třída dědičnosti "použití modelu code-behind" prostředí

Soubory komponent (*.cshtml*) kombinovat kód HTML a C# zpracování kódu ve stejném souboru. `@inherits` – Direktiva je možné poskytovat prostředí "použití modelu code-behind", který odděluje komponenty značek z zpracování kódu Razor součásti aplikace.

[Ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ukazuje, jak komponenty může dědit základní třídy `BlazorRocksBase`, aby vznikl komponenty vlastnosti a metody.

*BlazorRocks.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.cshtml?name=snippet_BlazorRocks)]

*BlazorRocksBase.cs*:

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

Základní třída musí být odvozený z: `BlazorComponent`.

## <a name="razor-support"></a>Podpora Razor

**Direktivy Razor**

V následující tabulce jsou uvedeny direktivy Razor.

| – Direktiva | Popis |
| --------- | ----------- |
| [@functions](xref:mvc/views/razor#section-5) | Přidá C# blok kódu na komponentu. |
| `@implements` | Implementuje rozhraní pro třídu vygenerované komponenty. |
| [@inherits](xref:mvc/views/razor#section-3) | Poskytuje plnou kontrolu nad třídu, která dědí komponentu. |
| [@inject](xref:mvc/views/razor#section-4) | Vkládání ze služby umožňuje [kontejneru služby](xref:fundamentals/dependency-injection). Další informace najdete v tématu [injektáž závislostí do zobrazení](xref:mvc/views/dependency-injection). |
| `@layout` | Určuje komponentu rozložení. Rozložení komponenty umožňují zabránit zdvojení kódu a nekonzistence. |
| [@page](xref:razor-pages/index#razor-pages) | Určuje, že by měla komponenta zpracování požadavků přímo. `@page` – Direktiva je možné zadat při trasy a volitelné parametry. Na rozdíl od Razor Pages `@page` – direktiva nemusí být první – direktiva v horní části souboru. Další informace najdete v tématu [směrování](xref:razor-components/routing). |
| [@using](xref:mvc/views/razor#using) | Přidá C# `using` směrnice do třídy vygenerované komponenty. |
| [@addTagHelper](xref:mvc/views/razor#tag-helpers) | Použít `@addTagHelper` použít komponentu v jiném sestavení než sestavení aplikace. |

**Podmíněné atributy**

Atributy jsou vykreslovány podmíněně na základě hodnoty .NET. Pokud je hodnota `false` nebo `null`, atribut není vykreslen. Pokud je hodnota `true`, atribut je vykreslen minimalizovaný.

V následujícím příkladu `IsCompleted` Určuje, zda `checked` se vykreslí v značky ovládacího prvku:

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@functions {
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

@functions {
    string myMarkup = "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a>Bez vizuálního vzhledu součásti

Bez vizuálního vzhledu komponenty jsou součástí, které přijímají jeden nebo více šablon uživatelského rozhraní jako parametry, které lze použít jako součást logiky komponenty vykreslování. Bez vizuálního vzhledu komponenty umožňují vytvářet vyšší úrovně součásti, které jsou více než regulární komponenty opakovaně použitelné. Zahrnují několik příkladů:

* Komponenta tabulky, která umožňuje uživateli zadat šablony pro záhlaví tabulky, řádky a zápatí.
* Seznam součástí, která umožňuje uživateli zadat šablonu pro vykreslování položky v seznamu.

### <a name="template-parameters"></a>Parametry šablony

Bez vizuálního vzhledu součásti je definován tak, že zadáte jeden nebo více parametrů součást typu `RenderFragment` nebo `RenderFragment<T>`. Vykreslení fragment reprezentuje segment, který uživatelského rozhraní, které je vykresleno komponentou. Vykreslení fragment volitelně přebírá parametr, který lze zadat, pokud je vyvolána fragment vykreslení.

*Components/TableTemplate.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.cshtml)]

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

Bez vizuálního vzhledu součásti jsou často obecně typu. Například komponentu obecného seznamu zobrazit šablonu můžete použít k vykreslení `IEnumerable<T>` hodnoty. K definování obecné součásti, použijte `@typeparam` směrnice a určete parametry typu.

*Components/ListViewTemplate.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.cshtml?highlight=1)]

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

V následujícím *motiv* příklad z ukázkové aplikace `ThemeInfo` třída určuje informace o motivech tok součástí hierarchií směrem dolů, aby všechna tlačítka v rámci dané části aplikace sdílet stejný styl.

*UIThemeClasses/ThemeInfo.cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Jako součást předchůdce může poskytnout kaskádové hodnotu pomocí komponenty kaskádové hodnotu. Součást CSS hodnoty zabalí podstrom součástí hierarchie a poskytuje jednu hodnotu pro všechny komponenty v rámci této podstrom.

Například ukázkové aplikace určuje informace o motivech (`ThemeInfo`) v jednom z rozložení aplikace jako parametr šablony pro všechny součásti, které tvoří rozložení textu `@Body` vlastnost. `ButtonClass` je přiřazena hodnota `btn-success` v komponentě rozložení. Všechny podřízené součásti mohou využívat tuto vlastnost prostřednictvím `ThemeInfo` šablony objektu.

*Shared/CascadingValuesParametersLayout.cshtml*:

```cshtml
@inherits BlazorLayoutComponent
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

@functions {
    ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Chcete-li pomocí kaskádových hodnot, komponenty deklarovat kaskádové parametry s využitím `[CascadingParameter]` atribut nebo na základě hodnoty názvu řetězec:

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")] PermInfo Permissions { get; set; }
```

Vazba s hodnotou řetězce názvu platí, pokud máte více kaskádových hodnot stejného typu a pro jejich odlišení v rámci stejné podstrom.

Kaskádové hodnoty se váží k parametrům šablony podle typu.

V ukázkové aplikaci, komponentě CSS motiv hodnoty parametrů vytvoří vazbu `ThemeInfo` kaskádové hodnotu pro parametr šablony. Tento parametr se používá nastavení třídy šablony stylů CSS pro jedno z tlačítek zobrazí komponentou.

*Pages/CascadingValuesParametersTheme.cshtml*:

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" onclick="@IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" onclick="@IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@functions {
    int currentCount = 0;

    [CascadingParameter] protected ThemeInfo ThemeInfo { get; set; }

    void IncrementCount()
    {
        currentCount++;
    }
}
```

### <a name="tabset-example"></a>Příklad TabSet

Parametry šablony také povolit součásti spolupracovat napříč hierarchií komponenty. Představte si třeba následující *TabSet* příklad v ukázkové aplikaci.

Ukázková aplikace má `ITab` rozhraní, které karty implementace:

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

Komponenta CSS hodnoty parametrů TabSet používá součásti záložku, která obsahuje několik komponent kartu:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.cshtml?name=snippet_TabSet)]

Podřízené kartu komponenty nejsou explicitně předány jako parametry na kartě nastavení. Místo toho podřízené kartu komponenty jsou součástí podřízenému obsahu ovládacího prvku na kartě nastavení. Však nastavení kartu stále potřebuje vědět o jednotlivých součástech kartu tak, aby ho může mít za následek záhlaví a na aktivní kartě. Povolit koordinace bez potřeby dalšího kódu, nastavte kartu komponenty *samotný můžete zadat jako hodnotu kaskádové* , který se potom vybere potomka kartu komponenty.

*Components/TabSet.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.cshtml)]

Podřízené součásti zachycení kartu obsahující kartu nastavit jako parametr šablony, karta součásti přidaly na kartu Nastavení a souřadnice na které kartě je aktivní.

*Components/Tab.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.cshtml)]

## <a name="razor-templates"></a>Šablony Razor

Vykreslení fragmenty lze definovat pomocí syntaxe šablon Razor. Šablony Razor představují způsob, jak definovat fragment kódu uživatelského rozhraní a předpokládají následující formát:

```cshtml
@<tag>...</tag>
```

Následující příklad ukazuje, jak určit `RenderFragment` a `RenderFragment<T>` hodnoty.

*RazorTemplates.cshtml*:

```cshtml
@{
    RenderFragment template = @<p>The time is @DateTime.Now.</p>;
    RenderFragment<Pet> petTemplate = (pet) => @<p>Your pet's name is @pet.Name.</p>;
}
```

Vykreslení fragmenty definované pomocí syntaxe Razor šablony mohou být předány jako argumenty bez vizuálního vzhledu součásti nebo vykresluje přímo. Předchozí šablony jsou přímo vykreslí následujícím kódem Razor:

```cshtml
@template

@petTemplate(new Pet { Name = "Rex" })
```

Vykresleného výstupu:

```
The time is 10/04/2018 01:26:52.

Your pet's name is Rex.
```
