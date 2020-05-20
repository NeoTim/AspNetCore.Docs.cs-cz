---
title: ' Vytvoření a použití ASP.NET Core Razor komponenty ' Author: Description: ' Naučte se vytvářet a používat Razor komponenty, včetně toho, jak navazovat data, zpracovávat události a spravovat životní cykly komponent. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

---
# <a name="create-and-use-aspnet-core-razor-components"></a>Vytvoření a použití Razor komponent ASP.NET Core

Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Tobias Bartsch](https://www.aveo-solutions.com/)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Blazoraplikace jsou sestavené pomocí *komponent*. Součást je samostatně obsažený blok uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář. Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vkládání dat nebo reakci na události uživatelského rozhraní. Komponenty jsou flexibilní a odlehčené. Můžou být vnořené, opakovaně používané a sdílené mezi projekty.

## <a name="component-classes"></a>Třídy komponent

Komponenty jsou implementovány v [Razor](xref:mvc/views/razor) souborech součástí (*. Razor*) pomocí kombinace kódu jazyka C# a HTML. Komponenta v Blazor je formálně označována jako * Razor Komponenta*.

Název součásti musí začínat velkým znakem. Například *MyCoolComponent. Razor* je platný a *MyCoolComponent. Razor* je neplatný.

Uživatelské rozhraní pro komponentu je definováno pomocí jazyka HTML. Dynamická logika vykreslování (například cykly, podmíněné výrazy, výrazy) je přidána pomocí vložené syntaxe jazyka C# s názvem *Razor* . Při kompilaci aplikace jsou značky HTML a vykreslování jazyka C# převedeny na třídu komponenty. Název generované třídy se shoduje s názvem souboru.

Členy třídy komponenty jsou definovány v [`@code`][1] bloku. V [`@code`][1] bloku je stav součásti (vlastnosti, pole) zadán pomocí metod pro zpracování událostí nebo pro definování jiné logiky komponent. Je přípustný více než jeden [`@code`][1] blok.

Členy součásti lze použít jako součást logiky vykreslování komponenty pomocí výrazů jazyka C#, které začínají na `@` . Například pole C# se vykreslí pomocí předpony `@` na název pole. Následující příklad vyhodnocuje a vykresluje:

* `headingFontStyle`do hodnoty vlastnosti CSS pro `font-style` .
* `headingText`k obsahu `<h1>` elementu.

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

Po prvním vykreslení komponenty vygeneruje komponenta znovu svůj strom vykreslování v reakci na události. Blazorpak porovná nový strom vykreslování s předchozí a aplikuje všechny úpravy model DOM (Document Object Model) v prohlížeči (DOM).

Komponenty jsou běžné třídy jazyka C# a lze je umístit kamkoli v rámci projektu. Komponenty, které tvoří webové stránky, jsou obvykle umístěny ve složce *stránky* . Komponenty mimo stránku jsou často umístěny ve *sdílené* složce nebo vlastní složce přidané do projektu.

Obor názvů komponenty obvykle je odvozen z kořenového oboru názvů aplikace a umístění komponenty (složka) v rámci aplikace. Pokud je kořenový obor názvů aplikace `BlazorApp` a součást se nachází `Counter` ve složce *stránky* :

* `Counter`Obor názvů součásti je `BlazorApp.Pages` .
* Plně kvalifikovaný název typu komponenty je `BlazorApp.Pages.Counter` .

Pro vlastní složky, které obsahují součásti, přidejte `using` příkaz do nadřazené komponenty nebo do souboru *_Imports. Razor* aplikace. Následující příklad zpřístupňuje komponenty ve složce *Components* :

```razor
@using BlazorApp.Components
```

Alternativně může být komponenta přímo odkazována:

```razor
<BlazorApp.Components.MyCoolComponent />
```

Další informace naleznete v části [Import komponent](#import-components) .

## <a name="razor-syntax"></a>Razorsyntaktick

Razorkomponenty v Blazor aplikacích výrazně používají Razor syntaxi. Pokud nejste obeznámeni s Razor jazykem označení, doporučujeme <xref:mvc/views/razor> před pokračováním číst.

Při přístupu k obsahu v Razor syntaxi věnujte zvláštní pozornost následujícím oddílům:

* [Direktivy](xref:mvc/views/razor#directives) &ndash; `@`-pevná vyhrazená klíčová slova, která obvykle mění způsob, jakým jsou analyzovány značky komponenty nebo funkce.
* [Atributy direktiv](xref:mvc/views/razor#directive-attributes) &ndash; `@`-pevná vyhrazená klíčová slova, která obvykle mění způsob, jakým jsou analyzovány prvky součásti nebo funkce.

## <a name="static-assets"></a>Statické prostředky

Blazorpostupuje podle konvence ASP.NET Core aplikací, které umísťují statické prostředky do [složky webového kořenového adresáře (wwwroot)](xref:fundamentals/index#web-root)projektu.

Použijte základní cestu ( `/` ) pro odkaz na webový kořenový adresář pro statický prostředek. V následujícím příkladu je soubor *logo. png* fyzicky umístěný ve složce *{Project root}/wwwroot/images* :

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razorkomponenty **nepodporují** vlnové lomítko ( `~/` ).

Informace o nastavení základní cesty aplikace najdete v tématu <xref:host-and-deploy/blazor/index#app-base-path> .

## <a name="tag-helpers-arent-supported-in-components"></a>V součástech nejsou podporovány pomocníky značek.

[Tag Helpers](xref:mvc/views/tag-helpers/intro) V Razor součástech (soubory *. Razor* ) nejsou podporované pomocníky značek. Chcete-li poskytnout funkci, jako je například Pomocník pro Blazor vytváření značek, vytvořte komponentu se stejnou funkcí jako pomocník značek a místo ní použijte komponentu.

## <a name="use-components"></a>Použití komponent

Komponenty mohou zahrnovat další komponenty deklarováním pomocí syntaxe elementu HTML. Označení pro použití komponenty vypadá jako značka HTML, kde název značky je typ komponenty.

Následující kód v *indexu. Razor* vykreslí `HeadingComponent` instanci:

```razor
<HeadingComponent />
```

*Components/HeadingComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Pokud komponenta obsahuje element HTML s velkým prvním písmenem, které neodpovídá názvu komponenty, je vygenerováno upozornění označující, že element má neočekávaný název. Přidání [`@using`][2] direktivy pro obor názvů součásti zpřístupňuje komponentu, která vyřeší upozornění.

## <a name="routing"></a>Směrování

Směrování do Blazor se dosahuje tím, že poskytuje šablonu směrování pro každou dostupnou součást aplikace.

Když Razor je zkompilován soubor s [`@page`][9] direktivou, vygenerovaná třída je dána <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadáním šablony trasy. V době běhu Směrovač vyhledává třídy komponent pomocí `RouteAttribute` a a vykreslí, že jakákoli součást má šablonu směrování, která odpovídá požadované adrese URL.

```razor
@page "/ParentComponent"

...
```

Další informace naleznete v tématu <xref:blazor/routing>.

## <a name="parameters"></a>Parametry

### <a name="route-parameters"></a>Parametry směrování

Komponenty mohou přijímat parametry směrování z šablony směrování uvedené v [`@page`][9] direktivě. Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponent.

*Stránky/RouteParameter. Razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Volitelné parametry nejsou podporovány, takže [`@page`][9] v předchozím příkladu jsou aplikovány dvě direktivy. První umožňuje navigaci na součást bez parametru. Druhá [`@page`][9] direktiva přijme `{text}` parametr Route a přiřadí hodnotu `Text` Vlastnosti.

*Catch-All* Parameter Syntax ( `*` / `**` ), která zachycuje cestu mezi více hranicemi složek, není **not** v Razor součástech (*. Razor*) podporován.

### <a name="component-parameters"></a>Parametry součásti

Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí veřejných vlastností třídy komponenty s `[Parameter]` atributem. Použijte atributy k určení argumentů pro komponentu v kódu.

*Components/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

V následujícím příkladu z ukázkové aplikace `ParentComponent` nastaví hodnotu `Title` vlastnosti `ChildComponent` .

*Stránky/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Nevytvářejte komponenty, které zapisují do vlastních *parametrů komponenty*, místo toho použijte soukromé pole. Další informace naleznete v části [Nevytvářet komponenty, které jsou zapsány do jejich vlastních vlastností parametrů](#dont-create-components-that-write-to-their-own-parameter-properties) .

## <a name="child-content"></a>Podřízený obsah

Komponenty mohou nastavit obsah jiné součásti. Součást přiřazení poskytuje obsah mezi značkami, které určují přijímací komponentu.

V následujícím příkladu `ChildComponent` má `ChildContent` vlastnost, která představuje, který představuje `RenderFragment` segment uživatelského rozhraní pro vykreslení. Hodnota `ChildContent` je umístěna v označení komponenty, kde má být obsah vykreslen. Hodnota `ChildContent` je přijímána z nadřazené komponenty a vykreslena v panelu Bootstrap `panel-body` .

*Components/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Vlastnost, která přijímá `RenderFragment` obsah, musí být pojmenována `ChildContent` podle konvence.

`ParentComponent`V ukázkové aplikaci může poskytovat obsah pro vykreslování `ChildComponent` umístěním obsahu uvnitř `<ChildComponent>` značek.

*Stránky/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Seskupováním atributů a libovolné parametry

Komponenty mohou kromě deklarovaných parametrů komponenty zachytit a vykreslovat další atributy. Další atributy mohou být zachyceny ve slovníku a poté *splatted* na prvek při vykreslení komponenty pomocí [`@attributes`][3] Razor direktivy. Tento scénář je užitečný při definování komponenty, která vytváří prvek značky, který podporuje nejrůznější přizpůsobení. Například může být zdlouhavé definovat atributy samostatně pro objekt `<input>` , který podporuje mnoho parametrů.

V následujícím příkladu první `<input>` prvek ( `id="useIndividualParams"` ) používá jednotlivé parametry komponenty, zatímco druhý `<input>` element ( `id="useAttributesDict"` ) používá atribut seskupováním:

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

Chcete-li přijmout libovolné atributy, definujte parametr komponenty pomocí `[Parameter]` atributu s `CaptureUnmatchedValues` vlastností nastavenou na `true` :

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

`CaptureUnmatchedValues`Vlastnost on `[Parameter]` umožňuje, aby parametr odpovídal všem atributům, které se neshodují s žádným jiným parametrem. Komponenta může definovat pouze jeden parametr s `CaptureUnmatchedValues` . Typ vlastnosti používaný pomocí `CaptureUnmatchedValues` musí být přiřazovatelné z `Dictionary<string, object>` řetězcových klíčů. `IEnumerable<KeyValuePair<string, object>>`nebo `IReadOnlyDictionary<string, object>` jsou také možnosti v tomto scénáři.

Pozice [`@attributes`][3] relativní vzhledem k poloze atributů elementu je důležitá. Když [`@attributes`][3] jsou splatted na elementu, atributy jsou zpracovávány zprava doleva (poslední až první). Vezměte v úvahu následující příklad komponenty, která využívá `Child` komponentu:

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

`Child` `extra` Atribut komponenty je nastaven na hodnotu napravo od [`@attributes`][3] . `Parent`Vykreslená komponenta `<div>` obsahuje `extra="5"` při předání prostřednictvím dodatečného atributu, protože atributy jsou zpracovávány zprava doleva (poslední až první):

```html
<div extra="5" />
```

V následujícím příkladu `extra` je pořadí a [`@attributes`][3] obrácené v `Child` komponentě `<div>` :

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

Vykreslená `<div>` `Parent` Komponenta obsahuje, `extra="10"` když je předána pomocí dalšího atributu:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Zachytit odkazy na komponenty

Odkazy na komponenty poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance, například `Show` nebo `Reset` . Zachytit odkaz na komponentu:

* Přidejte [`@ref`][4] atribut do podřízené součásti.
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

Při vykreslení komponenty `loginDialog` je pole vyplněno `MyLoginDialog` instancí podřízené součásti. Pak můžete vyvolat metody .NET v instanci komponenty.

> [!IMPORTANT]
> `loginDialog`Proměnná je naplněna pouze po vykreslení komponenty a její výstup obsahuje `MyLoginDialog` element. Do tohoto okamžiku neexistuje žádný odkaz na. Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování komponenty, použijte [metody OnAfterRenderAsync nebo OnAfterRender](xref:blazor/lifecycle#after-component-render).

Chcete-li odkazovat na součásti ve smyčce, viz [zachytit odkazy na více podobných podřízených komponent (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Při zachytávání odkazů na součásti použijte podobnou syntaxi pro [zachycení odkazů na prvky](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), není to funkce interoperability JavaScriptu. Odkazy na součásti nejsou předány kódu jazyka JavaScript. Odkazy na komponenty se používají pouze v kódu .NET.

> [!NOTE]
> Nepoužívejte odkazy na součásti **pro použití stavu** podřízených komponent. Místo toho použijte k předání dat podřízeným komponentám běžné deklarativní parametry. Použití běžných deklarativních parametrů má za následek podřízené komponenty, které jsou automaticky revykreslovány ve správný čas.

## <a name="invoke-component-methods-externally-to-update-state"></a>Vyvolat metody komponenty externě na stav aktualizace

Blazorpoužívá kontext synchronizace ( `SynchronizationContext` ) k vykonání jediného logického vlákna provádění. [Metody životního cyklu](xref:blazor/lifecycle) komponenty a všechna zpětná volání událostí, která jsou vyvolána, Blazor jsou spouštěna v kontextu synchronizace.

BlazorKontext synchronizace serveru se pokouší emulovat prostředí s jedním vláknem tak, aby přesně odpovídal modelu webového sestavení v prohlížeči, který je jediným vláknem. V jakémkoli daném časovém okamžiku se práce provádí v přesně jednom vlákně, což dává dojem o jednom logickém vlákně. Žádné dvě operace se neprovádějí současně.

V případě, že komponenta musí být aktualizována na základě externí události, jako je například časovač nebo jiné oznámení, použijte `InvokeAsync` metodu, která bude zaslána do Blazor kontextu synchronizace. Představte si například *službu* pro upozorňování, která může oznámit všechny součásti, které jsou v aktualizovaném stavu:

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

* V rámci Blazor služby WebAssembly Zaregistrujte službu v `Program.Main` :

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* V části Blazor Server Zaregistrujte službu v nástroji `Startup.ConfigureServices` :

  ```csharp
  services.AddSingleton<NotifierService>();
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

V předchozím příkladu `NotifierService` vyvolá `OnNotify` metodu komponenty mimo Blazor kontext synchronizace. `InvokeAsync`slouží k přepnutí do správného kontextu a vykreslení vykreslování do fronty.

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

Proces mapování lze řídit pomocí [`@key`][5] atributu direktiva. [`@key`][5]způsobí, že rozdílový algoritmus garantuje zachování prvků nebo komponent na základě hodnoty klíče:

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

Obvykle má smysl použít [`@key`][5] při každém vykreslení seznamu (například v `@foreach` bloku) a existuje vhodná hodnota pro definování [`@key`][5] .

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

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a>Nevytvářejte komponenty, které zapisují do vlastností vlastního parametru.

Parametry jsou přepsány za následující podmínky:

* Obsah podřízené komponenty je vykreslen pomocí `RenderFragment` .
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>je volána v nadřazené komponentě.

Parametry jsou resetovány, protože nadřazená komponenta je znovu vygenerována <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> , když je volána, a podřízené součásti jsou zadány nové hodnoty parametrů.

Vezměte v úvahu následující `Expander` komponentu:

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

`Expander`Komponenta je přidána do nadřazené komponenty, která může zavolat `StateHasChanged` :

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

Zpočátku se `Expander` komponenty chovají nezávisle při `Expanded` přepínání jejich vlastností. Podřízené komponenty udržují své stavy podle očekávání. Když `StateHasChanged` je volána v nadřazeném prvku, `Expanded` parametr první podřízené komponenty se obnoví zpět na původní hodnotu ( `true` ). Hodnota druhé `Expander` součásti `Expanded` není resetována, protože ve druhé komponentě není vykreslen žádný podřízený obsah.

Chcete-li zachovat stav v předchozím scénáři, použijte *soukromé pole* v `Expander` součásti k údržbě jeho přepnutého stavu.

Následující `Expander` součást:

* Akceptuje `Expanded` hodnotu parametru komponenty z nadřazené položky.
* Přiřadí hodnotu parametru komponenty k *privátnímu poli* ( `expanded` ) v [události s inicializací](xref:blazor/lifecycle#component-initialization-methods).
* Pomocí soukromého pole udržuje stav interního přepínání.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

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

## <a name="partial-class-support"></a>Podpora částečné třídy

Razorkomponenty jsou generovány jako částečné třídy. Razorkomponenty jsou vytvořeny jedním z následujících přístupů:

* Kód jazyka C# je definován v [`@code`][1] bloku s označením HTML a Razor kódem v jednom souboru. Blazoršablony definují své Razor komponenty pomocí tohoto přístupu.
* Kód jazyka C# je umístěn v souboru kódu na pozadí, který je definován jako částečná třída.

Následující příklad ukazuje výchozí `Counter` komponentu s [`@code`][1] blokem v aplikaci vygenerovanou ze Blazor šablony. Kód HTML kódu Razor a kód jazyka C# jsou ve stejném souboru:

*Čítač. Razor*:

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

## <a name="specify-a-base-class"></a>Zadat základní třídu

[`@inherits`][6]Direktiva se dá použít k určení základní třídy pro komponentu. Následující příklad ukazuje, jak komponenta může dědit základní třídu, `BlazorRocksBase` k poskytnutí vlastností a metod komponenty. Základní třída by měla být odvozena od třídy `ComponentBase` .

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

Atributy lze zadat v Razor součástech s [`@attribute`][7] direktivou. Následující příklad používá `[Authorize]` atribut pro třídu komponenty:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>Importovat součásti

Obor názvů komponenty, která se vytvořila, Razor je založený na (v pořadí podle priority):

* [`@namespace`][8]označení v Razor souboru (*. Razor*) značky ( `@namespace BlazorSample.MyNamespace` ).
* Projekt `RootNamespace` v souboru projektu ( `<RootNamespace>BlazorSample</RootNamespace>` ).
* Název projektu, pořízený z názvu souboru projektu (*. csproj*) a cesta z kořenového adresáře projektu ke komponentě. Rozhraní například překládá *{root]/pages/index.Razor* (*BlazorSample. csproj*) na obor názvů `BlazorSample.Pages` . Komponenty následují pravidla vazeb názvů C#. Pro `Index` komponentu v tomto příkladu komponenty v oboru jsou všechny komponenty:
  * Ve stejné složce *stránky*.
  * Komponenty v kořenu projektu, které explicitně neurčují jiný obor názvů.

Komponenty definované v jiném oboru názvů se přenesou do rozsahu Razor [`@using`][2] direktivy using.

Pokud jiná komponenta, `NavMenu.razor` existuje ve složce *BlazorSample/Shared/* Folder, lze komponentu použít v `Index.razor` příkazu s následujícím [`@using`][2] příkazem:

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Na součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, které nevyžadují [`@using`][2] direktivu:

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> `global::`Kvalifikace není podporovaná.
>
> Import komponent s příkazy s aliasem `using` (například `@using Foo = Bar` ) není podporován.
>
> Částečně kvalifikované názvy nejsou podporovány. Například přidání `@using BlazorSample` a odkazování `NavMenu.razor` pomocí `<Shared.NavMenu></Shared.NavMenu>` není podporováno.

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
> Některé atributy HTML, jako je například [Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), nefungují správně, pokud je typ .NET `bool` . V těchto případech použijte `string` typ místo typu `bool` .

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

## <a name="cascading-values-and-parameters"></a>Kaskádové hodnoty a parametry

V některých scénářích je nevhodné přesměrovat data z komponenty předchůdce na dceřinou komponentu pomocí [parametrů komponenty](#component-parameters), zejména pokud je k dispozici několik vrstev komponent. Kaskádové hodnoty a parametry řeší tento problém tím, že poskytují pohodlný způsob, jak komponenta předchůdce poskytne hodnotu všem jejím následným součástem. Kaskádové hodnoty a parametry také poskytují přístup ke koordinaci komponent.

### <a name="theme-example"></a>Příklad motivu

V následujícím příkladu z ukázkové aplikace `ThemeInfo` Třída určuje informace o motivu pro přetečení hierarchie komponent tak, aby všechna tlačítka v dané části aplikace sdílela stejný styl.

*UIThemeClasses/ThemeInfo. cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Komponenta předchůdce může poskytnout kaskádovou hodnotu pomocí komponenty kaskádová hodnota. `CascadingValue`Komponenta zabalí podstrom hierarchie komponent a poskytne jednu hodnotu všem součástem v rámci daného podstromu.

Například ukázková aplikace určuje informace o motivu ( `ThemeInfo` ) v jednom z rozložení aplikace jako kaskádový parametr pro všechny komponenty, které tvoří tělo `@Body` Vlastnosti. `ButtonClass`má přiřazenou hodnotu `btn-success` v součásti rozložení. Každá odvozená komponenta může tuto vlastnost spotřebovat prostřednictvím `ThemeInfo` kaskádového objektu.

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

Chcete-li použít kaskádové hodnoty, komponenty deklaruje kaskádové parametry pomocí `[CascadingParameter]` atributu. Kaskádové hodnoty jsou vázány na kaskádové parametry podle typu.

V ukázkové aplikaci `CascadingValuesParametersTheme` váže komponenta `ThemeInfo` kaskádovou hodnotu k kaskádovým parametrům. Parametr slouží k nastavení třídy CSS pro jedno z tlačítek zobrazených komponentou.

`CascadingValuesParametersTheme`část

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

Pro kaskádování více hodnot stejného typu v rámci stejného podstromu zadejte jedinečný `Name` řetězec pro každou `CascadingValue` součást a odpovídající `CascadingParameter` . V následujícím příkladu jsou dvě `CascadingValue` komponenty kaskádovitě různé instance `MyCascadingType` podle názvu:

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

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

`CascadingValuesParametersTabSet`Komponenta používá `TabSet` komponentu, která obsahuje několik `Tab` komponent:

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

Podřízené `Tab` součásti nejsou explicitně předány jako parametry do `TabSet` . Místo toho jsou podřízené `Tab` součásti součástí podřízeného obsahu `TabSet` . `TabSet`Pořád ale potřebuje znát každou `Tab` komponentu, aby mohla vykreslovat hlavičky a aktivní kartu. Chcete-li povolit tuto koordinaci bez nutnosti dalšího kódu, `TabSet` komponenta *může poskytnout sebe sama jako kaskádovou hodnotu* , která je následně vyzvednuta podřízenými `Tab` součástmi.

`TabSet`část

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Podřízené `Tab` komponenty zachytí objekt obsahující `TabSet` jako kaskádový parametr, takže `Tab` komponenty přidávají sebe sama do `TabSet` souřadnice a, na které karty jsou aktivní.

`Tab`část

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razoršablony

Fragmenty vykreslování lze definovat pomocí Razor syntaxe šablony. RazorŠablony představují způsob, jak definovat fragment uživatelského rozhraní a předpokládat následující formát:

```razor
@<{HTML tag}>...</{HTML tag}>
```

Následující příklad ukazuje, jak zadat `RenderFragment` a `RenderFragment<T>` hodnoty a vykreslit šablony přímo v součásti. Fragmenty vykreslování mohou být také předány jako argumenty [součástem šablon](xref:blazor/templated-components).

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

## <a name="scalable-vector-graphics-svg-images"></a>Obrázky ve formátu SVG (Scalable Vector Graphics)

Vzhledem k tomu, že Blazor VYKRESLUJE HTML, obrázky podporované prohlížečem, včetně obrázků SVG (Scalable Vector Graphics) (*. SVG*), jsou podporovány prostřednictvím `<img>` značky:

```html
<img alt="Example image" src="some-image.svg" />
```

Podobně jsou obrázky SVG podporovány v pravidlech CSS souboru šablony stylů (*. CSS*):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Vložené značky SVG se však ve všech scénářích nepodporují. Pokud `<svg>` značku přímo umístíte do souboru komponenty (*. Razor*), podporuje se základní vykreslování obrázků, ale mnoho pokročilých scénářů ještě není podporováno. Například `<use>` značky nejsou aktuálně dodržovány a `@bind` nelze je použít s některými značkami SVG. Další informace naleznete v tématu [Podpora SVG in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).

## <a name="additional-resources"></a>Další zdroje

* <xref:security/blazor/server/threat-mitigation>&ndash;Obsahuje doprovodné materiály k vytváření Blazor Serverové aplikace, které se musí soupeří s vyčerpáním prostředků

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
