---
title: Odkaz na syntaxi holicího strojku pro ASP.NET Core
author: rick-anderson
description: Přečtěte si o syntaxi značek Razor pro vkládání kódu založeného na serveru do webových stránek.
ms.author: riande
ms.date: 02/12/2020
uid: mvc/views/razor
ms.openlocfilehash: dd5c73be56ed0dafb759df2f5ff2eac1a3b5b09e
ms.sourcegitcommit: d03905aadf5ceac39fff17706481af7f6c130411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80381770"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a>Odkaz na syntaxi holicího strojku pro ASP.NET Core

[Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), a Dan [Vicarel](https://github.com/Rabadash8820)

Razor je syntaxe značek pro vkládání kódu založeného na serveru do webových stránek. Syntaxe Razor se skládá z značek Razor, C# a HTML. Soubory obsahující Razor mají obecně příponu *.cshtml.* Břitva se také nachází v [souborech komponent Razor](xref:blazor/components) (*.razor*).

## <a name="rendering-html"></a>Vykreslování HTML

Výchozím jazykem pro Razor je HTML. Vykreslování HTML z kódu Razor se nijak neliší od vykreslování HTML ze souboru HTML. Html značky v *.cshtml* Razor soubory je vykreslen serverem beze změny.

## <a name="razor-syntax"></a>Syntaxe Razor

Razor podporuje C# `@` a používá symbol pro přechod z HTML na C#. Razor vyhodnotí výrazy Jazyka C# a vykreslí je ve výstupu HTML.

Když `@` symbol následuje [razor vyhrazené klíčové slovo](#razor-reserved-keywords), přechody do razor-specifické značky. V opačném případě přejde do prostého jazyka C#.

Chcete-li `@` uniknout symbolu ve značkách Razor, použijte druhý `@` symbol:

```cshtml
<p>@@Username</p>
```

Kód je vykreslen v HTML `@` s jedním symbolem:

```html
<p>@Username</p>
```

Atributy HTML a obsah obsahující e-mailové `@` adresy nepovažují symbol za přechodový znak. E-mailové adresy v následujícím příkladu jsou nedotčeny analýzou Razor:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>Implicitní výrazy Razor

Implicitní výrazy `@` Razor začínají následovaným kódem Jazyka C#:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

S výjimkou klíčového `await` slova C# nesmí implicitní výrazy obsahovat mezery. Pokud c# prohlášení má jasný konec, mezery mohou být prolíná:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Implicitní výrazy **nemohou** obsahovat obecné znaky jazyka C#, protože znaky uvnitř závorek (`<>`) jsou interpretovány jako značka HTML. Následující kód **není** platný:

```cshtml
<p>@GenericMethod<int>()</p>
```

Předchozí kód generuje chybu kompilátoru podobnou jedné z následujících:

* Prvek "int" nebyl uzavřen. Všechny prvky musí být buď samouzavírací nebo musí mít odpovídající koncovou značku.
* Skupinu metod GenericMethod nelze převést na nedelegovaného typu "object". Měl jste v úmyslu vyvolat metodu?

Volání obecné metody musí být zabaleno v [explicitním výrazu Razor](#explicit-razor-expressions) nebo [bloku kódu Razor](#razor-code-blocks).

## <a name="explicit-razor-expressions"></a>Explicitní razor výrazy

Explicitní razor výrazy `@` se skládají ze symbolu s vyváženou závorkou. Chcete-li vykreslit čas z minulého týdne, použije se následující značky Razor:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Veškerý obsah `@()` v závorce je vyhodnocen a vykreslen do výstupu.

Implicitní výrazy popsané v předchozí části obecně nesmí obsahovat mezery. V následujícím kódu se jeden týden neodečítá od aktuálního času:

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

Kód vykreslí následující HTML:

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Explicitní výrazy lze použít ke zřetězení textu s výsledkem výrazu:

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Bez explicitní výraz, `<p>Age@joe.Age</p>` je považován za e-mailovou adresu a `<p>Age@joe.Age</p>` je vykreslen. Při zápisu jako `<p>Age33</p>` explicitní výraz, je vykreslen.

Explicitní výrazy lze použít k vykreslení výstupu z obecných metod v souborech *.cshtml.* Následující značky ukazují, jak opravit chybu zobrazenou dříve způsobenou závorkami obecného jazyka C#. Kód je napsán jako explicitní výraz:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Kódování výrazů

C# výrazy, které vyhodnocují řetězec jsou kódovány HTML. C# výrazy, `IHtmlContent` které vyhodnocují jsou vykresleny přímo prostřednictvím `IHtmlContent.WriteTo`. C# výrazy, které nejsou `IHtmlContent` vyhodnoceny na `ToString` jsou převedeny na řetězec a zakódovány před jejich vykreslení.

```cshtml
@("<span>Hello World</span>")
```

Kód vykreslí následující HTML:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

KÓD HTML se v prohlížeči zobrazuje jako:

```html
<span>Hello World</span>
```

`HtmlHelper.Raw`výstup není kódován, ale vykreslen jako html značky.

> [!WARNING]
> Použití `HtmlHelper.Raw` na nedezinfikované masívní uživatelský vstup je bezpečnostní riziko. Uživatelský vstup může obsahovat škodlivý JavaScript nebo jiné zneužití. Dezinfekce vstupu uživatele je obtížná. Vyhněte se použití `HtmlHelper.Raw` s uživatelským vstupem.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Kód vykreslí následující HTML:

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Bloky kódů holicí strojek

Bloky kódu holicí strojek začínají `@` a jsou uzavřeny . `{}` Na rozdíl od výrazů kód Jazyka C# uvnitř bloků kódu není vykreslen. Bloky kódu a výrazy v zobrazení sdílejí stejný obor a jsou definovány v pořadí:

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

Kód vykreslí následující HTML:

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

V blocích kódu deklarujte [místní funkce](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) se značkami, které slouží jako metody šablonování:

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

Kód vykreslí následující HTML:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a>Implicitní přechody

Výchozí jazyk v bloku kódu je C#, ale stránka Razor může přejít zpět na HTML:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Explicitní oddělený přechod

Chcete-li definovat podsekci bloku kódu, který by měl vykreslit HTML, obklopte znaky pro vykreslování značkou Razor: `<text>`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Tento přístup slouží k vykreslení html, který není obklopen značkou HTML. Bez značky HTML nebo Razor dojde k chybě razor runtime.

Značka `<text>` je užitečná pro řízení mezer při vykreslování obsahu:

* Vykreslí se `<text>` pouze obsah mezi značkou.
* Před nebo za výstupem `<text>` HTML se nezobrazí žádné mezery.

### <a name="explicit-line-transition"></a>Explicitní přechod řádku

Chcete-li vykreslit zbytek celého řádku `@:` jako HTML uvnitř bloku kódu, použijte syntaxi:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Bez `@:` v kódu je generována chyba razor runtime.

Další `@` znaky v souboru Razor může způsobit chyby kompilátoru na příkazy později v bloku. Tyto chyby kompilátoru může být obtížné pochopit, protože skutečné chyby dochází před ohlášenou chybu. Tato chyba je běžné po kombinaci více implicitní/explicitní výrazy do jednoho bloku kódu.

## <a name="control-structures"></a>Řídicí struktury

Řídicí struktury jsou rozšíření matných bloků kódu. Všechny aspekty bloků kódu (přechod na značky, vposlední řadě C#) platí také pro následující struktury:

### <a name="conditionals-if-else-if-else-and-switch"></a>\@Podmínky, pokud, jinak pokud, \@jinak, a přepnout

`@if`při spuštění kódu:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else`a `else if` nevyžadují `@` symbol:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

Následující značky ukazují, jak používat příkaz switch:

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a>Opakování \@pro, \@foreach, \@zatímco, a \@dělat, zatímco

Šablonované HTML lze vykreslit pomocí příkazů řízení opakování. Vykreslení seznamu osob:

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

Jsou podporovány následující příkazy opakování:

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a>Složené \@pomocí

V C# `using` příkaz se používá k zajištění objektu je uvolněn. V Razor, stejný mechanismus se používá k vytvoření HTML pomocníků, které obsahují další obsah. V následujícím kódu pomocníci HTML `<form>` vykreslují značku s příkazem: `@using`

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a>\@zkuste, chytit, konečně

Zpracování výjimek je podobné c#:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>\@Zámek

Holicí strojek má schopnost chránit kritické části pomocí příkazů zámku:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Komentáře

Razor podporuje c# a HTML komentáře:

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

Kód vykreslí následující HTML:

```html
<!-- HTML comment -->
```

Razor komentáře jsou odstraněny serverem před vykreslením webové stránky. Holicí strojek používá `@*  *@` k vymezení komentáře. Následující kód je zakomentován, takže server nevykresluje žádné značky:

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>Direktivy

Razor direktivy jsou reprezentovány implicitními výrazy s vyhrazenými klíčovými slovy za symbolem. `@` Direktiva obvykle mění způsob analýzy zobrazení nebo umožňuje různé funkce.

Pochopení toho, jak Razor generuje kód pro zobrazení usnadňuje pochopení, jak fungují direktivy.

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

Kód generuje třídu podobnou následující:

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

Dále v tomto článku části [Zkontrolujte razor C # třídy generované pro zobrazení](#inspect-the-razor-c-class-generated-for-a-view) vysvětluje, jak zobrazit tuto generovanou třídu.

### <a name="attribute"></a>\@Atribut

Směrnice `@attribute` přidá daný atribut do třídy generované stránky nebo zobrazení. Následující příklad přidá `[Authorize]` atribut:

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a>\@Kód

*Tento scénář platí pouze pro komponenty Razor (.razor).*

Blok `@code` umožňuje [komponentě Razor](xref:blazor/components) přidat členy jazyka C# (pole, vlastnosti a metody) do komponenty:

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

Pro komponenty `@code` Razor je [`@functions`](#functions) alias a `@functions`doporučený nad . Je přípustné `@code` více než jeden blok.

::: moniker-end

### <a name="functions"></a>\@Funkce

Směrnice `@functions` umožňuje přidání c# členy (pole, vlastnosti a metody) do generované třídy:

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

V [komponenty](xref:blazor/components)Razor `@code` `@functions` , použijte přes přidat c# členy.

::: moniker-end

Příklad:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

Kód generuje následující značky HTML:

```html
<div>From method: Hello</div>
```

Následující kód je vygenerovaná třída Razor C#:

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

`@functions`metody slouží jako šablonovací metody, pokud mají značky:

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

Kód vykreslí následující HTML:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a>\@Implementuje

Směrnice `@implements` implementuje rozhraní pro generované třídy.

Následující příklad implementuje <xref:System.IDisposable?displayProperty=fullName> <xref:System.IDisposable.Dispose*> tak, aby metoda může být volána:

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### <a name="inherits"></a>\@Dědí

Směrnice `@inherits` poskytuje úplné řízení třídy, kterou zobrazení dědí:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Následující kód je vlastní typ stránky Razor:

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

Zobrazí `CustomText` se v zobrazení:

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

Kód vykreslí následující HTML:

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model`a `@inherits` lze jej použít ve stejném zobrazení. `@inherits`může být v souboru *_ViewImports.cshtml,* který zobrazení importuje:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

Následující kód je příkladem zobrazení silného typu:

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

Pokudrick@contoso.comje v modelu předáno " "" pohled, zobrazí se následující značky HTML:

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a>\@Nastříkne

Směrnice `@inject` umožňuje Razor Page vložit službu z [kontejneru služby](xref:fundamentals/dependency-injection) do zobrazení. Další informace naleznete v [tématu vkládání závislostí do zobrazení](xref:mvc/views/dependency-injection).

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a>\@Rozložení

*Tento scénář platí pouze pro komponenty Razor (.razor).*

Směrnice `@layout` určuje rozložení komponenty Razor. Součásti rozložení se používají, aby se zabránilo duplikaci kódu a nekonzistenci. Další informace naleznete v tématu <xref:blazor/layouts>.

::: moniker-end

### <a name="model"></a>\@Model

*Tento scénář se vztahuje pouze na zobrazení MVC a Razor Pages (.cshtml).*

Směrnice `@model` určuje typ modelu předaného zobrazení nebo stránce:

```cshtml
@model TypeNameOfModel
```

V aplikaci ASP.NET Core MVC nebo Razor Pages vytvořené pomocí jednotlivých uživatelských účtů obsahuje *zobrazení/účet/Login.cshtml* následující prohlášení modelu:

```cshtml
@model LoginViewModel
```

Vygenerovaná třída `RazorPage<dynamic>`dědí z :

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor zpřístupňuje `Model` vlastnost pro přístup k modelu předané do pohledu:

```cshtml
<div>The Login Email: @Model.Email</div>
```

Směrnice `@model` určuje typ vlastnosti. `Model` Směrnice určuje `T` v `RazorPage<T>` tom, že generované třídy, které je odvozené z zobrazení. Pokud `@model` není zadána směrnice, `Model` vlastnost je `dynamic`typu . Další informace naleznete [v tématu Silné @model typy modelů a klíčové slovo](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### <a name="namespace"></a>\@namespace

Směrnice: `@namespace`

* Nastaví obor názvů třídy generované holicí strojek stránky, Zobrazení MVC nebo Razor komponenty.
* Nastaví kořenové odvozené obory názvů tříd stránek, zobrazení nebo komponent z nejbližšího importovaného souboru ve stromu *adresářů, _ViewImports.cshtml* (zobrazení nebo stránky) nebo *_Imports.razor* (komponenty Razor).

```cshtml
@namespace Your.Namespace.Here
```

Pro příklad Razor Pages zobrazený v následující tabulce:

* Každá stránka importuje *Pages/_ViewImports.cshtml*.
* *Stránky/_ViewImports.cshtml* `@namespace Hello.World`obsahuje .
* Každá stránka `Hello.World` má jako kořen svého oboru názvů.

| stránka                                        | Obor názvů                             |
| ------------------------------------------- | ------------------------------------- |
| *Stránky/Index.cshtml*                        | `Hello.World`                         |
| *Stránky/Další stránky/Page.cshtml*               | `Hello.World.MorePages`               |
| *Stránky/MorePages/EvenMorePages/Page.cshtml* | `Hello.World.MorePages.EvenMorePages` |

Předchozí vztahy platí pro import souborů používaných se zobrazeními MVC a komponentami Razor.

Pokud má více `@namespace` souborů importu direktivu, soubor, který je nejblíže stránce, zobrazení nebo součásti ve stromu adresářů, se použije k nastavení kořenového oboru názvů.

Pokud složka *EvenMorePages* v předchozím příkladu obsahuje `@namespace Another.Planet` soubor importů s (nebo obsahuje soubor *Pages/MorePages/EvenMorePages/Page.cshtml* `@namespace Another.Planet`), výsledek se zobrazí v následující tabulce.

| stránka                                        | Obor názvů               |
| ------------------------------------------- | ----------------------- |
| *Stránky/Index.cshtml*                        | `Hello.World`           |
| *Stránky/Další stránky/Page.cshtml*               | `Hello.World.MorePages` |
| *Stránky/MorePages/EvenMorePages/Page.cshtml* | `Another.Planet`        |

### <a name="page"></a>\@Stránka

::: moniker range=">= aspnetcore-3.0"

Směrnice `@page` má různé účinky v závislosti na typu souboru, kde se zobrazí. Směrnice:

* V souboru *.cshtml* označuje, že soubor je Razor Page. Další informace naleznete [v tématu Vlastní trasy](xref:razor-pages/index#custom-routes) a <xref:razor-pages/index>.
* Určuje, že komponenta Razor by měla zpracovávat požadavky přímo. Další informace naleznete v tématu <xref:blazor/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Direktiva `@page` na prvním řádku souboru *.cshtml* označuje, že soubor je Razor Page. Další informace naleznete v tématu <xref:razor-pages/index>.

::: moniker-end

### <a name="section"></a>\@Oddíl

*Tento scénář se vztahuje pouze na zobrazení MVC a Razor Pages (.cshtml).*

Směrnice `@section` se používá ve spojení s [rozloženími MVC a Razor Pages,](xref:mvc/views/layout) aby zobrazení nebo stránky mohly vykreslovat obsah v různých částech stránky HTML. Další informace naleznete v tématu <xref:mvc/views/layout>.

### <a name="using"></a>\@using

Směrnice `@using` přidá `using` direktivu Jazyka C# do generovaného zobrazení:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

V [komponenty](xref:blazor/components) `@using` Razor , také určuje, které součásti jsou v oboru.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Atributy směrnice

### <a name="attributes"></a>\@Atributy

*Tento scénář platí pouze pro komponenty Razor (.razor).*

`@attributes`umožňuje komponentě vykreslit nedeklarované atributy. Další informace naleznete v tématu <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.

### <a name="bind"></a>\@Vázat

*Tento scénář platí pouze pro komponenty Razor (.razor).*

Datová vazba v součástech `@bind` se provádí s atributem. Další informace naleznete v tématu <xref:blazor/data-binding>.

### <a name="onevent"></a>\@zapnuto{EVENT}

*Tento scénář platí pouze pro komponenty Razor (.razor).*

Holicí strojek poskytuje funkce zpracování událostí pro komponenty. Další informace naleznete v tématu <xref:blazor/event-handling>.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a>\@zapnuto{EVENT}:preventDefault

*Tento scénář platí pouze pro komponenty Razor (.razor).*

Zabrání výchozí akci pro událost.

### <a name="oneventstoppropagation"></a>\@on{EVENT}:stopPropagation

*Tento scénář platí pouze pro komponenty Razor (.razor).*

Zastaví šíření událostí pro událost.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a>\@key

*Tento scénář platí pouze pro komponenty Razor (.razor).*

Atribut `@key` direktiva způsobí, že algoritmus diferenciování komponent zaručuje zachování prvků nebo součástí na základě hodnoty klíče. Další informace naleznete v tématu <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.

### <a name="ref"></a>\@ref

*Tento scénář platí pouze pro komponenty Razor (.razor).*

Odkazy na`@ref`komponenty ( ) poskytují způsob, jak odkazovat na instanci komponenty, takže můžete vydávat příkazy k této instanci. Další informace naleznete v tématu <xref:blazor/components#capture-references-to-components>.

### <a name="typeparam"></a>\@typeparam

*Tento scénář platí pouze pro komponenty Razor (.razor).*

Směrnice `@typeparam` deklaruje parametr obecného typu pro třídu generované součásti. Další informace naleznete v tématu <xref:blazor/templated-components#generic-typed-components>.

::: moniker-end

## <a name="templated-razor-delegates"></a>Šablony Razor delegáti

Šablony razor umožňují definovat fragment uživatelského uživatelského nastavení v následujícím formátu:

```cshtml
@<tag>...</tag>
```

Následující příklad ukazuje, jak určit šablonovaný delegát <xref:System.Func%602>razor jako . [Dynamický typ](/dotnet/csharp/programming-guide/types/using-type-dynamic) je určen pro parametr metody, kterou delegát zapouzdřuje. [Typ objektu](/dotnet/csharp/language-reference/keywords/object) je určen jako vrácená hodnota delegáta. Šablona se používá <xref:System.Collections.Generic.List%601> `Pet` s a, která má `Name` vlastnost.

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

Šablona je vykreslena `pets` s `foreach` dodaným příkazem:

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

Vykreslený výstup:

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

Můžete také zadat vložku šablony jako argument metody. V následujícím příkladu `Repeat` metoda obdrží šablonu Razor. Metoda používá šablonu k vytvoření obsahu HTML s opakováními položek dodaných ze seznamu:

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

Pomocí seznamu domácích zvířat z `Repeat` předchozího příkladu je metoda volána pomocí:

* <xref:System.Collections.Generic.List%601>v `Pet`roce 2015 .
* Počet opakování každého domácího mazlíčka.
* Vsazená šablona, která se použije pro položky seznamu neuspořádaného seznamu.

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

Vykreslený výstup:

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a>Pomocné rutiny značek

*Tento scénář se vztahuje pouze na zobrazení MVC a Razor Pages (.cshtml).*

Existují tři direktivy, které se vztahuje k [Tag Pomocníky](xref:mvc/views/tag-helpers/intro).

| Směrnice | Funkce |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | Zpřístupní pomocné společování značek v zobrazení. |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Odebere pomocné saze značek, které byly dříve přidány ze zobrazení. |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Určuje předponu značky, která povolí podporu pomocníka značek a učiní použití pomocníka značek explicitní. |

## <a name="razor-reserved-keywords"></a>Holicí strojek vyhrazená klíčová slova

### <a name="razor-keywords"></a>Klíčová slova Razor

* (Vyžaduje ASP.NET Core 2.1 nebo novější)
* namespace
*  – funkce
* Dědí
* model
* section
* pomocník (Momentálně není podporovánASP.NET Core)

Razor klíčová slova `@(Razor Keyword)` jsou uvozeny s (například). `@(functions)`

### <a name="c-razor-keywords"></a>C# Razor klíčová slova

* case
* do
* default
* pro
* foreach
* if
* else
* lock
* switch
* vyzkoušení
* Chytit
* finally
* using
* while

C# Razor klíčová slova musí `@(@C# Razor Keyword)` být double-escaped s `@(@case)`(například). První `@` unikne analyzátoru Razor. Druhý `@` unikne analyzátoru Jazyka C#.

### <a name="reserved-keywords-not-used-by-razor"></a>Vyhrazená klíčová slova, která razor nepoužívá

* třída

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a>Kontrola třídy Razor C# generované pro zobrazení

::: moniker range=">= aspnetcore-2.1"

S .NET Core SDK 2.1 nebo novější, [Razor SDK](xref:razor-pages/sdk) zpracovává kompilaci razor souborů. Při vytváření projektu vkořenicí strojek SDK generuje *obj/<build_configuration>/<target_framework_moniker>/Razor* adresář v kořenovém adresáři projektu. Adresářová struktura v adresáři *Razor* zrcadlí adresářovou strukturu projektu.

Zvažte následující adresářovou strukturu v projektu ASP.NET Core 2.1 Razor Pages zaměřeného na rozhraní .NET Core 2.1:

* **Oblasti/**
  * **Admin/**
    * **Stránky/**
      * *Soubor Index.cshtml*
      * *Index.cshtml.cs*
* **Stránky/**
  * **Sdílené/**
    * *Soubor _Layout.cshtml*
  * *_ViewImports.cshtml*
  * *_ViewStart.cshtml*
  * *Soubor Index.cshtml*
  * *Index.cshtml.cs*

Vytvoření projektu v konfiguraci *ladění* poskytuje následující *adresář obj:*

* **obj/**
  * **Ladění/**
    * **netcoreapp2,1/**
      * **Holicí strojek/**
        * **Oblasti/**
          * **Admin/**
            * **Stránky/**
              * *Index.g.cshtml.cs*
        * **Stránky/**
          * **Sdílené/**
            * *_Layout.g.cshtml.cs*
          * *_ViewImports.g.cshtml.cs*
          * *_ViewStart.g.cshtml.cs*
          * *Index.g.cshtml.cs*

Chcete-li zobrazit vygenerovanou třídu pro *pages/Index.cshtml*, otevřete *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Přidejte do projektu ASP.NET Core MVC následující třídu:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

V `Startup.ConfigureServices`, přepsat `RazorTemplateEngine` přidané MVC `CustomTemplateEngine` s třídou:

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Nastavte zarážku `return csharpDocument;` na `CustomTemplateEngine`příkaz . Pokud se spuštění programu zastaví na zarážky, zobrazte hodnotu aplikace `generatedCode`.

![Zobrazení vizualizéru textu generovaného kódu](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Zobrazení vyhledávání a rozlišování malých a velkých písmen

Modul zobrazení Razor provádí vyhledávání zobrazení rozlišující malá a velká písmena. Skutečné vyhledávání je však určeno základním systémem souborů:

* Zdroj založený na souboru:
  * V operačních systémech se systémy souborů bez rozlišování velkých a malých písmen (například windows) jsou vyhledávání zprostředkovatele fyzických souborů rozlišována malá a velká písmena. `return View("Test")` Výsledkem jsou například shody pro */Views/Home/Test.cshtml*, */Views/home/test.cshtml*a jakoukoli jinou variantu krytu.
  * V systémech souborů rozlišujících malá a velká `EmbeddedFileProvider`písmena (například Linux, OSX a s ) jsou vyhledávání rozlišována malá a velká písmena. Konkrétně `return View("Test")` se shoduje například *s parametrem /Views/Home/Test.cshtml*.
* Předkompilovaná zobrazení: S ASP.NET jádrem 2.0 a novějším je vyhledávání předkompilovaných zobrazení ve všech operačních systémech malá a velká písmena. Chování je shodné s chováním zprostředkovatele fyzického souboru v systému Windows. Pokud se dvě předkompilovaná zobrazení liší pouze v případě, výsledek vyhledávání je nedeterministický.

Vývojáři se doporučuje, aby odpovídaly velikosti písmen souborů a názvů adresářů s velikostí:

* Názvy oblastí, kontroleru a akcí.
* Žiletky stránky.

Odpovídající případ zajišťuje nasazení najít jejich zobrazení bez ohledu na základní systém souborů.

## <a name="additional-resources"></a>Další zdroje

[Úvod do ASP.NET webového programování pomocí syntaxe holicího strojku](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) poskytuje mnoho vzorků programování se syntaxí Razor.
