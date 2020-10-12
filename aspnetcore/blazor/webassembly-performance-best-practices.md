---
title: Blazor WebAssemblyOsvědčené postupy týkající se ASP.NET Core výkonu
author: pranavkm
description: Tipy pro zvýšení výkonu v Blazor WebAssembly aplikacích ASP.NET Core a předcházení běžným problémům s výkonem.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: ea3f197e5bab82f4fb40238fe31cd5ce29ab62ad
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900970"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a>Blazor WebAssemblyOsvědčené postupy týkající se ASP.NET Core výkonu

Od [Pranav Krishnamoorthy](https://github.com/pranavkm) a [Steve Sanderson](https://github.com/SteveSandersonMS)

Blazor WebAssembly je pečlivě navržená a optimalizovaná tak, aby umožňovala vysoký výkon ve většině reálných scénářů uživatelského rozhraní aplikací. Vyprodukování nejlepších výsledků však závisí na vývojářích, kteří používají správné vzory a funkce. Vezměte v úvahu následující aspekty:

* **Běhová propustnost**: kód .NET běží na Překladači v rámci modulu WebAssembly runtime, takže propustnost procesoru je omezená. V případě náročných scénářů aplikace těží z [Optimalizace rychlosti vykreslování](#optimize-rendering-speed).
* **Čas spuštění**: aplikace přenáší modul runtime .NET do prohlížeče, takže je důležité používat funkce, které [minimalizují velikost stahovaných aplikací](#minimize-app-download-size).

## <a name="optimize-rendering-speed"></a>Optimalizovat rychlost vykreslování

V následujících oddílech jsou uvedeny doporučení pro minimalizaci úlohy vykreslování a zlepšení rychlosti odezvy uživatelského rozhraní. Podle těchto pokynů můžete snadno vytvořit *10 nebo vyšší vylepšení* rychlosti vykreslování uživatelského rozhraní.

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a>Vyhnout se zbytečnému vykreslování podstromů komponent

Za běhu komponenty existují jako hierarchie. Kořenová komponenta má podřízené součásti. Pak mají podřízené položky kořenového adresáře vlastní podřízené komponenty a tak dále. V případě, že dojde k události, jako je například uživatel, který vybírá tlačítko, se jedná o způsob, jakým se budou Blazor součásti znovu vykreslovat:

 1. Událost samotná je odeslána do jakékoli součásti vygenerované obslužnou rutinou události. Po spuštění obslužné rutiny události se tato součást znovu vykreslí.
 1. Pokaždé, když je znovu vykreslena nějaká komponenta, poskytuje novou kopii hodnot parametrů každé z jejích podřízených komponent.
 1. Při přijímání nové sady hodnot parametrů každá součást zvolí, zda se má znovu vykreslit. Ve výchozím nastavení se komponenty znovu vykreslují, pokud se hodnoty parametrů mohly změnit (například pokud jsou proměnlivé objekty).

Poslední dva kroky této sekvence budou rekurzivně vycházet z hierarchie součástí. V mnoha případech se celý podstrom znovu vykreslí. To znamená, že události, které cílí na komponenty vysoké úrovně, mohou způsobit náročné procesy opětovného vykreslování, protože vše pod tímto bodem musí být znovu vykresleno.

Pokud chcete tento proces přerušit a zabránit vykreslování rekurze do konkrétního podstromu, můžete buď:

 * Zajistěte, aby všechny parametry určité součásti byly primitivních neměnných typů (například,,, `string` `int` `bool` `DateTime` a další). Vestavěná logika pro zjišťování změn automaticky přeskočí opětovné vykreslení, pokud žádná z těchto hodnot parametrů nebyla změněna. Pokud vykreslíte podřízenou komponentu pomocí `<Customer CustomerId="@item.CustomerId" />` , kde `CustomerId` je `int` hodnota, pak není znovu vykreslena s výjimkou `item.CustomerId` změn.
 * Pokud potřebujete přijmout neprimitivní hodnoty parametrů, jako jsou například vlastní typy modelů, zpětná volání událostí nebo <xref:Microsoft.AspNetCore.Components.RenderFragment> hodnoty, můžete přepsat <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pro řízení rozhodování o tom, zda se má vykreslit, což je popsáno v části [ `ShouldRender` použití](#use-of-shouldrender) oddílu.

Vynecháním revykreslování celých podstromů může být možné odebrat převážnou většinu nákladů na vykreslování, když dojde k události.

Můžete chtít, aby podřízené komponenty byly vytvořeny konkrétně, abyste mohli přeskočit znovu vykreslování, které je součástí uživatelského rozhraní. Toto je platný způsob, jak snížit náklady na vykreslování nadřazené komponenty.

#### <a name="use-of-shouldrender"></a>Použití `ShouldRender`

Pokud vytváříte komponentu pouze s uživatelským rozhraním, která se po počátečním vykreslení nemění (bez ohledu na hodnoty parametrů), nakonfigurujte, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> aby vracela `false` :

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

Pokud komponenta vyžaduje převykreslování pouze v případě, že hodnoty parametru jsou vhodné pro konkrétní způsob, můžete použít soukromá pole ke sledování potřebných informací pro detekci změn. V následujícím příkladu `shouldRender` je založena na kontrole jakéhokoli druhu změny nebo mutace, které by měly zobrazit výzvu k revykreslování. `prevOutboundFlightId` a `prevInboundFlightId` Sledujte informace o další možné aktualizaci:

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

V předchozím kódu může obslužná rutina události také nastavit `shouldRender` `true` tak, aby se komponenta po události znovu vygenerovala.

Pro většinu komponent není tato úroveň ručního řízení nutná. Měli byste se zabývat pouze přeskočením podstromů vykreslování, pokud jsou tyto podstromy zvláště nákladné pro vykreslování a způsobují prodlevu uživatelského rozhraní.

Další informace naleznete v tématu <xref:blazor/components/lifecycle>.

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a>Virtualizace

Při vykreslování velkých objemů uživatelského rozhraní v rámci smyčky, například seznamu nebo mřížky s tisíci položkami, může Sheer množství operací vykreslování vést k prodlevě při vykreslování uživatelského rozhraní a tudíž k nedostatečnému uživatelskému prostředí. Vzhledem k tomu, že uživatel může zobrazit pouze malý počet prvků současně bez posouvání, zdá se, že wasteful stráví spoustu času vykreslováním prvků, které nejsou aktuálně viditelné.

K tomuto řešení Blazor poskytuje vestavěnou [ `<Virtualize>` komponentu](xref:blazor/components/virtualization) , která vytvoří chování při zobrazení a posouvání v libovolně velkém seznamu, ale ve skutečnosti vykreslí pouze položky seznamu, které jsou v aktuálním zobrazení posuvníku. To například znamená, že aplikace může obsahovat seznam s 100 000 položkami, ale pouze platíte náklady na vykreslování na 20 položek, které jsou zobrazeny v jednom okamžiku. Použití `<Virtualize>` komponenty může škálovat výkon uživatelského rozhraní podle pořadí podle velikosti.

`<Virtualize>` dá se použít, když:

 * Vykreslení sady datových položek ve smyčce.
 * Většina položek není viditelná v důsledku posouvání.
 * Vykreslené položky mají přesně stejnou velikost. Když se uživatel posune na libovolný bod, komponenta může vypočítat viditelné položky k zobrazení.

Následující příklad ukazuje příklad nevirtualizovaného seznamu:

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

Pokud `allFlights` kolekce obsahuje 10 000 položek, vytvoří instance a vykresluje 10 000 `<FlightSummary>` instancí komponent. V porovnání ukazuje následující příklad virtualizovaného seznamu:

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    </Virtualize>
</div>
```

I když výsledné uživatelské rozhraní vypadá stejně pro uživatele, na pozadí se komponenta pouze vytváří a vykresluje jako mnoho `<FlightSummary>` instancí, kolik je potřeba k vyplnění rolovací oblasti. Sada `<FlightSummary>` zobrazených instancí se přepočítá a vykreslí při posunu uživatele.

`<Virtualize>` má také jiné výhody. Například pokud komponenta požaduje data z externího rozhraní API, `<Virtualize>` umožňuje komponentě načíst jenom řez záznamů, které odpovídají aktuální viditelné oblasti, místo aby se stáhla všechna data z kolekce.

Další informace naleznete v tématu <xref:blazor/components/virtualization>.

::: moniker-end

### <a name="create-lightweight-optimized-components"></a>Vytváření odlehčených optimalizovaných komponent

Většina Blazor součástí nevyžaduje agresivní optimalizační úsilí. Důvodem je to, že většina komponent se v uživatelském rozhraní často neopakuje a neprovádí se znovu s vysokou frekvencí. Například `@page` komponenty a komponenty představující uživatelské rozhraní vysoké úrovně, jako jsou dialogová okna nebo formuláře, se pravděpodobně zobrazují pouze po jednom a znovu se vykreslují v reakci na gesto uživatele. Tyto komponenty nevytvářejí vysokou úlohu vykreslování, takže můžete volně používat libovolnou kombinaci funkcí rozhraní, které chcete, aniž byste se museli starat o výkon vykreslování.

Existují však i běžné scénáře, kde sestavíte součásti, které je třeba opakovaně škálovat. Například:

 * Velké vnořené formuláře mohou obsahovat stovky individuálních vstupů, popisků a dalších prvků.
 * Mřížky mohou obsahovat tisíce buněk.
 * Bodový graf může mít miliony datových bodů.

Pokud se každá jednotka řadí jako samostatné instance komponenty, bude to mnoho z nich, aby jejich výkon při vykreslování byl kritický. V této části najdete pokyny k tomu, aby tyto komponenty byly odlehčené, takže uživatelské rozhraní zůstane rychlé a reagovat.

#### <a name="avoid-thousands-of-component-instances"></a>Vyhněte se tisícům instancí součástí

Každá součást je samostatný ostrov, který lze vykreslit nezávisle na svých nadřazených a podřízených objektech. Výběrem způsobu rozdělení uživatelského rozhraní do hierarchie komponent převezmete kontrolu nad členitost vykreslování uživatelského rozhraní. Může to být pro výkon buď dobrá, nebo nesprávná.

 * Rozdělením uživatelského rozhraní do více komponent můžete mít menší části uživatelského rozhraní znovu vykreslit, když dojde k událostem. Například když uživatel klikne na tlačítko v řádku tabulky, bude možné mít pouze jeden řádek znovu vykreslit místo celé stránky nebo tabulky.
 * Každá další součást ale zahrnuje určitou dodatečnou paměť a režii procesoru při zakládání se svým nezávislým stavem a životním cyklem vykreslování.

Při vyladění výkonu Blazor WebAssembly rozhraní .NET 5 jsme zjistili režii vykreslování přibližně 0,06 MS za instanci komponenty. Vychází z jednoduché součásti, která přijímá tři parametry běžící na typickém přenosném počítači. Interní režie je z velké části z důvodu načítání stavu jednotlivých součástí ze slovníků a předávání a přijímání parametrů. Pomocí násobení můžete vidět, že přidání 2 000 instancí součástí by do doby vykreslování přidali 0,12 sekund a uživatelské rozhraní by mohlo zpomalit uživatele.

Je možné, aby komponenty byly mnohem obtížnější, abyste je mohli využít, ale často výkonnější techniky nemají tolik komponent. Následující části popisují dva přístupy.

##### <a name="inline-child-components-into-their-parents"></a>Vložené podřízené komponenty do jejich nadřazených prvků

Vezměte v úvahu následující komponentu, která vykreslí sekvenci podřízených komponent:

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

Pro předchozí příklad kódu `<ChatMessageDisplay>` je komponenta definována v souboru `ChatMessageDisplay.razor` obsahujícím:

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

Předchozí příklad funguje správně a má za předpokladu, že tisíce zpráv nejsou zobrazeny najednou. Chcete-li zobrazit tisíce zpráv najednou, zvažte, jestli *nechcete* oddělit jednotlivé `ChatMessageDisplay` součásti. Místo toho je třeba vložit vykreslování přímo do nadřazeného objektu:

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

Tím se zabrání režie na součást vykreslování, takže mnoho podřízených komponent za cenu není schopné je nezávisle znovu vykreslovat.

##### <a name="define-reusable-renderfragments-in-code"></a>Definovat opakované použití `RenderFragments` v kódu

Podřízené komponenty můžete vyfiltrovat čistě jako způsob, jak znovu použít logiku vykreslování. Pokud je to tento případ, je stále možné znovu použít logiku vykreslování bez deklarování skutečných komponent. V bloku jakékoli komponenty `@code` můžete definovat <xref:Microsoft.AspNetCore.Components.RenderFragment> , který EMITUJE uživatelské rozhraní a který lze volat odkudkoli:

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

Jak je demonstated v předchozím příkladu, komponenty mohou generovat značky z kódu v rámci svého `@code` bloku a mimo něj. Tento přístup definuje <xref:Microsoft.AspNetCore.Components.RenderFragment> delegáta, který můžete vykreslit uvnitř normálního výstupu vykreslování komponenty, volitelně na více místech. Je nezbytné, aby mohl delegát přijmout parametr s názvem `__builder` <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> , aby Razor kompilátor mohl vytvořit pokyny pro vykreslování pro tento typ.

Pokud chcete tuto možnost opakovaně použít napříč více komponentami, zvažte její deklaraci jako `public static` člena:

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

Tuto chybu lze nyní vyvolat z nesouvisející součásti. Tato technika je užitečná pro vytváření knihoven opakovaně použitelných fragmentů kódu, které vykreslují bez režie pro komponentu.

<xref:Microsoft.AspNetCore.Components.RenderFragment> Delegáti mohou také přijímat parametry. Chcete-li vytvořit ekvivalent `ChatMessageDisplay` komponenty z předchozího příkladu:

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @DisplayChatMessage(message)
    }
</div>

@code {
    RenderFragment<ChatMessage> DisplayChatMessage = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

Tento přístup poskytuje výhodu opětovného použití logiky vykreslování bez režie pro jednotlivé komponenty. Nemá ale výhodu pro možnost aktualizovat svůj podstrom uživatelského rozhraní nezávisle, ani nemůže přeskočit vykreslování, který podstrom uživatelského rozhraní při vykreslení jeho nadřazeného objektu, protože neexistuje hranice součásti.

#### <a name="dont-receive-too-many-parameters"></a>Nedostávat příliš mnoho parametrů

Pokud se komponenta opakuje velmi často, například stovky nebo tisíce časů, pak je potřeba mít na paměti, že režie při předávání a přijímání jednotlivých parametrů.

Je málo pravděpodobné, že příliš mnoho parametrů vážně omezuje výkon, ale může se jednat o faktor. Pro `<TableCell>` komponentu, která vykresluje v rámci mřížky 1 000 časů, každý další předaný parametr by mohl přidat přibližně 15 MS na celkové náklady na vykreslení. Pokud každá buňka přijala 10 parametrů, předávání parametru přebírá přibližně 150 MS na vykreslování komponenty a tedy asi 150 000 ms (150 sekund) a na vlastní příčinu neurčitého uživatelského rozhraní.

Pro snížení tohoto zatížení můžete seskupit více parametrů prostřednictvím vlastních tříd. `<TableCell>`Komponenta může například přijmout:

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

V předchozím příkladu je aplikace `Data` pro každou buňku odlišná, ale `Options` je společná napříč všemi nimi. Samozřejmě se může jednat o vylepšení, které neobsahují `<TableCell>` komponentu a místo toho by měla být vložena její logika do nadřazené komponenty.

#### <a name="ensure-cascading-parameters-are-fixed"></a>Zajistěte, aby se kaskádové parametry opravily

`<CascadingValue>`Komponenta má volitelný parametr s názvem `IsFixed` .

 * Pokud `IsFixed` je hodnota `false` (výchozí nastavení), pak každý příjemce v kaskádové hodnotě nastaví předplatné pro přijímání oznámení o změně. V takovém případě je každá `[CascadingParameter]` z nich z důvodu sledování předplatného **podstatně dražší** , než je obvyklé `[Parameter]` .
 * Pokud `IsFixed` je hodnota `true` (například `<CascadingValue Value="@someValue" IsFixed="true">` ), pak příjemci obdrží počáteční hodnotu, ale nenastaví žádné předplatné *not* pro příjem aktualizací. V takovém případě je každá z nich `[CascadingParameter]` odlehčená a **nesmí být dražší** než pravidelná `[Parameter]` .

Takže pokud je to možné, měli byste použít `IsFixed="true"` na kaskádové hodnoty. To můžete provést vždy, když se zadaná hodnota nemění v průběhu času. V běžném vzoru, kde součást přechází `this` jako na kaskádovou hodnotu, byste měli použít `IsFixed="true"` :

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

To přináší velký rozdíl v případě, že existuje velký počet dalších komponent, které získají kaskádovou hodnotu. Další informace naleznete v tématu <xref:blazor/components/cascading-values-and-parameters>.

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a>Vyhnout se atributu seskupováním s `CaptureUnmatchedValues`

Komponenty se mohou rozhodnout, že budou přijímat "nespárované" hodnoty parametrů pomocí <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> příznaku:

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

Tento přístup umožňuje předávání libovolných dalších atributů elementu. Je však také poměrně nákladné, protože zobrazovací jednotka musí:

* Porovná všechny zadané parametry se sadou známých parametrů pro sestavení slovníku.
* Udržujte si přehled o tom, jak různé kopie stejného atributu jsou vzájemně přepsány.

Používejte <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> nepostradatelné komponenty, jako jsou ty, které se neopakují často. Nicméně pro komponenty, které jsou škálovatelné, například jednotlivé položky v rozsáhlých seznamech nebo buňkách v mřížce, se pokuste vyhnout atributu seskupováním.

Další informace naleznete v tématu <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.

#### <a name="implement-setparametersasync-manually"></a>`SetParametersAsync`Ruční implementace

Jednou z hlavních aspektů režie vykreslování jednotlivých komponent je zápis hodnot příchozích parametrů do `[Parameter]` vlastností. K tomuto účelu musí zobrazovací jednotka použít reflexi. I když je to poněkud optimalizované, neexistence podpory JIT v modulu runtime WebAssembly ukládá omezení.

V některých extrémních případech možná budete chtít zabránit reflexi a implementovat logiku vlastního nastavení parametrů ručně. To může být použitelné v těchto případech:

 * Máte komponentu, která vykresluje extrémně často (například existují stovky nebo tisíce kopií v uživatelském rozhraní).
 * Přijímá mnoho parametrů.
 * Zjistíte, že režie přijímání parametrů má pozorovatelný vliv na odezvu uživatelského rozhraní.

V těchto případech můžete přepsat virtuální <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> metodu komponenty a implementovat vlastní logiku specifickou pro komponentu. Následující příklad záměrně zabrání žádnému vyhledávání slovníku:

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

V předchozím kódu návrat základní třídy <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> spouští normální metody životního cyklu bez přiřazení parametrů znovu.

Jak vidíte v předchozím kódu, přepsání <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> a poskytnutí vlastní logiky jsou komplikované a pracné, takže tento přístup nedoporučujeme obecně. V extrémních případech může zvýšit výkon při vykreslování 20-25%, ale měli byste zvážit jenom tento postup ve výše uvedených scénářích.

### <a name="dont-trigger-events-too-rapidly"></a>Nespouštět události příliš rychle

Některé události prohlížeče se proaktivují extrémně často, například `onmousemove` a `onscroll` , což může aktivovat desítky nebo stovky časů za sekundu. Ve většině případů nemusíte často provádět aktualizace uživatelského rozhraní. Pokud se o to pokusíte, můžete poškodit rychlost odezvy uživatelského rozhraní nebo spotřebovávat nadměrný čas procesoru.

Namísto použití nativních `@onmousemove` nebo `@onscroll` událostí můžete chtít použít zprostředkovatele komunikace js k registraci zpětného volání, které se aktivuje méně často. Například následující součást ( `MyComponent.razor` ) zobrazuje pozici myši, ale pouze aktualizace vždy každých 500 ms:

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

Odpovídající kód JavaScriptu, který lze umístit na `index.html` stránku nebo načíst jako modul ES6, zaregistruje skutečný naslouchací proces událostí DOM. V tomto příkladu naslouchací proces událostí používá [ `throttle` funkci Lodash](https://lodash.com/docs/4.17.15#throttle) k omezení míry vyvolání:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

Tato technika může být ještě důležitější pro Blazor Server , protože každé vyvolání události zahrnuje doručení zprávy přes síť. To je užitečné, Blazor WebAssembly protože může významně snížit množství práce při vykreslování.

## <a name="optimize-javascript-interop-speed"></a>Optimalizovat rychlost komunikace JavaScript

Volání mezi .NET a JavaScript se skládá z nějaké další režie, protože:

 * Ve výchozím nastavení jsou volání asynchronní.
 * Ve výchozím nastavení jsou parametry a návratové hodnoty serializovány pomocí formátu JSON. Slouží k poskytnutí snadno pochopitelného mechanismu převodů mezi typy rozhraní .NET a JavaScriptu.

Kromě toho Blazor Server se tato volání předávají přes síť.

### <a name="avoid-excessively-fine-grained-calls"></a>Vyhněte se nadměrnému vyjemnému volání

Vzhledem k tomu, že každé volání zahrnuje určitou režii, může být užitečné snížit počet volání. Vezměte v úvahu následující kód, který ukládá kolekci položek v `localStorage` úložišti prohlížeče:

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

Předchozí příklad provede samostatné volání interoperability JS pro každou položku. Místo toho následující přístup snižuje zprostředkovatele komunikace JS na jedno volání:

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

Odpovídající funkce jazyka JavaScript definovaná následujícím způsobem:

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

K Blazor WebAssembly tomu obvykle dochází pouze v případě, že provádíte velké množství volání interoperability js.

### <a name="consider-making-synchronous-calls"></a>Zvažte provedení synchronních volání.

Volání interoperability JavaScriptu jsou ve výchozím nastavení asynchronní, bez ohledu na to, zda je kód volán synchronní nebo asynchronní. K tomu je potřeba zajistit, aby komponenty byly kompatibilní s Blazor WebAssembly a Blazor Server . V systému Blazor Server musí být všechna volání interoperability JavaScriptu asynchronní, protože jsou odesílána přes síťové připojení.

Pokud víte, že jste si jistí, že vaše aplikace se pořád spouští jenom na Blazor WebAssembly , můžete se rozhodnout provést synchronní volání interoperability JavaScriptu. To má poněkud méně režie než provádění asynchronních volání a může mít za následek menší počet cyklů vykreslování, protože při čekání na výsledky neexistují žádné přechodné stavy.

Chcete-li provést synchronní volání z rozhraní .NET do JavaScriptu, přetypování <xref:Microsoft.JSInterop.IJSRuntime> na <xref:Microsoft.JSInterop.IJSInProcessRuntime> :

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

Při práci s nástrojem `IJSObjectReference` můžete provést synchronní volání přetypováním na `IJSInProcessObjectReference` .

::: moniker-end

Chcete-li uskutečnit synchronní volání z JavaScriptu do .NET, použijte `DotNet.invokeMethod` místo `DotNet.invokeMethodAsync` .

Synchronní volání fungují v těchto případech:

* Aplikace běží na Blazor WebAssembly , ne Blazor Server .
* Volaná funkce vrací hodnotu synchronně (nejedná se o `async` metodu a nevrací rozhraní .NET <xref:System.Threading.Tasks.Task> nebo JavaScript `Promise` ).

Další informace naleznete v tématu <xref:blazor/call-javascript-from-dotnet>.

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a>Zvažte provedení zařazování volání

Při spuštění v systému je možné provést nezařazená Blazor WebAssembly volání z rozhraní .NET do JavaScriptu. Jedná se o synchronní volání, která neprovádějí serializaci argumentů nebo vrácených hodnot pomocí JSON. Všechny aspekty správy paměti a překlady mezi reprezentacemi .NET a JavaScriptu jsou v rámci vývojářů.

> [!WARNING]
> I když použití `IJSUnmarshalledRuntime` má minimální režii spojené s těmito rozhraními API, jsou aktuálně nedokumentovaná rozhraní API pro interakci s těmito rozhraními API a můžou v budoucích verzích podléhat zásadním změnám.

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a>Minimalizovat velikost stahovaných aplikací

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a>Oříznutí mezilehlého jazyka (IL)

[Oříznutí nepoužívaných sestavení z Blazor WebAssembly aplikace](xref:blazor/host-and-deploy/configure-trimmer) zmenší velikost aplikace odebráním nepoužívaného kódu v binárních souborech aplikace. Ve výchozím nastavení je oříznutí provedeno při publikování aplikace. Pokud chcete využít oříznutí, publikujte aplikaci pro nasazení pomocí [`dotnet publish`](/dotnet/core/tools/dotnet-publish) příkazu s možností [-c |--konfigurace](/dotnet/core/tools/dotnet-publish#options) nastavenou na `Release` :

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a>Propojování IL (Intermediate Language)

[Propojení Blazor WebAssembly aplikace](xref:blazor/host-and-deploy/configure-linker) zmenšuje velikost Aplikace oříznutím nepoužívaného kódu v binárních souborech aplikace. Ve výchozím nastavení je linker zprostředkujícího jazyka (IL) povolen pouze při sestavování v `Release` konfiguraci. Pokud to chcete využít, publikujte aplikaci pro nasazení pomocí [`dotnet publish`](/dotnet/core/tools/dotnet-publish) příkazu s možností [-c |--konfigurace](/dotnet/core/tools/dotnet-publish#options) nastavenou na `Release` :

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a>Použít System.Text.Jsna

Blazorimplementace interoperability JS spoléhá na <xref:System.Text.Json> , což je vysoce výkonné knihovny serializace JSON s neomezeným přidělením paměti. Použití <xref:System.Text.Json> nemá za následek další velikost datové části aplikace nad přidáním jedné nebo více alternativních knihoven JSON.

Pokyny k migraci najdete v tématu [Postup migrace z `Newtonsoft.Json` na `System.Text.Json` ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

### <a name="lazy-load-assemblies"></a>Sestavení opožděného načtení

Načíst sestavení za běhu, když jsou sestavení požadována trasou. Další informace naleznete v tématu <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="compression"></a>Komprese

Při Blazor WebAssembly publikování aplikace je výstup během publikování staticky komprimován, aby se snížila velikost aplikace a odstranila se režie pro kompresi za běhu. Blazor spoléhá na server, aby provedl negotation obsahu a sloužil staticky komprimovaným souborům.

Po nasazení aplikace ověřte, jestli aplikace obsluhuje komprimované soubory. Zkontrolujte kartu síť v Vývojářské nástroje prohlížeče a ověřte, zda jsou soubory obsluhovány `Content-Encoding: br` nebo `Content-Encoding: gz` . Pokud hostitel neobsluhuje komprimované soubory, postupujte podle pokynů v tématu <xref:blazor/host-and-deploy/webassembly#compression> .

### <a name="disable-unused-features"></a>Zakázat nepoužívané funkce

Blazor WebAssemblymodul runtime obsahuje následující funkce .NET, které je možné zakázat, pokud je aplikace nepotřebuje pro menší velikost datové části:

* K dispozici je datový soubor pro správné informace o časovém pásmu. Pokud aplikace tuto funkci nevyžaduje, zvažte její zakázání nastavením `BlazorEnableTimeZoneSupport` vlastnosti MSBuild v souboru projektu aplikace na `false` :

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* Ve výchozím nastavení obsahuje Blazor WebAssembly prostředky globalizace vyžadované k zobrazení hodnot, jako jsou například data a měna, v jazykové verzi uživatele. Pokud aplikace nevyžaduje lokalizaci, můžete [aplikaci nakonfigurovat tak, aby podporovala invariantní jazykovou verzi](xref:blazor/globalization-localization), která je založena na `en-US` jazykové verzi:

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* K zajištění správného fungování rozhraní API, jako je například práce, jsou zahrnuty informace o kolaci <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> . Pokud jste si jisti, že aplikace nevyžaduje data kolace, zvažte její zakázání nastavením `BlazorWebAssemblyPreserveCollationData` vlastnosti MSBuild v souboru projektu aplikace na `false` :

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
