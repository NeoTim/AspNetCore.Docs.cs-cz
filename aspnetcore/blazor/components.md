---
title: Vytváření a používání ASP.NET Corech komponent Razor
author: guardrex
description: Naučte se vytvářet a používat komponenty Razor, včetně toho, jak navazovat na data, zpracovávat události a spravovat životní cykly komponent.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: a9ae84c36716bfc07ae3cf86214e48ad24770401
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205953"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Vytváření a používání ASP.NET Corech komponent Razor

Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Tobias Bartsch](https://www.aveo-solutions.com/)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Blazoraplikace jsou sestavené pomocí *komponent*. Součást je samostatně obsažený blok uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář. Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vkládání dat nebo reakci na události uživatelského rozhraní. Komponenty jsou flexibilní a odlehčené. Můžou být vnořené, opakovaně používané a sdílené mezi projekty.

## <a name="component-classes"></a>Třídy komponent

Komponenty jsou implementovány v souborech komponenty [Razor](xref:mvc/views/razor) (*. Razor*) pomocí kombinace kódu jazyka C# a HTML. Komponenta v Blazor je formálně označována jako *Komponenta Razor*.

Název součásti musí začínat velkým znakem. Například *MyCoolComponent. Razor* je platný a *MyCoolComponent. Razor* je neplatný.

Uživatelské rozhraní pro komponentu je definováno pomocí jazyka HTML. Dynamická logika vykreslování (například smyčky, podmíněné výrazy, výrazy) je přidána pomocí vložené syntaxe jazyka C# s názvem [Razor](xref:mvc/views/razor). Při kompilaci aplikace jsou značky HTML a vykreslování jazyka C# převedeny na třídu komponenty. Název generované třídy se shoduje s názvem souboru.

Členy třídy komponenty jsou definovány v `@code` bloku. V `@code` bloku je stav součásti (vlastnosti, pole) zadán pomocí metod pro zpracování událostí nebo pro definování jiné logiky komponent. Je přípustný více `@code` než jeden blok.

Členy součásti lze použít jako součást logiky vykreslování komponenty pomocí výrazů jazyka C#, které začínají na `@`. Například pole C# se vykreslí pomocí předpony `@` na název pole. Následující příklad vyhodnocuje a vykresluje:

* `_headingFontStyle`do hodnoty vlastnosti CSS pro `font-style`.
* `_headingText`k obsahu `<h1>` elementu.

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Po prvním vykreslení komponenty vygeneruje komponenta znovu svůj strom vykreslování v reakci na události. Blazorpak porovná nový strom vykreslování s předchozí a aplikuje všechny úpravy model DOM (Document Object Model) v prohlížeči (DOM).

Komponenty jsou běžné třídy jazyka C# a lze je umístit kamkoli v rámci projektu. Komponenty, které tvoří webové stránky, jsou obvykle umístěny ve složce *stránky* . Komponenty mimo stránku jsou často umístěny ve *sdílené* složce nebo vlastní složce přidané do projektu.

Obor názvů komponenty obvykle je odvozen z kořenového oboru názvů aplikace a umístění komponenty (složka) v rámci aplikace. Pokud je `BlazorApp` kořenový obor názvů aplikace a `Counter` součást se nachází ve složce *stránky* :

* Obor `Counter` názvů součásti je `BlazorApp.Pages`.
* Plně kvalifikovaný název typu komponenty je `BlazorApp.Pages.Counter`.

Další informace naleznete v části [Import komponent](#import-components) .

Chcete-li použít vlastní složku, přidejte obor názvů vlastní složky buď do nadřazené komponenty, nebo do souboru *_Imports. Razor* aplikace. Například následující obor názvů zpřístupňuje komponenty ve složce *Components* , když je `BlazorApp`kořenový obor názvů aplikace:

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a>Statické prostředky

Blazorpostupuje podle konvence ASP.NET Core aplikací, které umísťují statické prostředky do [složky webového kořenového adresáře (wwwroot)](xref:fundamentals/index#web-root)projektu.

Použijte základní cestu (`/`) pro odkaz na webový kořenový adresář pro statický prostředek. V následujícím příkladu je soubor *logo. png* fyzicky umístěný ve složce *{Project root}/wwwroot/images* :

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Komponenty Razor **nepodporují znak** tilda () s`~/`vlnovým lomítkem ().

Informace o nastavení základní cesty aplikace najdete v tématu <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="tag-helpers-arent-supported-in-components"></a>V součástech nejsou podporovány pomocníky značek.

V součástech Razor (soubory *. Razor* ) nejsou podporované [pomocníky značek](xref:mvc/views/tag-helpers/intro) . Chcete-li poskytnout funkci Blazor, jako je například Pomocník pro vytváření značek, vytvořte komponentu se stejnou funkcí jako pomocník značek a místo ní použijte komponentu.

## <a name="use-components"></a>Použití komponent

Komponenty mohou zahrnovat další komponenty deklarováním pomocí syntaxe elementu HTML. Označení pro použití komponenty vypadá jako značka HTML, kde název značky je typ komponenty.

Následující kód v *indexu. Razor* vykreslí `HeadingComponent` instanci:

```razor
<HeadingComponent />
```

*Components/HeadingComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Pokud komponenta obsahuje element HTML s velkým prvním písmenem, které neodpovídá názvu komponenty, je vygenerováno upozornění označující, že element má neočekávaný název. Přidání `@using` direktivy pro obor názvů součásti zpřístupňuje komponentu, která vyřeší upozornění.

## <a name="routing"></a>Směrování

Směrování do Blazor se dosahuje tím, že poskytuje šablonu směrování pro každou dostupnou součást aplikace.

Když je zkompilován soubor Razor s `@page` direktivou, vygenerovaná třída je dána <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadáním šablony trasy. V době běhu Směrovač vyhledává třídy komponent pomocí `RouteAttribute` a a vykreslí, že jakákoli součást má šablonu směrování, která odpovídá požadované adrese URL.

```razor
@page "/ParentComponent"

...
```

Další informace naleznete v tématu <xref:blazor/routing>.

## <a name="parameters"></a>Parametry

### <a name="route-parameters"></a>Parametry směrování

Komponenty mohou přijímat parametry směrování z šablony směrování uvedené v `@page` direktivě. Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponent.

*Stránky/RouteParameter. Razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Volitelné parametry nejsou podporovány, takže v `@page` předchozím příkladu jsou aplikovány dvě direktivy. První umožňuje navigaci na součást bez parametru. Druhá `@page` direktiva přijme parametr `{text}` Route a přiřadí hodnotu `Text` vlastnosti.

*Catch-All* Parameter Syntax (`*`/`**`), která zachycuje cestu mezi více hranicemi složek, není v součástech Razor (*. Razor* **) podporován.**

### <a name="component-parameters"></a>Parametry součásti

Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí veřejných vlastností třídy komponenty s `[Parameter]` atributem. Použijte atributy k určení argumentů pro komponentu v kódu.

*Components/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

V následujícím příkladu z ukázkové aplikace `ParentComponent` nastaví hodnotu `Title` vlastnosti. `ChildComponent`

*Stránky/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Nevytvářejte komponenty, které zapisují do vlastních *parametrů komponenty*, místo toho použijte soukromé pole. Další informace naleznete v části [Nevytvářet komponenty, které jsou zapsány do jejich vlastních vlastností parametrů](#dont-create-components-that-write-to-their-own-parameter-properties) .

## <a name="child-content"></a>Podřízený obsah

Komponenty mohou nastavit obsah jiné součásti. Součást přiřazení poskytuje obsah mezi značkami, které určují přijímací komponentu.

V následujícím příkladu `ChildComponent` má `ChildContent` vlastnost, která představuje `RenderFragment`, který představuje segment uživatelského rozhraní pro vykreslení. Hodnota `ChildContent` je umístěna v označení komponenty, kde má být obsah vykreslen. Hodnota `ChildContent` je přijímána z nadřazené komponenty a vykreslena v panelu Bootstrap `panel-body`.

*Components/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Vlastnost, která přijímá `RenderFragment` obsah, musí být `ChildContent` pojmenována podle konvence.

`ParentComponent` V ukázkové aplikaci může poskytovat obsah pro vykreslování `ChildComponent` umístěním obsahu uvnitř `<ChildComponent>` značek.

*Stránky/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Seskupováním atributů a libovolné parametry

Komponenty mohou kromě deklarovaných parametrů komponenty zachytit a vykreslovat další atributy. Další atributy mohou být zachyceny ve slovníku a poté *splatted* na prvek při vykreslení komponenty pomocí direktivy [`@attributes`](xref:mvc/views/razor#attributes) Razor. Tento scénář je užitečný při definování komponenty, která vytváří prvek značky, který podporuje nejrůznější přizpůsobení. Například může být zdlouhavé definovat atributy samostatně pro objekt `<input>` , který podporuje mnoho parametrů.

V následujícím `<input>` příkladu první prvek (`id="useIndividualParams"`) používá jednotlivé parametry komponenty, zatímco druhý `<input>` element (`id="useAttributesDict"`) používá atribut seskupováním:

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

Typ parametru musí být implementován `IEnumerable<KeyValuePair<string, object>>` pomocí řetězcových klíčů. Použití `IReadOnlyDictionary<string, object>` je také možností v tomto scénáři.

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

Chcete-li přijmout libovolné atributy, definujte parametr komponenty pomocí `[Parameter]` atributu s `CaptureUnmatchedValues` vlastností nastavenou na `true`:

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

`CaptureUnmatchedValues` Vlastnost on `[Parameter]` umožňuje, aby parametr odpovídal všem atributům, které se neshodují s žádným jiným parametrem. Komponenta může definovat pouze jeden parametr s `CaptureUnmatchedValues`. Typ vlastnosti používaný pomocí `CaptureUnmatchedValues` musí být přiřazovatelné z `Dictionary<string, object>` řetězcových klíčů. `IEnumerable<KeyValuePair<string, object>>`nebo `IReadOnlyDictionary<string, object>` jsou také možnosti v tomto scénáři.

Pozice `@attributes` relativní vzhledem k poloze atributů elementu je důležitá. Když `@attributes` jsou splatted na elementu, atributy jsou zpracovávány zprava doleva (poslední až první). Vezměte v úvahu následující příklad komponenty, která využívá `Child` komponentu:

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

Atribut komponenty je nastaven na hodnotu napravo od `@attributes` `Child` `extra` Vykreslená `Parent` `<div>` komponenta obsahuje `extra="5"` při předání prostřednictvím dodatečného atributu, protože atributy jsou zpracovávány zprava doleva (poslední až první):

```html
<div extra="5" />
```

V následujícím příkladu je `extra` pořadí a `@attributes` obrácené v `Child` komponentě: `<div>`

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

Vykreslená `<div>` `Parent` komponenta obsahuje `extra="10"` , když je předána pomocí dalšího atributu:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Zachytit odkazy na komponenty

Odkazy na komponenty poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance, například `Show` nebo `Reset`. Zachytit odkaz na komponentu:

* Přidejte [`@ref`](xref:mvc/views/razor#ref) atribut do podřízené součásti.
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

Při vykreslení komponenty je `_loginDialog` pole vyplněno instancí `MyLoginDialog` podřízené součásti. Pak můžete vyvolat metody .NET v instanci komponenty.

> [!IMPORTANT]
> `_loginDialog` Proměnná je naplněna pouze po vykreslení komponenty a její výstup obsahuje `MyLoginDialog` element. Do tohoto okamžiku neexistuje žádný odkaz na. Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování komponenty, použijte [metody OnAfterRenderAsync nebo OnAfterRender](xref:blazor/lifecycle#after-component-render).

Chcete-li odkazovat na součásti ve smyčce, viz [zachytit odkazy na více podobných podřízených komponent (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Při zachytávání odkazů na součásti použijte podobnou syntaxi pro [zachycení odkazů na prvky](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), není to funkce interoperability JavaScriptu. Odkazy na součásti nejsou předány kódu&mdash;jazyka JavaScript, které jsou používány pouze v kódu .NET.

> [!NOTE]
> Nepoužívejte odkazy na součásti **pro použití stavu** podřízených komponent. Místo toho použijte k předání dat podřízeným komponentám běžné deklarativní parametry. Použití běžných deklarativních parametrů má za následek podřízené komponenty, které jsou automaticky revykreslovány ve správný čas.

## <a name="invoke-component-methods-externally-to-update-state"></a>Vyvolat metody komponenty externě na stav aktualizace

Blazorpoužívá `SynchronizationContext` k vykonání jediného logického vlákna provádění. [Metody životního cyklu](xref:blazor/lifecycle) komponenty a všechna zpětná volání událostí, která jsou Blazor aktivována, jsou provedena `SynchronizationContext`na tomto. V případě, že komponenta musí být aktualizována na základě externí události, jako je například časovač nebo jiné oznámení, použijte `InvokeAsync` metodu, která bude zaslána do. Blazor `SynchronizationContext`

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

* V Blazor rámci služby WebAssembly Zaregistrujte `Program.Main`službu v:

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* V Blazor části Server Zaregistrujte službu `Startup.ConfigureServices`v nástroji:

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

`NotifierService` K aktualizaci součásti použijte.

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

`NotifierService` V předchozím příkladu `OnNotify` vyvolá metodu komponenty mimo Blazor'. `SynchronizationContext` `InvokeAsync`slouží k přepnutí do správného kontextu a vykreslení vykreslování do fronty.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Použití \@klíče k řízení uchovávání prvků a komponent

Při vykreslování seznamu prvků nebo komponent a následné změny prvků nebo komponent se musí rozhodnout, Blazorkterý z předchozích prvků nebo komponent může být zachován a jak se mají objekty modelu namapovat. Obvykle je tento proces automatický a může být ignorován, ale existují případy, kdy můžete chtít řídit proces.

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

Obsah `People` kolekce se může změnit vloženými, odstraněnými nebo znovu seřazenými položkami. Když se komponenta znovu vykreslí, může `<DetailsEditor>` se tato součást změnit, aby `Details` přijímala jiné hodnoty parametrů. To může způsobit složitější revykreslování, než se očekávalo. V některých případech může reprodukce vést k viditelným rozdílům v chování, jako je například ztracený fokus elementu.

Proces mapování lze řídit pomocí atributu [`@key`](xref:mvc/views/razor#key) direktiva. `@key`způsobí, že rozdílový algoritmus garantuje zachování prvků nebo komponent na základě hodnoty klíče:

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

* Pokud `Person` se ze `People` seznamu odstraní, z uživatelského rozhraní se odebere `<DetailsEditor>` jenom odpovídající instance. Ostatní instance zůstanou beze změny.
* Pokud `Person` je vložena na nějaké místo v seznamu, je do příslušné `<DetailsEditor>` pozice vložena jedna nová instance. Ostatní instance zůstanou beze změny.
* Pokud `Person` jsou položky znovu seřazeny, odpovídající `<DetailsEditor>` instance jsou zachovány a znovu uspořádány v uživatelském rozhraní.

V některých scénářích použití `@key` minimalizuje složitost reprodukce a vyhne se potenciálním problémům se stavovou částí modelu DOM, jako je například pozice fokusu.

> [!IMPORTANT]
> Klíče jsou místní pro každý prvek kontejneru nebo komponentu. Klíče nejsou v dokumentu globálně porovnány.

### <a name="when-to-use-key"></a>Kdy použít \@klíč

Obvykle má smysl použít `@key` při každém vykreslení seznamu (například v `@foreach` bloku) a existuje vhodná hodnota pro definování. `@key`

Můžete také použít `@key` k zabránění Blazor v zachování prvku nebo podstromu komponenty při změně objektu:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Pokud `@currentPerson` se změní, `@key` direktiva Attribute Blazor vynutí zrušení celého `<div>` a jeho následníků a znovu sestaví podstrom v uživatelském rozhraní novými prvky a komponentami. To může být užitečné, pokud potřebujete zaručit, že se při `@currentPerson` změnách nezachovají stav uživatelského rozhraní.

### <a name="when-not-to-use-key"></a>Kdy nepoužívat \@klíč

Při rozdílech s se `@key`účtují náklady na výkon. Náklady na výkon nejsou velké, ale zadávejte `@key` jenom v případě, že řízení pravidel uchovávání prvků nebo součástí má aplikace výhodu.

I v `@key` případě, že Blazor není použit, zachová podřízený element a instance komponenty co nejvíce. Jedinou výhodou použití `@key` je kontrola, *jak* jsou instance modelů mapovány na zachované instance komponent namísto rozdílového algoritmu výběru mapování.

### <a name="what-values-to-use-for-key"></a>Jaké hodnoty se mají použít \@pro klíč

Obecně dává smysl pro zadání jednoho z následujících typů hodnot pro `@key`:

* Instance objektů modelu (například `Person` instance jako v předchozím příkladu). To zajišťuje zachování v závislosti na rovnosti odkazů na objekty.
* Jedinečné identifikátory (například hodnoty primárního klíče typu `int`, `string`nebo `Guid`).

Zajistěte, aby `@key` hodnoty použité pro nekolidovat. Pokud jsou v rámci stejného nadřazeného prvku zjištěny hodnoty konfliktu, Blazor vyvolá výjimku, protože nemůže deterministické namapovat staré prvky nebo součásti na nové prvky nebo komponenty. Používejte pouze jedinečné hodnoty, například instance objektů nebo hodnoty primárního klíče.

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a>Nevytvářejte komponenty, které zapisují do vlastností vlastního parametru.

Parametry jsou přepsány za následující podmínky:

* Obsah podřízené komponenty je vykreslen pomocí `RenderFragment`.
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>je volána v nadřazené komponentě.

Parametry jsou resetovány, protože nadřazená komponenta je znovu <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> vygenerována, když je volána, a podřízené součásti jsou zadány nové hodnoty parametrů.

Vezměte v úvahu `Expander` následující komponentu:

* Vykreslí podřízený obsah.
* Přepíná zobrazení podřízeného obsahu s parametrem součásti.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
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

`Expander` Komponenta je přidána do nadřazené komponenty, která může zavolat `StateHasChanged`:

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

Zpočátku se `Expander` komponenty chovají nezávisle při přepínání `Expanded` jejich vlastností. Podřízené komponenty udržují své stavy podle očekávání. Když `StateHasChanged` je volána v nadřazeném prvku, `Expanded` parametr první podřízené komponenty se obnoví zpět na původní hodnotu (`true`). `Expanded` Hodnota druhé `Expander` součásti není resetována, protože ve druhé komponentě není vykreslen žádný podřízený obsah.

Chcete-li zachovat stav v předchozím scénáři, použijte *soukromé pole* v `Expander` součásti k údržbě jeho přepnutého stavu.

Následující `Expander` součást:

* Akceptuje `Expanded` hodnotu parametru komponenty z nadřazené položky.
* Přiřadí hodnotu parametru komponenty k *privátnímu poli* (`_expanded`) v [události s inicializací](xref:blazor/lifecycle#component-initialization-methods).
* Pomocí soukromého pole udržuje stav interního přepínání.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @_expanded)

    @if (_expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool _expanded;

    protected override void OnInitialized()
    {
        _expanded = Expanded;
    }

    private void Toggle()
    {
        _expanded = !_expanded;
    }
}
```

## <a name="partial-class-support"></a>Podpora částečné třídy

Komponenty Razor jsou generovány jako částečné třídy. Komponenty Razor jsou vytvořeny některým z následujících přístupů:

* Kód jazyka C# je definován v [`@code`](xref:mvc/views/razor#code) bloku s označením HTML a kódem Razor v jednom souboru. Blazoršablony definují své komponenty Razor pomocí tohoto přístupu.
* Kód jazyka C# je umístěn v souboru kódu na pozadí, který je definován jako částečná třída.

Následující příklad ukazuje výchozí `Counter` komponentu s `@code` blokem v aplikaci vygenerovanou ze Blazor šablony. HTML značky, kód Razor a kód jazyka C# jsou ve stejném souboru:

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

`Counter` Komponentu lze také vytvořit pomocí souboru kódu na pozadí s částečnou třídou:

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

[`@inherits`](xref:mvc/views/razor#inherits) Direktiva se dá použít k určení základní třídy pro komponentu. Následující příklad ukazuje, jak komponenta může dědit základní třídu, `BlazorRocksBase`k poskytnutí vlastností a metod komponenty. Základní třída by měla být odvozena od `ComponentBase`třídy.

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

Atributy lze zadat v součástech Razor s [`@attribute`](xref:mvc/views/razor#attribute) direktivou. Následující příklad používá `[Authorize]` atribut pro třídu komponenty:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>Importovat součásti

Obor názvů komponenty, která je vytvořená pomocí Razor, je založen na (v pořadí podle priority):

* [`@namespace`](xref:mvc/views/razor#namespace)označení v souboru Razor (*. Razor*) značky (`@namespace BlazorSample.MyNamespace`).
* Projekt `RootNamespace` v souboru projektu (`<RootNamespace>BlazorSample</RootNamespace>`).
* Název projektu, pořízený z názvu souboru projektu (*. csproj*) a cesta z kořenového adresáře projektu ke komponentě. Rozhraní například překládá *{root]/pages/index.Razor* (*BlazorSample. csproj*) na obor názvů `BlazorSample.Pages`. Komponenty následují pravidla vazeb názvů C#. Pro `Index` komponentu v tomto příkladu komponenty v oboru jsou všechny komponenty:
  * Ve stejné složce *stránky*.
  * Komponenty v kořenu projektu, které explicitně neurčují jiný obor názvů.

Komponenty definované v jiném oboru názvů se přenesou do rozsahu pomocí [`@using`](xref:mvc/views/razor#using) direktivy Razor.

Pokud jiná komponenta, `NavMenu.razor`existuje ve složce *BlazorSample/Shared/* Folder, lze komponentu použít v `Index.razor` příkazu s následujícím `@using` příkazem:

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Na [`@using`](xref:mvc/views/razor#using) součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, které nevyžadují direktivu:

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> `global::` Kvalifikace není podporovaná.
>
> Import komponent s `using` příkazy s aliasem (například `@using Foo = Bar`) není podporován.
>
> Částečně kvalifikované názvy nejsou podporovány. Například přidání `@using BlazorSample` a odkazování `NavMenu.razor` pomocí `<Shared.NavMenu></Shared.NavMenu>` není podporováno.

## <a name="conditional-html-element-attributes"></a>Podmíněné atributy elementu HTML

Atributy elementu HTML jsou podmíněně vykresleny na základě hodnoty .NET. Pokud je `false` hodnota nebo `null`, atribut není vykreslen. Pokud je `true`hodnota, je vygenerována hodnota atributu minimalizovaný.

V následujícím příkladu určuje, `IsCompleted` zda `checked` je vykreslena v kódu elementu:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Pokud `IsCompleted` je `true`, zaškrtávací políčko je vykresleno jako:

```html
<input type="checkbox" checked />
```

Pokud `IsCompleted` je `false`, zaškrtávací políčko je vykresleno jako:

```html
<input type="checkbox" />
```

Další informace naleznete v tématu <xref:mvc/views/razor>.

> [!WARNING]
> Některé atributy HTML, jako je například [Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), nefungují správně, pokud je typ .NET `bool`. V těchto případech použijte `string` typ místo typu. `bool`

## <a name="raw-html"></a>Nezpracovaný kód HTML

Řetězce jsou obvykle vykreslovány pomocí textových uzlů modelu DOM, což znamená, že všechny značky, které mohou obsahovat, se ignorují a považují se za text literálu. Chcete-li vykreslit nezpracovaný kód HTML, zabalte obsah HTML do `MarkupString` hodnoty. Hodnota je analyzována jako HTML nebo SVG a vložena do modelu DOM.

> [!WARNING]
> Vykreslování nezpracovaného HTML vytvořeného z jakéhokoli nedůvěryhodného zdroje je **bezpečnostní riziko** a mělo by se jim vyhnout!

Následující příklad ukazuje použití `MarkupString` typu pro přidání bloku statického obsahu HTML do vykresleného výstupu komponenty:

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

V následujícím příkladu z ukázkové aplikace `ThemeInfo` třída určuje informace o motivu pro přetečení hierarchie komponent tak, aby všechna tlačítka v dané části aplikace sdílela stejný styl.

*UIThemeClasses/ThemeInfo. cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Komponenta předchůdce může poskytnout kaskádovou hodnotu pomocí komponenty kaskádová hodnota. `CascadingValue` Komponenta zabalí podstrom hierarchie komponent a poskytne jednu hodnotu všem součástem v rámci daného podstromu.

Například ukázková aplikace určuje informace o motivu (`ThemeInfo`) v jednom z rozložení aplikace jako kaskádový parametr pro všechny komponenty, které tvoří tělo `@Body` vlastnosti. `ButtonClass`má přiřazenou hodnotu `btn-success` v součásti rozložení. Každá odvozená komponenta může tuto vlastnost spotřebovat `ThemeInfo` prostřednictvím kaskádového objektu.

`CascadingValuesParametersLayout`část

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

Chcete-li použít kaskádové hodnoty, komponenty deklaruje kaskádové parametry pomocí `[CascadingParameter]` atributu. Kaskádové hodnoty jsou vázány na kaskádové parametry podle typu.

V ukázkové aplikaci váže `CascadingValuesParametersTheme` komponenta `ThemeInfo` kaskádovou hodnotu k kaskádovým parametrům. Parametr slouží k nastavení třídy CSS pro jedno z tlačítek zobrazených komponentou.

`CascadingValuesParametersTheme`část

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

Pro kaskádování více hodnot stejného typu v rámci stejného podstromu zadejte jedinečný `Name` řetězec pro každou `CascadingValue` součást a odpovídající. `CascadingParameter` V následujícím příkladu jsou dvě `CascadingValue` komponenty kaskádovitě různé instance `MyCascadingType` podle názvu:

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

Ukázková aplikace má `ITab` rozhraní, které implementuje karty:

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

`CascadingValuesParametersTabSet` Komponenta používá `TabSet` komponentu, která obsahuje několik `Tab` komponent:

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

Podřízené `Tab` součásti nejsou explicitně předány jako parametry do `TabSet`. Místo toho jsou podřízené `Tab` součásti součástí podřízeného obsahu `TabSet`. `TabSet` Pořád ale potřebuje znát každou `Tab` komponentu, aby mohla vykreslovat hlavičky a aktivní kartu. Chcete-li povolit tuto koordinaci bez nutnosti dalšího kódu `TabSet` , komponenta *může poskytnout sebe sama jako kaskádovou hodnotu* , která je následně vyzvednuta `Tab` podřízenými součástmi.

`TabSet`část

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Podřízené komponenty `Tab` zachytí objekt obsahující `TabSet` jako kaskádový parametr, takže `Tab` komponenty přidávají sebe sama do souřadnice `TabSet` a, na které karty jsou aktivní.

`Tab`část

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Šablony Razor

Fragmenty vykreslování lze definovat pomocí syntaxe šablony Razor. Šablony Razor představují způsob, jak definovat fragment uživatelského rozhraní a předpokládat následující formát:

```razor
@<{HTML tag}>...</{HTML tag}>
```

Následující příklad ukazuje, jak zadat `RenderFragment` a `RenderFragment<T>` hodnoty a vykreslit šablony přímo v součásti. Fragmenty vykreslování mohou být také předány jako argumenty [součástem šablon](xref:blazor/templated-components).

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

Vzhledem Blazor k `<img>` tomu, že vykresluje HTML, obrázky podporované prohlížečem, včetně obrázků SVG (Scalable Vector Graphics) (*. SVG*), jsou podporovány prostřednictvím značky:

```html
<img alt="Example image" src="some-image.svg" />
```

Podobně jsou obrázky SVG podporovány v pravidlech CSS souboru šablony stylů (*. CSS*):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Vložené značky SVG se však ve všech scénářích nepodporují. Pokud `<svg>` značku přímo umístíte do souboru komponenty (*. Razor*), podporuje se základní vykreslování obrázků, ale mnoho pokročilých scénářů ještě není podporováno. Například `<use>` značky nejsou aktuálně dodržovány a `@bind` nelze je použít s některými značkami SVG. Očekáváme, že tato omezení vyřešíme v budoucí verzi.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/blazor/server/threat-mitigation>&ndash; Obsahuje doprovodné materiály k Blazor vytváření serverových aplikací, které se musí soupeří s vyčerpáním prostředků.
