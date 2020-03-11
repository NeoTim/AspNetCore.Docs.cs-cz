---
title: Vytváření a používání ASP.NET Corech komponent Razor
author: guardrex
description: Naučte se vytvářet a používat komponenty Razor, včetně toho, jak navazovat na data, zpracovávat události a spravovat životní cykly komponent.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: e444ebfef5143a6c33ed2d122933903ad3a4f4a7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660697"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Vytváření a používání ASP.NET Corech komponent Razor

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

aplikace Blazor jsou sestaveny pomocí *komponent*. Součást je samostatně obsažený blok uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář. Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vkládání dat nebo reakci na události uživatelského rozhraní. Komponenty jsou flexibilní a odlehčené. Můžou být vnořené, opakovaně používané a sdílené mezi projekty.

## <a name="component-classes"></a>Třídy komponent

Komponenty jsou implementovány v souborech komponenty [Razor](xref:mvc/views/razor) ( *. Razor*) pomocí kombinace kódu C# a kódu HTML. Komponenta v Blazor je formálně označována jako *Komponenta Razor*.

Název součásti musí začínat velkým znakem. Například *MyCoolComponent. Razor* je platný a *MyCoolComponent. Razor* je neplatný.

Uživatelské rozhraní pro komponentu je definováno pomocí jazyka HTML. Dynamická logika vykreslování (například smyčky, podmíněné výrazy, výrazy) se přidá pomocí vložené C# syntaxe s názvem [Razor](xref:mvc/views/razor). Při kompilaci aplikace jsou značky kódu HTML a C# vykreslovací logiky převedeny na třídu součásti. Název generované třídy se shoduje s názvem souboru.

Členy třídy komponenty jsou definovány v `@code`ovém bloku. V bloku `@code` je stav součásti (vlastnosti, pole) zadán pomocí metod pro zpracování událostí nebo pro definování jiné logiky komponent. Je přípustný více než jeden blok `@code`.

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

Komponenty jsou běžné C# třídy a lze je umístit kamkoli v rámci projektu. Komponenty, které tvoří webové stránky, jsou obvykle umístěny ve složce *stránky* . Komponenty mimo stránku jsou často umístěny ve *sdílené* složce nebo vlastní složce přidané do projektu.

Obor názvů komponenty obvykle je odvozen z kořenového oboru názvů aplikace a umístění komponenty (složka) v rámci aplikace. Pokud je kořenový obor názvů aplikace `BlazorApp` a komponenta `Counter` se nachází ve složce *stránky* :

* Obor názvů součásti `Counter` je `BlazorApp.Pages`.
* Plně kvalifikovaný název typu komponenty je `BlazorApp.Pages.Counter`.

