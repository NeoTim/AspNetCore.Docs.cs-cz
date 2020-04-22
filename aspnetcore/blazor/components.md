---
title: Vytvářejte a používejte komponenty ASP.NET Core Razor
author: guardrex
description: Naučte se vytvářet a používat komponenty Razor, včetně toho, jak svázat s daty, zpracovávat události a spravovat životní cykly komponent.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: 4434636992cb2506ef6525996690946f97c43764
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791491"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Vytvářejte a používejte komponenty ASP.NET Core Razor

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)a [Tobias Bartsch](https://www.aveo-solutions.com/)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Blazoraplikace jsou vytvořeny pomocí *komponent*. Komponenta je samostatný blok uživatelského rozhraní (UI), například stránka, dialog nebo formulář. Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vložení dat nebo reakci na události nového jazyka. Komponenty jsou flexibilní a lehké. Lze je vnořit, znovu použít a sdílet mezi projekty.

## <a name="component-classes"></a>Třídy komponent

Komponenty jsou implementovány v souborech komponent [Razor](xref:mvc/views/razor) (*.razor*) pomocí kombinace značek C# a HTML. Součást in Blazor je formálně označována jako *součást Razor*.

Název komponenty musí začínat velkým znakem. Například *MyCoolComponent.razor* je platný a *myCoolComponent.razor* je neplatný.

UI pro komponentu je definováno pomocí HTML. Logika dynamického vykreslování (například smyčky, podmínky, výrazy) je přidána pomocí vložené syntaxe jazyka C# s názvem [Razor](xref:mvc/views/razor). Když je aplikace zkompilován, kódy HTML a logika vykreslování Jazyka C# jsou převedeny na třídu komponenty. Název generované třídy odpovídá názvu souboru.

Členové třídy komponent jsou `@code` definováni v bloku. V `@code` bloku je stav komponenty (vlastnosti, pole) určen metodami pro zpracování událostí nebo pro definování jiné logiky komponenty. Je přípustné `@code` více než jeden blok.

Členy komponenty lze použít jako součást logiky vykreslování komponenty pomocí výrazů Jazyka C#, které začínají písmenem `@`. Například c# pole je vykreslen předponou `@` na název pole. Následující příklad vyhodnotí a vykreslí:

* `_headingFontStyle`na hodnotu vlastnosti `font-style`CSS pro .
* `_headingText`k obsahu `<h1>` prvku.

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Po zpočátku vykreslování komponenty komponenty regeneruje svůj strom vykreslení v reakci na události. Blazorpotom porovná nový strom vykreslení s předchozím stromem a použije všechny změny v objektovém modelu dokumentu prohlížeče (DOM).

Součásti jsou běžné třídy Jazyka C# a lze umístit kdekoli v rámci projektu. Součásti, které vytvářejí webové stránky, jsou obvykle umístěny ve složce *Stránky.* Nestránkové součásti jsou často umístěny ve *sdílené* složce nebo ve vlastní složce přidané do projektu.

Obor názvů komponenty je obvykle odvozen od kořenového oboru názvů aplikace a umístění (složky) komponenty v rámci aplikace. Pokud je `BlazorApp` kořenový obor názvů `Counter` aplikace a komponenta se nachází ve složce *Stránky:*

* Obor `Counter` názvů komponenty `BlazorApp.Pages`je .
* Plně kvalifikovaný název typu součásti je `BlazorApp.Pages.Counter`.

Další informace naleznete v části [Importovat součásti.](#import-components)

Chcete-li použít vlastní složku, přidejte obor názvů vlastní složky do nadřazené součásti nebo do souboru *_Imports.razor* aplikace. Například následující obor názvů zpřístupňuje součásti ve složce *Komponenty,* `BlazorApp`pokud je kořenový obor názvů aplikace :

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a>Statické datové zdroje

Blazorřídí se konvencí aplikací ASP.NET Core, které umísťují statické datové zdroje pod [složku kořenového adresáře webu (wwwroot) projektu](xref:fundamentals/index#web-root).

Použijte cestu relativní k`/`základně ( ) k odkazování na kořen webu pro statický datový zdroj. V následujícím příkladu je *soubor logo.png* fyzicky umístěn ve složce *{PROJECT ROOT}/wwwroot/images:*

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Komponenty břitvy **nepodporují** zápis tildového lomítka (`~/`).

Informace o nastavení základní cesty aplikace <xref:host-and-deploy/blazor/index#app-base-path>naleznete v tématu .

## <a name="tag-helpers-arent-supported-in-components"></a>Pomocné spoje nejsou v součástech podporovány

[Tag Helpers](xref:mvc/views/tag-helpers/intro) nejsou podporovány v razor komponenty *(.razor* soubory). Chcete-li v části Poskytnout Blazorfunkci podobné funkci pomocné značce , vytvořte komponentu se stejnou funkcí jako pomocník značek a místo toho použijte komponentu.

## <a name="use-components"></a>Použití součástí

Součásti mohou obsahovat další součásti deklarováním pomocí syntaxe elementu HTML. Značky pro použití komponenty vypadají jako značka HTML, kde je název značky typem komponenty.

Následující značky v *Index.razor* `HeadingComponent` vykreslí instanci:

```razor
<HeadingComponent />
```

*Komponenty/HeadingComponent.holicí strojek*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Pokud komponenta obsahuje element HTML s velkým prvním písmenem, které neodpovídá názvu komponenty, je vyzařováno upozornění označující, že prvek má neočekávaný název. Přidání `@using` směrnice pro obor názvů komponenty zpřístupní součást, která řeší upozornění.

## <a name="routing"></a>Směrování

Směrování Blazor v je dosaženo poskytnutím šablony trasy pro každou přístupnou součást v aplikaci.

Při kompilaci souboru Razor se `@page` směrnicí je vygenerovaná třída přidělena <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> určující šablonu trasy. Za běhu směrovač vyhledá třídy komponent `RouteAttribute` s a vykreslí, která komponenta má šablonu trasy, která odpovídá požadované adrese URL.

```razor
@page "/ParentComponent"

...
```

Další informace naleznete v tématu <xref:blazor/routing>.

## <a name="parameters"></a>Parametry

### <a name="route-parameters"></a>Parametry trasy

Komponenty mohou přijímat parametry postupu ze šablony `@page` postupu uvedené ve směrnici. Směrovač používá parametry trasy k naplnění odpovídajících parametrů komponenty.

*Stránky/RouteParameter.holicí strojek*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Volitelné parametry nejsou podporovány, `@page` takže v předchozím příkladu jsou použity dvě direktivy. První umožňuje navigaci do komponenty bez parametru. Druhá `@page` směrnice obdrží `{text}` parametr trasy a přiřadí `Text` hodnotu vlastnosti.

*Syntaxe parametrů catch-all* `*`/`**`( ), která zachycuje cestu přes hranice více složek, **není** podporována v komponentách Razor (*.razor*).

### <a name="component-parameters"></a>Parametry komponenty

Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí veřejných vlastností třídy komponenty s atributem. `[Parameter]` Pomocí atributů můžete zadat argumenty pro komponentu ve značkách.

*Komponenty/ChildComponent.holicí strojek*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

V následujícím příkladu z ukázkové aplikace nastaví `ParentComponent` hodnotu `Title` vlastnosti `ChildComponent`.

*Stránky/ParentComponent.razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Nevytvářejte součásti, které zapisují do *vlastních parametrů komponenty*, použijte místo toho soukromé pole. Další informace naleznete v části [Nevytvářet součásti, které zapisují do vlastních vlastností parametrů.](#dont-create-components-that-write-to-their-own-parameter-properties)

## <a name="child-content"></a>Podřízený obsah

Součásti mohou nastavit obsah jiné součásti. Přiřazující komponenta poskytuje obsah mezi značkami, které určují přijímající komponentu.

V následujícím příkladu `ChildComponent` má `ChildContent` vlastnost, `RenderFragment`která představuje , který představuje segment uI k vykreslení. Hodnota `ChildContent` je umístěna ve značkách komponenty, kde by měl být obsah vykreslen. Hodnota `ChildContent` je přijata z nadřazené součásti a `panel-body`vykreslena uvnitř panelu Bootstrap .

*Komponenty/ChildComponent.holicí strojek*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Vlastnost přijímající `RenderFragment` obsah musí `ChildContent` být pojmenována podle konvence.

V `ParentComponent` ukázkové aplikaci můžete poskytnout `ChildComponent` obsah pro vykreslování umístěním obsahu uvnitř `<ChildComponent>` značek.

*Stránky/ParentComponent.razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Atribut splatting a libovolné parametry

Součásti mohou zachytit a vykreslit další atributy kromě deklarovaných parametrů komponenty. Další atributy mohou být zachyceny ve slovníku a potom *přetavovány* na [`@attributes`](xref:mvc/views/razor#attributes) prvek při vykreslení komponenty pomocí direktivy Razor. Tento scénář je užitečné při definování součásti, která vytváří prvek značky, který podporuje různé vlastní nastavení. Například může být únavné definovat atributy samostatně pro `<input>` který podporuje mnoho parametrů.

V následujícím příkladu `<input>` první`id="useIndividualParams"`prvek ( ) používá parametry `<input>` jednotlivých komponent, zatímco druhý prvek (`id="useAttributesDict"`) používá atribut splatting:

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

Typ parametru musí `IEnumerable<KeyValuePair<string, object>>` implementovat s řetězcovými klíči. Použití `IReadOnlyDictionary<string, object>` je také možnost v tomto scénáři.

Vykreslené `<input>` prvky pomocí obou přístupů je identická:

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

Chcete-li přijmout libovolné atributy, definujte parametr komponenty pomocí atributu `[Parameter]` s vlastností nastavenou `CaptureUnmatchedValues` na `true`:

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

Vlastnost `CaptureUnmatchedValues` na `[Parameter]` umožňuje parametr, aby odpovídaly všechny atributy, které neodpovídají žádné jiné parametr. Komponenta může definovat pouze `CaptureUnmatchedValues`jeden parametr s . Typ vlastnosti, `CaptureUnmatchedValues` který se `Dictionary<string, object>` používá s musí být přiřaditelné z s řetězci klíče. `IEnumerable<KeyValuePair<string, object>>`nebo `IReadOnlyDictionary<string, object>` jsou také možnosti v tomto scénáři.

Pozice vzhledem `@attributes` k pozici atributů prvku je důležitá. Když `@attributes` jsou štěkat na prvek, atributy jsou zpracovány zprava doleva (poslední k prvnímu). Zvažte následující příklad součásti, `Child` která spotřebovává komponentu:

*ParentComponent.razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent.holicí strojek*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Atribut `Child` komponenty `extra` je nastaven na `@attributes`pravo . Vykreslená `Parent` komponenta `<div>` obsahuje `extra="5"` při průchodu další atribut, protože atributy jsou zpracovány zprava doleva (od poslední k první):

```html
<div extra="5" />
```

V následujícím příkladu je `extra` `@attributes` pořadí a `Child` je obráceno v komponentě `<div>`:

*ParentComponent.razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent.holicí strojek*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Vykreslenv `<div>` komponenty `Parent` obsahuje `extra="10"` při průchodu další atribut:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Zachycení odkazů na součásti

Odkazy na komponenty umožňují odkazovat na instanci komponenty, takže `Show` můžete `Reset`vydávat příkazy k této instanci, například nebo . Chcete-li zachytit odkaz na komponentu:

* Přidejte [`@ref`](xref:mvc/views/razor#ref) atribut do podřízené součásti.
* Definujte pole se stejným typem jako podřízená komponenta.

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

Při vykreslení komponenty `_loginDialog` je pole naplněno `MyLoginDialog` instancí podřízené součásti. Potom můžete vyvolat metody .NET na instanci komponenty.

> [!IMPORTANT]
> Proměnná `_loginDialog` je naplněna pouze po vykreslení komponenty `MyLoginDialog` a její výstup obsahuje prvek. Do té doby není na co se odkazovat. Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování součásti, použijte [metody OnAfterRenderAsync nebo OnAfterRender](xref:blazor/lifecycle#after-component-render).

Chcete-li odkazovat na součásti ve smyčce, viz [Zachycení odkazů na více podobných podřízených součástí (dotnet/aspnetcore #13358).](https://github.com/dotnet/aspnetcore/issues/13358)

Při zachytávání odkazů na komponenty používá podobnou syntaxi k [zachytávání odkazů na elementy](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), nejedná se o funkci interop javascriptu. Odkazy na komponenty nejsou předány&mdash;kódu JavaScriptu, které se používají pouze v kódu .NET.

> [!NOTE]
> **Nepoužívejte** odkazy na komponenty k mutu stavu podřízených součástí. Místo toho použijte normální deklarativní parametry k předání dat podřízeným součástem. Použití normálních deklarativních parametrů má za následek podřízené součásti, které se automaticky vykreslují ve správný čas.

## <a name="invoke-component-methods-externally-to-update-state"></a>Vyvolat metody komponenty externě aktualizovat stav

Blazorpoužívá `SynchronizationContext` k vynucení jednoho logického vlákna provádění. Metody životního [cyklu komponenty](xref:blazor/lifecycle) a všechna zpětná volání Blazor událostí, která `SynchronizationContext`jsou vyvolána, jsou v tomto spuštěny . V případě, že komponenta musí být aktualizována na základě externí události, `InvokeAsync` jako je například časovač nebo jiná oznámení, použijte metodu, která bude odeslána do Blazoraplikace `SynchronizationContext`.

Zvažte například *službu oznamovatele,* která může upozornit jakoukoli součást naslouchání aktualizovaného stavu:

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

* Ve Blazor službě WebAssembly zaregistrujte službu v `Program.Main`:

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* V Blazor části Server zaregistrujte službu v `Startup.ConfigureServices`:

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

Použijte `NotifierService` k aktualizaci komponenty:

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

V předchozím příkladu `NotifierService` vyvolá `OnNotify` metodu komponenty Blazormimo `SynchronizationContext`'s . `InvokeAsync`slouží k přepnutí do správného kontextu a fronty vykreslení.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Použití \@klíče k řízení zachování prvků a součástí

Při vykreslování seznam prvků nebo součástí a Blazorprvky nebo součásti následně změnit, 's diffing algoritmus musí rozhodnout, které z předchozích prvků nebo součástí mohou být zachovány a jak by měly být objekty modelu mapovat na ně. Za normálních okolností je tento proces automatický a může být ignorován, ale existují případy, kdy můžete chtít řídit proces.

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

Obsah kolekce `People` se může změnit s vloženými, odstraněnými nebo znovu objednanými položkami. Když se komponenta znovu `<DetailsEditor>` vykreslí, `Details` může se změnit, aby přijínala různé hodnoty parametrů. To může způsobit složitější rerendering, než bylo očekáváno. V některých případech může rerendering vést k viditelné rozdíly chování, jako je například ztráta fokusu prvku.

Proces mapování lze řídit [`@key`](xref:mvc/views/razor#key) pomocí atributu direktivy. `@key`způsobí, že algoritmus diffing zaručit zachování prvků nebo součástí na základě hodnoty klíče:

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

Při `People` změně kolekce algoritmus diffing zachová `<DetailsEditor>` přidružení `person` mezi instancemi a instancemi:

* Pokud `Person` je odstraněn `People` ze seznamu, `<DetailsEditor>` pouze odpovídající instance je odebrána z ui. Ostatní instance zůstanou beze změny.
* Pokud `Person` je a vložen na některé místo `<DetailsEditor>` v seznamu, jedna nová instance je vložena na odpovídající pozici. Ostatní instance zůstanou beze změny.
* Pokud `Person` jsou položky znovu `<DetailsEditor>` objednány, odpovídající instance jsou zachovány a znovu uspořádány v ui.

V některých `@key` scénářích použití minimalizuje složitost rerendering a zabraňuje potenciální problémy se změnou stavové části DOM, jako je například umístění fokusu.

> [!IMPORTANT]
> Klíče jsou místní pro každý prvek kontejneru nebo součást. Klíče nejsou v celém dokumentu porovnávány globálně.

### <a name="when-to-use-key"></a>Kdy použít \@klíč

Obvykle má smysl použít `@key` vždy, když je vykreslen seznam (například v `@foreach` bloku) a existuje `@key`vhodná hodnota pro definování .

Můžete také `@key` zabránit Blazor zachování prvku nebo podstromu součásti při změně objektu:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Pokud `@currentPerson` se `@key` změní, Blazor direktiva atributů vynutí zahodit celý `<div>` a jeho potomky a znovu vytvořit podstrom v rámci uživatelského rozhraní s novými prvky a součásti. To může být užitečné, pokud potřebujete zaručit, že `@currentPerson` žádný stav ui je zachována při změnách.

### <a name="when-not-to-use-key"></a>Kdy nepoužívat \@klíč

Při rozptylování s `@key`. Náklady na výkon není velký, `@key` ale pouze určit, pokud řízení elementu nebo pravidla zachování komponenty prospěch aplikace.

I `@key` v případě, Blazor že se nepoužívá, zachová podřízené instance elementu a komponenty co nejvíce. Jedinou výhodou `@key` použití je kontrola nad *tím, jak* jsou instance modelu mapovány na instance zachované součásti, namísto algoritmu diffing, který vybírá mapování.

### <a name="what-values-to-use-for-key"></a>Jaké hodnoty použít \@pro klíč

Obecně má smysl dodávat jeden z následujících `@key`druhů hodnoty pro :

* Instance objektu modelu (například `Person` instance jako v předchozím příkladu). Tím je zajištěno zachování na základě rovnosti odkazů na objekt.
* Jedinečné identifikátory (například hodnoty primárního `string`klíče `Guid`typu `int`, nebo ).

Ujistěte se, `@key` že hodnoty použité pro nekolidovat. Pokud jsou zjištěny kolidující hodnoty Blazor v rámci stejného nadřazeného prvku, vyvolá výjimku, protože nemůže deterministicky mapovat staré prvky nebo součásti na nové prvky nebo součásti. Používejte pouze odlišné hodnoty, například instance objektů nebo hodnoty primárního klíče.

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a>Nevytvářejte součásti, které zapisují do vlastních vlastností parametrů

Parametry jsou přepsány za následujících podmínek:

* Podřízený prvek obsah je vykreslen `RenderFragment`s .
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>se nazývá v nadřazené součásti.

Parametry jsou resetovány, protože nadřazená komponenta se znovu vykresluje, když <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> je volána, a do podřízené součásti jsou dodávány nové hodnoty parametrů.

Zvažte `Expander` následující součást, která:

* Vykreslí podřízený obsah.
* Přepíná zobrazení podřízeného obsahu s parametrem komponenty.

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

Komponenta `Expander` je přidána do nadřazené součásti, která může volat `StateHasChanged`:

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

Zpočátku `Expander` součásti chovat nezávisle, `Expanded` když jsou jejich vlastnosti přepnuto. Podřízené součásti udržovat jejich stavy podle očekávání. Když `StateHasChanged` je volána v `Expanded` nadřazeném, parametr první podřízené`true`součásti je obnoven zpět na počáteční hodnotu ( ). `Expanded` Hodnota druhé `Expander` součásti není resetována, protože v druhé součásti není vykreslen žádný podřízený obsah.

Chcete-li zachovat stav v předchozím scénáři, `Expander` použijte soukromé *pole* v komponentě k zachování jeho přeplovaný stav.

Následující `Expander` součást:

* Přijme hodnotu parametru `Expanded` komponenty z nadřazeného objektu.
* Přiřadí hodnotu parametru komponenty`_expanded` *soukromému poli* ( ) v události [OnInitialized](xref:blazor/lifecycle#component-initialization-methods).
* Používá soukromé pole k zachování jeho vnitřní přepnout stav.

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

Komponenty razor jsou generovány jako částečné třídy. Komponenty břitvy jsou vytvářeny jedním z následujících přístupů:

* Kód C# je [`@code`](xref:mvc/views/razor#code) definován v bloku se značkami HTML a kódem Razor v jednom souboru. Blazoršablony definují své komponenty Razor pomocí tohoto přístupu.
* Kód Jazyka C# je umístěn v souboru s kódem na pozadí definovaném jako částečná třída.

Následující příklad ukazuje `Counter` výchozí komponentu s blokem `@code` v Blazor aplikaci vygenerované ze šablony. Značky HTML, kód Razor a kód Jazyka C# jsou ve stejném souboru:

*Counter.razor*:

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

Komponentu `Counter` lze také vytvořit pomocí souboru s kódem na pozadí s částečnou třídou:

*Counter.razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.razor.cs*:

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

Podle potřeby přidejte všechny požadované obory názvů do souboru částečné třídy. Mezi typické obory názvů používané komponentami Razor patří:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>Určení základní třídy

Direktivu [`@inherits`](xref:mvc/views/razor#inherits) lze použít k určení základní třídy pro komponentu. Následující příklad ukazuje, jak může komponenta `BlazorRocksBase`zdědit základní třídu , a poskytnout tak vlastnosti a metody komponenty. Základní třída by `ComponentBase`měla být odvozena z .

*Stránky/BlazorRocks.břitva*:

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

## <a name="specify-an-attribute"></a>Určení atributu

Atributy lze zadat v komponenty [`@attribute`](xref:mvc/views/razor#attribute) Razor se směrnicí. Následující příklad použije `[Authorize]` atribut pro třídu komponenty:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>Import komponent

Obor názvů komponenty vytvořené holicím strojkem je založen na (v pořadí podle priority):

* [`@namespace`](xref:mvc/views/razor#namespace)označení v souboru Razor (`@namespace BlazorSample.MyNamespace`*.razor*) ().
* Projekt je `RootNamespace` v souboru`<RootNamespace>BlazorSample</RootNamespace>`projektu ( ).
* Název projektu převzatý z názvu souboru projektu (*.csproj*) a cesta z kořenového adresáře projektu do komponenty. Například rozhraní překládá *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj* `BlazorSample.Pages`) do oboru názvů . Součásti postupujte c# název vazby pravidla. Pro `Index` součást v tomto příkladu jsou součásti v oboru všechny součásti:
  * Ve stejné složce *stránky*.
  * Součásti v kořenovém adresáři projektu, které explicitně neurčují jiný obor názvů.

Součásti definované v jiném oboru názvů jsou uvedeny do oboru pomocí [`@using`](xref:mvc/views/razor#using) Razor směrnice.

Pokud ve `NavMenu.razor`složce *BlazorSample/Shared/* existuje jiná komponenta , `Index.razor` lze komponentu použít pomocí následujícího `@using` příkazu:

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Součásti lze také odkazovat pomocí jejich plně kvalifikované názvy, což nevyžaduje [`@using`](xref:mvc/views/razor#using) směrnice:

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> Kvalifikace `global::` není podporována.
>
> Import součástí pomocí aliasovaných `using` příkazů `@using Foo = Bar`(například) není podporován.
>
> Částečně kvalifikované názvy nejsou podporovány. Například přidávání `@using BlazorSample` a `NavMenu.razor` `<Shared.NavMenu></Shared.NavMenu>` odkazování s není podporováno.

## <a name="conditional-html-element-attributes"></a>Podmíněné atributy elementu HTML

Atributy elementu HTML jsou podmíněně vykresleny na základě hodnoty .NET. Pokud je `false` hodnota `null`nebo , atribut není vykreslen. Pokud je `true`hodnota , atribut je vykreslen minimalizovat.

V následujícím příkladu určuje, `IsCompleted` zda `checked` je vykreslen ve značkách prvku:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Pokud `IsCompleted` `true`je , zaškrtávací políčko je vykresleno takto:

```html
<input type="checkbox" checked />
```

Pokud `IsCompleted` `false`je , zaškrtávací políčko je vykresleno takto:

```html
<input type="checkbox" />
```

Další informace naleznete v tématu <xref:mvc/views/razor>.

> [!WARNING]
> Některé atributy HTML, například [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), nefungují správně, `bool`pokud je typ .NET . V těchto případech `string` použijte typ `bool`namísto .

## <a name="raw-html"></a>Nezpracovaný kód HTML

Řetězce jsou obvykle vykresleny pomocí textových uzlů DOM, což znamená, že všechny značky, které mohou obsahovat, jsou ignorovány a považovány za doslovný text. Chcete-li vykreslit nezpracovaný `MarkupString` kód HTML, zalomte obsah HTML do hodnoty. Hodnota je analyzována jako HTML nebo SVG a vložena do do m.

> [!WARNING]
> Vykreslování raw HTML vytvořené z jakéhokoli nedůvěryhodného zdroje je **bezpečnostní riziko** a je třeba se mu vyhnout!

Následující příklad ukazuje `MarkupString` použití typu pro přidání bloku statického obsahu HTML do vykresleného výstupu komponenty:

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>Kaskádové hodnoty a parametry

V některých případech je nepohodlné tok dat z nadřazené součásti do podmořské součásti pomocí [parametrů komponenty](#component-parameters), zejména pokud existuje několik vrstev komponent. Kaskádové hodnoty a parametry tento problém řeší tím, že poskytují nadřazené součásti pohodlný způsob, jak poskytnout hodnotu všem jejím nadřízeným součástem. Kaskádové hodnoty a parametry také poskytují přístup pro komponenty koordinovat.

### <a name="theme-example"></a>Příklad motivu

V následujícím příkladu z ukázkové aplikace `ThemeInfo` třída určuje informace o motivu, které mají stékat dolů hierarchií komponent, takže všechna tlačítka v dané části aplikace sdílejí stejný styl.

*UIThemeClasses/ThemeInfo.cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Nadřazená komponenta může poskytnout kaskádovou hodnotu pomocí komponenty Kaskádová hodnota. Komponenta `CascadingValue` obtéká podstrom hierarchie komponent a dodává jednu hodnotu všem součástem v rámci tohoto podstromu.

Ukázková aplikace například určuje informace`ThemeInfo`o motivu ( ) v jednom z rozložení aplikace jako kaskádový parametr pro `@Body` všechny součásti, které tvoří tělo rozložení vlastnosti. `ButtonClass`je přiřazena `btn-success` hodnota v součásti rozvržení. Všechny potomkové součásti můžete `ThemeInfo` spotřebovat tuto vlastnost prostřednictvím kaskádového objektu.

`CascadingValuesParametersLayout`Komponenty:

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

Chcete-li použít kaskádové hodnoty, komponenty deklarují kaskádové parametry pomocí atributu. `[CascadingParameter]` Kaskádové hodnoty jsou vázány na kaskádové parametry podle typu.

V ukázkové aplikaci `CascadingValuesParametersTheme` komponenta `ThemeInfo` váže kaskádovou hodnotu na kaskádový parametr. Parametr slouží k nastavení třídy CSS pro jedno z tlačítek zobrazených komponentou.

`CascadingValuesParametersTheme`Komponenty:

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

Chcete-li kaskádovat více hodnot stejného typu v `Name` rámci `CascadingValue` stejného podstromu, zadejte jedinečný řetězec pro každou komponentu a její odpovídající `CascadingParameter`. V následujícím příkladu `CascadingValue` dvě součásti kaskádové různé instance `MyCascadingType` podle názvu:

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

V součásti potomku obdrží kaskádové parametry své hodnoty z odpovídajících kaskádových hodnot v součásti předchůdce podle názvu:

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

Kaskádové parametry také umožňují součástem spolupracovat v hierarchii komponent. Zvažte například následující příklad *TabSet* v ukázkové aplikaci.

Ukázková aplikace `ITab` má rozhraní, které karty implementují:

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

Komponenta `CascadingValuesParametersTabSet` používá `TabSet` komponentu, `Tab` která obsahuje několik součástí:

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

Podřízené `Tab` součásti nejsou explicitně předány `TabSet`jako parametry . Místo toho `Tab` podřízené součásti jsou součástí `TabSet`podřízeného obsahu . Stále však `TabSet` potřebuje vědět `Tab` o každé součásti tak, aby mohla vykreslit záhlaví a aktivní kartu. Chcete-li povolit tuto koordinaci `TabSet` bez nutnosti dalšího kódu, *komponenta může poskytnout sám sebe jako kaskádové hodnoty,* která je pak zvedl podspodnou `Tab` součásti.

`TabSet`Komponenty:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Podřízené `Tab` součásti zachycují obsahující `TabSet` jako kaskádový parametr, `Tab` takže se komponenty `TabSet` přidají k souřadnici a souřadnice, na které je karta aktivní.

`Tab`Komponenty:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Šablony holicích strojků

Render fragmenty lze definovat pomocí syntaxe šablony Razor. Šablony holicího strojku představují způsob, jak definovat fragment uživatelského uživatelského nastavení a převzít následující formát:

```razor
@<{HTML tag}>...</{HTML tag}>
```

Následující příklad ukazuje, jak `RenderFragment` `RenderFragment<T>` určit a hodnoty a vykreslení šablon přímo v komponentě. Fragmenty vykreslení lze také předat jako argumenty [šablonované součásti](xref:blazor/templated-components).

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

## <a name="scalable-vector-graphics-svg-images"></a>Škálovatelné obrazy vektorové grafiky (SVG)

Vzhledem k tomu, Blazor rendrování HTML, prohlížeč-podporované obrázky, včetně škálovatelné vektorové grafiky (SVG) obrázky (*.svg*), jsou podporovány prostřednictvím tag: `<img>`

```html
<img alt="Example image" src="some-image.svg" />
```

Podobně jsou obrázky SVG podporovány v pravidlech CSS souboru stylů (*.css*):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Vložená značka SVG však není podporována ve všech scénářích. Pokud umístíte `<svg>` značku přímo do souboru komponenty *(.razor),* základní vykreslování obrázků je podporováno, ale mnoho pokročilých scénářů ještě není podporováno. `<use>` Značky například nejsou v současné době `@bind` respektovány a nelze je použít u některých značek SVG. Očekáváme, že tato omezení budeme řešit v budoucí verzi.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/blazor/server>&ndash; Obsahuje pokyny Blazor pro vytváření serverových aplikací, které se musí potýkat s vyčerpáním prostředků.
