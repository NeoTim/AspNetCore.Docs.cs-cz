---
title: Vytvoření a použití Razor komponent ASP.NET Core
author: guardrex
description: Naučte se vytvářet a používat Razor komponenty, včetně toho, jak navazovat data, zpracovávat události a spravovat životní cykly komponent.
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
uid: blazor/components/index
ms.openlocfilehash: 6ee767ee76b622e15a1dc5a7fe2f3e05f03dabd0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628492"
---
# <a name="create-and-use-aspnet-core-no-locrazor-components"></a>Vytvoření a použití Razor komponent ASP.NET Core

Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Tobias Bartsch](https://www.aveo-solutions.com/)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Blazor aplikace jsou sestavené pomocí *komponent*. Součást je samostatně obsažený blok uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář. Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vkládání dat nebo reakci na události uživatelského rozhraní. Komponenty jsou flexibilní a odlehčené. Můžou být vnořené, opakovaně používané a sdílené mezi projekty.

## <a name="component-classes"></a>Třídy komponent

Komponenty jsou implementovány v [Razor](xref:mvc/views/razor) souborech komponenty ( `.razor` ) pomocí kombinace kódu jazyka C# a HTML. Komponenta v Blazor je formálně označována jako * Razor Komponenta*.

### <a name="no-locrazor-syntax"></a>Razor syntaktick

Razor komponenty v Blazor aplikacích výrazně používají Razor syntaxi. Pokud nejste obeznámeni s Razor jazykem označení, doporučujeme <xref:mvc/views/razor> před pokračováním číst.

Při přístupu k obsahu v Razor syntaxi věnujte zvláštní pozornost následujícím oddílům:

* [Direktivy](xref:mvc/views/razor#directives): `@` -předem stanovená vyhrazená klíčová slova, která obvykle mění způsob, jakým se kód komponenty analyzuje nebo funkce.
* [Atributy direktiv](xref:mvc/views/razor#directive-attributes): `@` -předem stanovená vyhrazená klíčová slova, která obvykle mění způsob, jakým jsou analyzovány prvky součásti nebo funkce.

### <a name="names"></a>Názvy

Název součásti musí začínat velkým znakem. Například `MyCoolComponent.razor` je platný a `myCoolComponent.razor` je neplatný.

### <a name="routing"></a>Směrování

Směrování do Blazor se dosahuje tím, že poskytuje šablonu směrování pro každou dostupnou součást aplikace. Když Razor je zkompilován soubor s [`@page`][9] direktivou, vygenerovaná třída je dána <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadáním šablony trasy. V době běhu Směrovač vyhledává třídy komponent pomocí <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> a a vykreslí, že jakákoli součást má šablonu směrování, která odpovídá požadované adrese URL. Další informace naleznete v tématu <xref:blazor/fundamentals/routing>.

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a>Kód

Uživatelské rozhraní pro komponentu je definováno pomocí jazyka HTML. Dynamická logika vykreslování (například cykly, podmíněné výrazy, výrazy) je přidána pomocí vložené syntaxe jazyka C# s názvem *Razor* . Při kompilaci aplikace jsou značky HTML a vykreslování jazyka C# převedeny na třídu komponenty. Název generované třídy se shoduje s názvem souboru.

Členy třídy komponenty jsou definovány v [`@code`][1] bloku. V [`@code`][1] bloku je stav součásti (vlastnosti, pole) zadán pomocí metod pro zpracování událostí nebo pro definování jiné logiky komponent. Je přípustný více než jeden [`@code`][1] blok.

Členy součásti lze použít jako součást logiky vykreslování komponenty pomocí výrazů jazyka C#, které začínají na `@` . Například pole C# se vykreslí pomocí předpony `@` na název pole. Následující příklad vyhodnocuje a vykresluje:

* `headingFontStyle` do hodnoty vlastnosti CSS pro `font-style` .
* `headingText` k obsahu `<h1>` elementu.

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

Po prvním vykreslení komponenty vygeneruje komponenta znovu svůj strom vykreslování v reakci na události. Blazor pak porovná nový strom vykreslování s předchozí a aplikuje všechny úpravy model DOM (Document Object Model) v prohlížeči (DOM).

Komponenty jsou běžné třídy jazyka C# a lze je umístit kamkoli v rámci projektu. Komponenty, které tvoří webové stránky, se obvykle nacházejí ve `Pages` složce. Komponenty mimo stránku jsou často umístěny do `Shared` složky nebo vlastní složky přidané do projektu.

### <a name="namespaces"></a>Obory názvů

Obor názvů komponenty obvykle je odvozen z kořenového oboru názvů aplikace a umístění komponenty (složka) v rámci aplikace. Pokud je kořenový obor názvů aplikace `BlazorSample` a součást se nachází `Counter` ve `Pages` složce:

* `Counter`Obor názvů součásti je `BlazorSample.Pages` .
* Plně kvalifikovaný název typu komponenty je `BlazorSample.Pages.Counter` .

Pro vlastní složky, které obsahují součásti, přidejte do [`@using`][2] nadřazené komponenty nebo do souboru aplikace direktivu `_Imports.razor` . V následujícím příkladu jsou komponenty ve `Components` složce k dispozici:

```razor
@using BlazorSample.Components
```

Na součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, které nevyžadují [`@using`][2] direktivu:

```razor
<BlazorSample.Components.MyComponent />
```

Obor názvů komponenty, která se vytvořila, Razor je založený na (v pořadí podle priority):

* [`@namespace`][8] označení v Razor souboru ( `.razor` ) značky ( `@namespace BlazorSample.MyNamespace` ).
* Projekt `RootNamespace` v souboru projektu ( `<RootNamespace>BlazorSample</RootNamespace>` ).
* Název projektu, pořízený z názvu souboru projektu ( `.csproj` ) a cesta z kořenového adresáře projektu k součásti. Například rozhraní překládá `{PROJECT ROOT}/Pages/Index.razor` ( `BlazorSample.csproj` ) na obor názvů `BlazorSample.Pages` . Komponenty následují pravidla vazeb názvů C#. Pro `Index` komponentu v tomto příkladu komponenty v oboru jsou všechny komponenty:
  * Ve stejné složce, `Pages` .
  * Komponenty v kořenu projektu, které explicitně neurčují jiný obor názvů.

> [!NOTE]
> `global::`Kvalifikace není podporovaná.
>
> Import komponent s příkazy s aliasem [`using`](/dotnet/csharp/language-reference/keywords/using-statement) (například `@using Foo = Bar` ) není podporován.
>
> Částečně kvalifikované názvy nejsou podporovány. Například přidání `@using BlazorSample` a odkazování na `NavMenu` komponentu ( `NavMenu.razor` ) s `<Shared.NavMenu></Shared.NavMenu>` není podporováno.

### <a name="partial-class-support"></a>Podpora částečné třídy

Razor komponenty jsou generovány jako částečné třídy. Razor komponenty jsou vytvořeny jedním z následujících přístupů:

* Kód jazyka C# je definován v [`@code`][1] bloku s označením HTML a Razor kódem v jednom souboru. Blazor šablony definují své Razor komponenty pomocí tohoto přístupu.
* Kód jazyka C# je umístěn v souboru kódu na pozadí, který je definován jako částečná třída.

Následující příklad ukazuje výchozí `Counter` komponentu s [`@code`][1] blokem v aplikaci vygenerovanou ze Blazor šablony. Kód HTML kódu Razor a kód jazyka C# jsou ve stejném souboru:

`Pages/Counter.razor`:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

`Counter`Komponentu lze také vytvořit pomocí souboru kódu na pozadí s částečnou třídou:

`Pages/Counter.razor`:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

`Counter.razor.cs`:

```csharp
namespace BlazorSample.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

Podle potřeby přidejte všechny požadované obory názvů do souboru dílčí třídy. Mezi obvyklé obory názvů používané Razor komponentami patří:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> [`@using`][2] direktivy v `_Imports.razor` souboru jsou aplikovány pouze na Razor soubory ( `.razor` ), nikoli soubory jazyka C# ( `.cs` ).

### <a name="specify-a-base-class"></a>Zadat základní třídu

[`@inherits`][6]Direktiva se dá použít k určení základní třídy pro komponentu. Následující příklad ukazuje, jak komponenta může dědit základní třídu, `BlazorRocksBase` k poskytnutí vlastností a metod komponenty. Základní třída by měla být odvozena od třídy <xref:Microsoft.AspNetCore.Components.ComponentBase> .

`Pages/BlazorRocks.razor`:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

`BlazorRocksBase.cs`:

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

### <a name="use-components"></a>Použití komponent

Komponenty mohou zahrnovat další komponenty deklarováním pomocí syntaxe elementu HTML. Označení pro použití komponenty vypadá jako značka HTML, kde název značky je typ komponenty.

Následující značka v `Pages/Index.razor` vykresluje `HeadingComponent` instanci:

```razor
<HeadingComponent />
```

`Components/HeadingComponent.razor`:

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

Pokud komponenta obsahuje element HTML s velkým prvním písmenem, které neodpovídá názvu komponenty, je vygenerováno upozornění označující, že element má neočekávaný název. Přidání [`@using`][2] direktivy pro obor názvů součásti zpřístupňuje komponentu, která vyřeší upozornění.

## <a name="parameters"></a>Parametry

### <a name="route-parameters"></a>Parametry směrování

Komponenty mohou přijímat parametry směrování z šablony směrování uvedené v [`@page`][9] direktivě. Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponent.

`Pages/RouteParameter.razor`:

[!code-razor[](index/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Volitelné parametry nejsou podporovány, takže [`@page`][9] v předchozím příkladu jsou aplikovány dvě direktivy. První umožňuje navigaci na součást bez parametru. Druhá [`@page`][9] direktiva přijme `{text}` parametr Route a přiřadí hodnotu `Text` Vlastnosti.

*Catch-All* Parameter Syntax ( `*` / `**` ), která zachycuje cestu mezi více hranicemi složek, není **not** v Razor součástech ( `.razor` ) podporováno.

### <a name="component-parameters"></a>Parametry součásti

Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí veřejných vlastností třídy komponenty s [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributem. Použijte atributy k určení argumentů pro komponentu v kódu.

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

V následujícím příkladu z ukázkové aplikace `ParentComponent` nastaví hodnotu `Title` vlastnosti `ChildComponent` .

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Nevytvářejte komponenty, které zapisují do vlastních *parametrů komponenty* , když je obsah komponenty vykreslen pomocí <xref:Microsoft.AspNetCore.Components.RenderFragment> , použijte místo toho soukromé pole. Další informace najdete v části [přepsané parametry `RenderFragment` ](#overwritten-parameters-with-renderfragment) v oddílu.

## <a name="child-content"></a>Podřízený obsah

Komponenty mohou nastavit obsah jiné součásti. Součást přiřazení poskytuje obsah mezi značkami, které určují přijímací komponentu.

V následujícím příkladu `ChildComponent` má `ChildContent` vlastnost, která představuje, který představuje <xref:Microsoft.AspNetCore.Components.RenderFragment> segment uživatelského rozhraní pro vykreslení. Hodnota `ChildContent` je umístěna v označení komponenty, kde má být obsah vykreslen. Hodnota `ChildContent` je přijímána z nadřazené komponenty a vykreslena v panelu Bootstrap `panel-body` .

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Vlastnost, která přijímá <xref:Microsoft.AspNetCore.Components.RenderFragment> obsah, musí být pojmenována `ChildContent` podle konvence.

`ParentComponent`V ukázkové aplikaci může poskytovat obsah pro vykreslování `ChildComponent` umístěním obsahu uvnitř `<ChildComponent>` značek.

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

Vzhledem k způsobu, jakým Blazor vykresluje podřízený obsah, vykreslování komponent uvnitř `for` smyčky vyžaduje proměnnou místního indexu, pokud se v obsahu podřízené součásti používá proměnná přírůstkového cyklu:
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Param1="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> Alternativně můžete použít `foreach` smyčku s <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Param1="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Seskupováním atributů a libovolné parametry

Komponenty mohou kromě deklarovaných parametrů komponenty zachytit a vykreslovat další atributy. Další atributy mohou být zachyceny ve slovníku a poté *splatted* na prvek při vykreslení komponenty pomocí [`@attributes`][3] Razor direktivy. Tento scénář je užitečný při definování komponenty, která vytváří prvek značky, který podporuje nejrůznější přizpůsobení. Například může být zdlouhavé definovat atributy samostatně pro objekt `<input>` , který podporuje mnoho parametrů.

V následujícím příkladu první `<input>` prvek ( `id="useIndividualParams"` ) používá jednotlivé parametry komponenty, zatímco druhý `<input>` element ( `id="useAttributesDict"` ) používá atribut seskupováním:

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

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

Typ parametru musí implementovat `IEnumerable<KeyValuePair<string, object>>` nebo `IReadOnlyDictionary<string, object>` s klíči řetězce.

Vykreslené `<input>` elementy pomocí obou přístupů jsou identické:

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

Chcete-li přijmout libovolné atributy, definujte parametr komponenty pomocí [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributu s <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> vlastností nastavenou na `true` :

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>Vlastnost on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) umožňuje, aby parametr odpovídal všem atributům, které se neshodují s žádným jiným parametrem. Komponenta může definovat pouze jeden parametr s <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> . Typ vlastnosti používaný pomocí <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> musí být přiřazovatelné z `Dictionary<string, object>` řetězcových klíčů. `IEnumerable<KeyValuePair<string, object>>` nebo `IReadOnlyDictionary<string, object>` jsou také možnosti v tomto scénáři.

Pozice [`@attributes`][3] relativní vzhledem k poloze atributů elementu je důležitá. Když [`@attributes`][3] jsou splatted na elementu, atributy jsou zpracovávány zprava doleva (poslední až první). Vezměte v úvahu následující příklad komponenty, která využívá `Child` komponentu:

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Child` `extra` Atribut komponenty je nastaven na hodnotu napravo od [`@attributes`][3] . `Parent`Vykreslená komponenta `<div>` obsahuje `extra="5"` při předání prostřednictvím dodatečného atributu, protože atributy jsou zpracovávány zprava doleva (poslední až první):

```html
<div extra="5" />
```

V následujícím příkladu `extra` je pořadí a [`@attributes`][3] obrácené v `Child` komponentě `<div>` :

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Vykreslená `<div>` `Parent` Komponenta obsahuje, `extra="10"` když je předána pomocí dalšího atributu:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Zachytit odkazy na komponenty

Odkazy na komponenty poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance, například `Show` nebo `Reset` . Zachytit odkaz na komponentu:

* Přidejte [`@ref`][4] atribut do podřízené součásti.
* Definujte pole stejného typu jako podřízená komponenta.

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Při vykreslení komponenty `loginDialog` je pole vyplněno `MyLoginDialog` instancí podřízené součásti. Pak můžete vyvolat metody .NET v instanci komponenty.

> [!IMPORTANT]
> `loginDialog`Proměnná je naplněna pouze po vykreslení komponenty a její výstup obsahuje `MyLoginDialog` element. Dokud se komponenta nevykresluje, neexistuje žádný odkaz na.
>
> Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování komponenty, použijte [ `OnAfterRenderAsync` `OnAfterRender` metody nebo](xref:blazor/components/lifecycle#after-component-render).
>
> Chcete-li použít referenční proměnnou s obslužnou rutinou události, použijte výraz lambda nebo přiřaďte delegáta obslužné rutiny události v [ `OnAfterRenderAsync` `OnAfterRender` metodách nebo](xref:blazor/components/lifecycle#after-component-render). Tím se zajistí, že referenční proměnná je přiřazena před přiřazením obslužné rutiny události.
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

Chcete-li odkazovat na součásti ve smyčce, viz [zachytit odkazy na více podobných podřízených komponent (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Při zachytávání odkazů na součásti použijte podobnou syntaxi pro [zachycení odkazů na prvky](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), není to funkce interoperability JavaScriptu. Odkazy na součásti nejsou předány kódu jazyka JavaScript. Odkazy na komponenty se používají pouze v kódu .NET.

> [!NOTE]
> Nepoužívejte odkazy na součásti **pro použití stavu** podřízených komponent. Místo toho použijte k předání dat podřízeným komponentám běžné deklarativní parametry. Použití běžných deklarativních parametrů má za následek podřízené komponenty, které jsou automaticky revykreslovány ve správný čas.

## <a name="synchronization-context"></a>Kontext synchronizace

Blazor používá kontext synchronizace ( <xref:System.Threading.SynchronizationContext> ) k vykonání jediného logického vlákna provádění. [Metody životního cyklu](xref:blazor/components/lifecycle) komponenty a všechna zpětná volání událostí, která jsou vyvolána, Blazor jsou spouštěna v kontextu synchronizace.

Blazor Serverkontext synchronizace se pokouší emulovat prostředí s jedním vláknem tak, aby přesně odpovídal modelu webového sestavení v prohlížeči, který je jediným vláknem. V jakémkoli daném časovém okamžiku se práce provádí v přesně jednom vlákně, což dává dojem o jednom logickém vlákně. Žádné dvě operace se neprovádějí současně.

### <a name="avoid-thread-blocking-calls"></a>Vyhněte se voláním blokujícím vlákna

Obecně platí, že Nevolejte následující metody. Následující metody zablokují vlákno, takže aplikace brání v pokračování práce, dokud se podklady <xref:System.Threading.Tasks.Task> nedokončí:

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a>Vyvolat metody komponenty externě na stav aktualizace

V případě, že komponenta musí být aktualizována na základě externí události, jako je například časovač nebo jiné oznámení, použijte `InvokeAsync` metodu, která odesílá Blazor kontext synchronizace. Představte si například *službu* pro upozorňování, která může oznámit všechny součásti, které jsou v aktualizovaném stavu:

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

Zaregistrujte `NotifierService` :

* V nástroji Blazor WebAssembly Zaregistrujte službu jako singleton v `Program.Main` :

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* V nástroji Blazor Server Zaregistrujte službu jako vymezenou v `Startup.ConfigureServices` :

  ```csharp
  services.AddScoped<NotifierService>();
  ```

`NotifierService`K aktualizaci součásti použijte.

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

V předchozím příkladu `NotifierService` vyvolá `OnNotify` metodu komponenty mimo Blazor kontext synchronizace. `InvokeAsync` slouží k přepnutí do správného kontextu a vykreslení vykreslování do fronty.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Použití \@ klíče k řízení uchovávání prvků a komponent

Při vykreslování seznamu prvků nebo komponent a následné změny prvků nebo komponent se Blazor musí rozhodnout, který z předchozích prvků nebo komponent může být zachován a jak se mají objekty modelu namapovat. Obvykle je tento proces automatický a může být ignorován, ale existují případy, kdy můžete chtít řídit proces.

Uvažujte následující příklad:

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Obsah `People` kolekce se může změnit vloženými, odstraněnými nebo znovu seřazenými položkami. Když se komponenta znovu vykreslí, může se tato `<DetailsEditor>` součást změnit, aby přijímala jiné `Details` hodnoty parametrů. To může způsobit složitější revykreslování, než se očekávalo. V některých případech může reprodukce vést k viditelným rozdílům v chování, jako je například ztracený fokus elementu.

Proces mapování lze řídit pomocí [`@key`][5] atributu direktiva. [`@key`][5] způsobí, že rozdílový algoritmus garantuje zachování prvků nebo komponent na základě hodnoty klíče:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Když se `People` kolekce změní, rozdílový algoritmus zachovává přidružení mezi `<DetailsEditor>` instancemi a `person` instancemi:

* Pokud `Person` se ze `People` seznamu odstraní, `<DetailsEditor>` z uživatelského rozhraní se odebere jenom odpovídající instance. Ostatní instance zůstanou beze změny.
* Pokud `Person` je vložena na nějaké místo v seznamu, `<DetailsEditor>` je do příslušné pozice vložena jedna nová instance. Ostatní instance zůstanou beze změny.
* Pokud `Person` jsou položky znovu seřazeny, odpovídající `<DetailsEditor>` instance jsou zachovány a znovu uspořádány v uživatelském rozhraní.

V některých scénářích použití [`@key`][5] minimalizuje složitost reprodukce a vyhne se potenciálním problémům se stavovou částí modelu DOM, jako je například pozice fokusu.

> [!IMPORTANT]
> Klíče jsou místní pro každý prvek kontejneru nebo komponentu. Klíče nejsou v dokumentu globálně porovnány.

### <a name="when-to-use-key"></a>Kdy použít \@ klíč

Obvykle má smysl použít [`@key`][5] při každém vykreslení seznamu (například v bloku [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ) a existuje vhodná hodnota pro definování [`@key`][5] .

Můžete také použít [`@key`][5] k zabránění Blazor v zachování prvku nebo podstromu komponenty při změně objektu:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Pokud se `@currentPerson` změní, [`@key`][5] direktiva Attribute vynutí Blazor zrušení celého `<div>` a jeho následníků a znovu sestaví podstrom v uživatelském rozhraní novými prvky a komponentami. To může být užitečné, pokud potřebujete zaručit, že se při změnách nezachovají stav uživatelského rozhraní `@currentPerson` .

### <a name="when-not-to-use-key"></a>Kdy nepoužívat \@ klíč

Při rozdílech s se účtují náklady na výkon [`@key`][5] . Náklady na výkon nejsou velké, ale zadávejte jenom [`@key`][5] v případě, že řízení pravidel uchovávání prvků nebo součástí má aplikace výhodu.

I v případě [`@key`][5] , že není použit, Blazor zachová podřízený element a instance komponenty co nejvíce. Jedinou výhodou použití [`@key`][5] je kontrola, *jak* jsou instance modelů mapovány na zachované instance komponent namísto rozdílového algoritmu výběru mapování.

### <a name="what-values-to-use-for-key"></a>Jaké hodnoty se mají použít pro \@ klíč

Obecně dává smysl pro zadání jednoho z následujících typů hodnot pro [`@key`][5] :

* Instance objektů modelu (například `Person` instance jako v předchozím příkladu). To zajišťuje zachování v závislosti na rovnosti odkazů na objekty.
* Jedinečné identifikátory (například hodnoty primárního klíče typu `int` , `string` nebo `Guid` ).

Zajistěte, aby hodnoty použité pro [`@key`][5] nekolidovat. Pokud jsou v rámci stejného nadřazeného prvku zjištěny hodnoty konfliktu, Blazor vyvolá výjimku, protože nemůže deterministické namapovat staré prvky nebo součásti na nové prvky nebo komponenty. Používejte pouze jedinečné hodnoty, například instance objektů nebo hodnoty primárního klíče.

## <a name="overwritten-parameters-with-renderfragment"></a>Přepsání parametrů s `RenderFragment`

Parametry jsou přepsány za následující podmínky:

* Obsah podřízené komponenty je vykreslen pomocí <xref:Microsoft.AspNetCore.Components.RenderFragment> .
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> je volána v nadřazené komponentě.

Parametry jsou resetovány, protože nadřazená komponenta je znovu vygenerována <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> , když je volána, a podřízené součásti jsou zadány nové hodnoty parametrů.

Vezměte v úvahu následující `Expander` komponentu:

* Vykreslí podřízený obsah.
* Přepíná zobrazení podřízeného obsahu s parametrem součásti.

```razor
<div @onclick="@Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

`Expander`Komponenta je přidána do nadřazené komponenty, která může zavolat <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

Zpočátku se `Expander` komponenty chovají nezávisle při `Expanded` přepínání jejich vlastností. Podřízené komponenty udržují své stavy podle očekávání. Když <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> je volána v nadřazeném prvku, `Expanded` parametr první podřízené komponenty se obnoví zpět na původní hodnotu ( `true` ). Hodnota druhé `Expander` součásti `Expanded` není resetována, protože ve druhé komponentě není vykreslen žádný podřízený obsah.

Chcete-li zachovat stav v předchozím scénáři, použijte *soukromé pole* v `Expander` součásti k údržbě jeho přepnutého stavu.

Následující revidovaná `Expander` součást:

* Akceptuje `Expanded` hodnotu parametru komponenty z nadřazené položky.
* Přiřadí hodnotu parametru komponenty k *privátnímu poli* ( `expanded` ) v [události s inicializací](xref:blazor/components/lifecycle#component-initialization-methods).
* Pomocí soukromého pole udržuje stav interního přepínání.

```razor
<div @onclick="@Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="apply-an-attribute"></a>Použití atributu

Atributy lze použít pro Razor součásti s [`@attribute`][7] direktivou. Následující příklad používá [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atribut pro třídu komponenty:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a>Podmíněné atributy elementu HTML

Atributy elementu HTML jsou podmíněně vykresleny na základě hodnoty .NET. Pokud je hodnota `false` nebo `null` , atribut není vykreslen. Pokud je hodnota `true` , je vygenerována hodnota atributu minimalizovaný.

V následujícím příkladu určuje, `IsCompleted` zda `checked` je vykreslena v kódu elementu:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Pokud `IsCompleted` je `true` , zaškrtávací políčko je vykresleno jako:

```html
<input type="checkbox" checked />
```

Pokud `IsCompleted` je `false` , zaškrtávací políčko je vykresleno jako:

```html
<input type="checkbox" />
```

Další informace naleznete v tématu <xref:mvc/views/razor>.

> [!WARNING]
> Některé atributy HTML, například [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) , nefungují správně, pokud je typ .NET `bool` . V těchto případech použijte `string` typ místo typu `bool` .

## <a name="raw-html"></a>Nezpracovaný kód HTML

Řetězce jsou obvykle vykreslovány pomocí textových uzlů modelu DOM, což znamená, že všechny značky, které mohou obsahovat, se ignorují a považují se za text literálu. Chcete-li vykreslit nezpracovaný kód HTML, zabalte obsah HTML do `MarkupString` hodnoty. Hodnota je analyzována jako HTML nebo SVG a vložena do modelu DOM.

> [!WARNING]
> Vykreslování nezpracovaného HTML vytvořeného z jakéhokoli nedůvěryhodného zdroje je **bezpečnostní riziko** a mělo by se jim vyhnout!

Následující příklad ukazuje použití `MarkupString` typu pro přidání bloku statického obsahu HTML do vykresleného výstupu komponenty:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="no-locrazor-templates"></a>Razor šablony

Fragmenty vykreslování lze definovat pomocí Razor syntaxe šablony. Razor Šablony představují způsob, jak definovat fragment uživatelského rozhraní a předpokládat následující formát:

```razor
@<{HTML tag}>...</{HTML tag}>
```

Následující příklad ukazuje, jak zadat <xref:Microsoft.AspNetCore.Components.RenderFragment> a <xref:Microsoft.AspNetCore.Components.RenderFragment%601> hodnoty a vykreslit šablony přímo v součásti. Fragmenty vykreslování mohou být také předány jako argumenty [součástem šablon](xref:blazor/components/templated-components).

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Vykreslený výstup předchozího kódu:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a>Statické prostředky

Blazorpostupuje podle konvence ASP.NET Core aplikací, které umísťují statické prostředky do [ `web root (wwwroot)` složky](xref:fundamentals/index#web-root)projektu.

Použijte základní cestu ( `/` ) pro odkaz na webový kořenový adresář pro statický prostředek. V následujícím příkladu `logo.png` je fyzicky umístěný ve `{PROJECT ROOT}/wwwroot/images` složce:

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor komponenty **nepodporují** vlnové lomítko ( `~/` ).

Informace o nastavení základní cesty aplikace najdete v tématu <xref:blazor/host-and-deploy/index#app-base-path> .

## <a name="tag-helpers-arent-supported-in-components"></a>V součástech nejsou podporovány pomocníky značek.

[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) nepodporuje se v Razor součástech ( `.razor` soubory). Chcete-li poskytnout funkci, jako je například Pomocník pro Blazor vytváření značek, vytvořte komponentu se stejnou funkcí jako pomocník značek a místo ní použijte komponentu.

## <a name="scalable-vector-graphics-svg-images"></a>Obrázky ve formátu SVG (Scalable Vector Graphics)

Vzhledem k tomu, že Blazor VYKRESLUJE HTML, obrázky podporované prohlížečem, včetně obrázků SVG (Scalable Vector Graphics) ( `.svg` ), jsou podporovány prostřednictvím `<img>` značky:

```html
<img alt="Example image" src="some-image.svg" />
```

Podobně jsou obrázky SVG podporovány v pravidlech CSS souboru šablony stylů ( `.css` ):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Vložené značky SVG se však ve všech scénářích nepodporují. Pokud `<svg>` značku umístíte přímo do souboru komponenty ( `.razor` ), podporuje se základní vykreslování obrázků, ale mnoho pokročilých scénářů ještě není podporováno. Například `<use>` značky nejsou aktuálně dodržovány a [`@bind`][10] nelze je použít s některými značkami SVG. Další informace naleznete v tématu [Podpora SVG in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).

## <a name="additional-resources"></a>Další zdroje informací

* <xref:blazor/security/server/threat-mitigation>: Obsahuje pokyny pro vytváření Blazor Server aplikací, které se musí soupeří s vyčerpáním prostředků.

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