Další informace naleznete v části [Import komponent](#import-components) .

Chcete-li použít vlastní složku, přidejte obor názvů vlastní složky buď do nadřazené komponenty, nebo do souboru *_Imports. Razor* aplikace. Například následující obor názvů zpřístupňuje komponenty ve složce *Components* , když je kořenový obor názvů aplikace `BlazorApp`:

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a>Statické prostředky

Blazor se řídí konvencí ASP.NET Core aplikací, které umísťují statické prostředky do [kořenové složky webu projektu (wwwroot)](xref:fundamentals/index#web-root).

K odkazování na webový kořenový adresář pro statický prostředek použijte cestu relativní vzhledem k základnímu prostředku (`/`). V následujícím příkladu je soubor *logo. png* fyzicky umístěný ve složce *{Project root}/wwwroot/images* :

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Komponenty Razor **nepodporují notaci** vlnovku (`~/`).

Informace o nastavení základní cesty aplikace najdete v tématu <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="tag-helpers-arent-supported-in-components"></a>V součástech nejsou podporovány pomocníky značek.

V součástech Razor (soubory *. Razor* ) nejsou podporované [pomocníky značek](xref:mvc/views/tag-helpers/intro) . Chcete-li v Blazorposkytnout funkce podobné pomocníkům, vytvořte komponentu se stejnou funkcí jako pomocník značek a místo ní použijte komponentu.

## <a name="use-components"></a>Použití komponent

Komponenty mohou zahrnovat další komponenty deklarováním pomocí syntaxe elementu HTML. Označení pro použití komponenty vypadá jako značka HTML, kde název značky je typ komponenty.

Vazba atributu rozlišuje velká a malá písmena. Například `@bind` je platný a `@Bind` je neplatný.

Následující kód v *indexu. Razor* vykresluje instanci `HeadingComponent`:

```razor
<HeadingComponent />
```

*Components/HeadingComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Pokud komponenta obsahuje element HTML s velkým prvním písmenem, které neodpovídá názvu komponenty, je vygenerováno upozornění označující, že element má neočekávaný název. Přidání direktivy `@using` pro obor názvů součásti zpřístupňuje komponentu, která vyřeší upozornění.

## <a name="routing"></a>Směrování

Směrování v Blazor dosáhnete tak, že v aplikaci poskytnete šablonu směrování pro každou dostupnou součást.

Když je zkompilován soubor Razor s direktivou `@page`, vygenerovaná třída má <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> určující šablonu trasy. V době běhu směrovač vyhledá třídy komponent pomocí `RouteAttribute` a vykreslí, že každá komponenta má šablonu směrování, která odpovídá požadované adrese URL.

```razor
@page "/ParentComponent"

...
```

Další informace naleznete v tématu <xref:blazor/routing>.

## <a name="parameters"></a>Parametry

### <a name="route-parameters"></a>Parametry směrování

Komponenty mohou přijímat parametry směrování z šablony směrování uvedené v direktivě `@page`. Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponent.

*Stránky/RouteParameter. Razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Volitelné parametry nejsou podporované, takže se v předchozím příkladu použijí dvě direktivy `@page`. První umožňuje navigaci na součást bez parametru. Druhá direktiva `@page` přijímá parametr trasy `{text}` a přiřazuje hodnotu vlastnosti `Text`.

*Catch-All –* syntaxe parametru (`*`/`**`), která zachycuje cestu mezi více hranicemi složek, **není v** součástech Razor ( *. Razor*) podporována.

### <a name="component-parameters"></a>Parametry součásti

Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí veřejných vlastností třídy komponenty s atributem `[Parameter]`. Použijte atributy k určení argumentů pro komponentu v kódu.

*Components/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

V následujícím příkladu z ukázkové aplikace `ParentComponent` nastaví hodnotu vlastnosti `Title` `ChildComponent`.

*Stránky/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a>Podřízený obsah

Komponenty mohou nastavit obsah jiné součásti. Součást přiřazení poskytuje obsah mezi značkami, které určují přijímací komponentu.

V následujícím příkladu má `ChildComponent` vlastnost `ChildContent`, která představuje `RenderFragment`, která představuje segment uživatelského rozhraní pro vykreslení. Hodnota `ChildContent` je umístěna v označení komponenty, kde má být obsah vykreslen. Hodnota `ChildContent` je přijímána z nadřazené komponenty a vykreslena v `panel-body`panelu Bootstrap.

*Components/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Vlastnost, která přijímá obsah `RenderFragment`, musí mít název `ChildContent` podle konvence.

`ParentComponent` v ukázkové aplikaci může poskytovat obsah pro vykreslování `ChildComponent` umístěním obsahu do značek `<ChildComponent>`.

*Stránky/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

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

## <a name="capture-references-to-components"></a>Zachytit odkazy na komponenty

Odkazy na komponenty poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance, například `Show` nebo `Reset`. Zachytit odkaz na komponentu:

* Přidejte atribut [`@ref`](xref:mvc/views/razor#ref) pro podřízenou komponentu.
* Definujte pole stejného typu jako podřízená komponenta.

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

Při vykreslení komponenty je pole `_loginDialog` vyplněno instancí `MyLoginDialog` podřízená komponenta. Pak můžete vyvolat metody .NET v instanci komponenty.

> [!IMPORTANT]
> Proměnná `_loginDialog` je naplněna pouze po vykreslení komponenty a její výstup obsahuje prvek `MyLoginDialog`. Do tohoto okamžiku neexistuje žádný odkaz na. Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování komponenty, použijte [metody OnAfterRenderAsync nebo OnAfterRender](xref:blazor/lifecycle#after-component-render).

Při zachytávání odkazů na součásti použijte podobnou syntaxi pro [zachycení odkazů na prvky](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), není to funkce interoperability JavaScriptu. Odkazy na součásti nejsou předány kódu jazyka JavaScript&mdash;jsou používány pouze v kódu .NET.

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

Zaregistrujte `NotifierService` jako singletion:

* V Blazor WebAssembly Zaregistrujte službu v `Program.Main`:

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* V Blazor serveru Zaregistrujte službu v `Startup.ConfigureServices`:

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

K aktualizaci součásti použijte `NotifierService`:

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
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

## <a name="partial-class-support"></a>Podpora částečné třídy

Komponenty Razor jsou generovány jako částečné třídy. Komponenty Razor jsou vytvořeny některým z následujících přístupů:

* C#kód je definován v [`@code`](xref:mvc/views/razor#code) bloku pomocí značek HTML a kódu Razor v jednom souboru. šablony Blazor definují své komponenty Razor pomocí tohoto přístupu.
* C#kód je umístěn v souboru kódu na pozadí, který je definován jako částečná třída.

Následující příklad ukazuje výchozí komponentu `Counter` s blokem `@code` v aplikaci vygenerovanou šablonou Blazor. Značky HTML, kód Razor a C# kód jsou ve stejném souboru:

*Čítač. Razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

Komponentu `Counter` lze také vytvořit pomocí souboru kódu na pozadí s částečnou třídou:

*Čítač. Razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.Razor.cs*:

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

Podle potřeby přidejte všechny požadované obory názvů do souboru dílčí třídy. Mezi obvyklé obory názvů používané komponentami Razor patří:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>Zadat základní třídu

Direktivu [`@inherits`](xref:mvc/views/razor#inherits) lze použít k určení základní třídy pro komponentu. Následující příklad ukazuje, jak komponenta může dědit základní třídu, `BlazorRocksBase`, k poskytnutí vlastností a metod komponenty. Základní třída by měla být odvozena od `ComponentBase`.

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

## <a name="specify-an-attribute"></a>Zadat atribut

Atributy lze zadat v součástech Razor s direktivou [`@attribute`](xref:mvc/views/razor#attribute) . Následující příklad používá atribut `[Authorize]` pro třídu komponenty:

```razor
@page "/"
@attribute [Authorize]
```

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

Další informace naleznete v tématu <xref:mvc/views/razor>.

> [!WARNING]
> Některé atributy HTML, jako je například [Standard ARIA](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), nebudou fungovat správně, pokud je typ .NET `bool`. V těchto případech použijte místo `bool``string` typ.

## <a name="raw-html"></a>Nezpracovaný kód HTML

Řetězce jsou obvykle vykreslovány pomocí textových uzlů modelu DOM, což znamená, že všechny značky, které mohou obsahovat, se ignorují a považují se za text literálu. Chcete-li vykreslit nezpracovaný kód HTML, zabalte obsah HTML do `MarkupString` hodnoty. Hodnota je analyzována jako HTML nebo SVG a vložena do modelu DOM.

> [!WARNING]
> Vykreslování nezpracovaného HTML vytvořeného z jakéhokoli nedůvěryhodného zdroje je **bezpečnostní riziko** a mělo by se jim vyhnout!

Následující příklad ukazuje použití typu `MarkupString` pro přidání bloku statického obsahu HTML do vykresleného výstupu komponenty:

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
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
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
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

<p>Current count: @_currentCount</p>

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
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

Pro kaskádování více hodnot stejného typu v rámci stejného podstromu zadejte jedinečný `Name` řetězec pro každou `CascadingValue` komponentu a odpovídající `CascadingParameter`. V následujícím příkladu se dvě `CascadingValue` komponenty zanášejí do různých instancí `MyCascadingType` podle názvu:

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

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

Následující příklad ukazuje, jak určit hodnoty `RenderFragment` a `RenderFragment<T>` a vykreslit šablony přímo v komponentě. Fragmenty vykreslování mohou být také předány jako argumenty [součástem šablon](xref:blazor/templated-components).

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

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

## <a name="additional-resources"></a>Další zdroje

* <xref:security/blazor/server> &ndash; obsahuje pokyny k vytváření Blazor serverových aplikací, které se musí soupeří s vyčerpáním prostředků.
