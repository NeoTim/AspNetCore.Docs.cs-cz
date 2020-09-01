---
title: Referenční dokumentace syntaxe Razor pro ASP.NET Core
author: rick-anderson
description: Přečtěte si o Razor syntaxi značek pro vkládání kódu založeného na serveru na webové stránky.
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/views/razor
ms.openlocfilehash: 9c2bbd2d463af8a2ea7db716d01bf1436338ea77
ms.sourcegitcommit: cd861463faf44956855e3c4b3669483bbc4a7463
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2020
ms.locfileid: "89101358"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a>Razor Referenční informace k syntaxi pro ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)a [Dan Vicarel](https://github.com/Rabadash8820)

Razor je syntaxe značek pro vložení kódu založeného na serveru na webové stránky. RazorSyntaxe se skládá z Razor značek, C# a HTML. Soubory, Razor které obsahují obecně, mají příponu *. cshtml* . Razorse nachází také v souborech [ Razor komponent](xref:blazor/components/index) (*. Razor*).

## <a name="rendering-html"></a>Vykreslování kódu HTML

Výchozí Razor jazyk je HTML. Vykreslování HTML ze Razor značek se neliší od vykreslování HTML ze souboru HTML. Kód HTML v souborech *. cshtml* Razor je vykreslen serverem beze změny.

## <a name="no-locrazor-syntax"></a>Razor syntaktick

Razor podporuje jazyk C# a používá `@` symbol k přechodu z formátu HTML do C#. Razor vyhodnotí výrazy jazyka C# a vykresluje je ve výstupu HTML.

`@`Je-li symbol následován [ Razor vyhrazeným klíčovým slovem](#razor-reserved-keywords), přechází na Razor konkrétní značku. V opačném případě přejde do jednoduchého jazyka C#.

Chcete-li `@` v kódu řídicí znak Razor , použijte druhý `@` symbol:

```cshtml
<p>@@Username</p>
```

Kód je vykreslen ve formátu HTML s jedním `@` symbolem:

```html
<p>@Username</p>
```

Atributy HTML a obsah obsahující e-mailové adresy se symbol nepovažují `@` jako znak přechodu. E-mailové adresy v následujícím příkladu nejsou vyretušované Razor analýzou:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a>Implicitní Razor výrazy

Implicitní Razor výrazy začínají `@` následováním kódu jazyka C#:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

S výjimkou `await` klíčového slova jazyka C# nesmí implicitní výrazy obsahovat mezery. Pokud má příkaz jazyka C# jasný konec, mezery mohou být smíšené:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Implicitní výrazy **nemůžou** obsahovat obecné typy C#, protože znaky uvnitř závorek ( `<>` ) jsou interpretovány jako značka jazyka HTML. Následující kód **není platný:**

```cshtml
<p>@GenericMethod<int>()</p>
```

Předchozí kód vygeneruje chybu kompilátoru podobný jednomu z následujících:

* Element int se nezavřel. Všechny elementy musí být buď samouzavírací, nebo musí mít stejnou koncovou značku.
* Nejde převést skupinu metod ' GenericMethod ' na Nedelegovaný typ ' Object '. Chtěli jste vyvolat metodu?

Volání obecných metod musí být zabalena do [explicitního Razor výrazu](#explicit-razor-expressions) nebo [ Razor bloku kódu](#razor-code-blocks).

## <a name="explicit-no-locrazor-expressions"></a>Explicitní Razor výrazy

Explicitní Razor výrazy se skládají ze `@` symbolu s vyrovnanými závorkami. Chcete-li vykreslit čas posledního týdne, Razor je použit následující kód:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Veškerý obsah v `@()` závorkách je vyhodnocen a vykreslen do výstupu.

Implicitní výrazy popsané v předchozí části všeobecně nemůžou obsahovat mezery. V následujícím kódu se jeden týden odečte od aktuálního času:

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

Kód vykresluje následující kód HTML:

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Explicitní výrazy lze použít k zřetězení textu s výsledkem výrazu:

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Bez explicitního výrazu `<p>Age@joe.Age</p>` je zpracována jako e-mailová adresa a `<p>Age@joe.Age</p>` je vykreslena. Je-li zapsán jako explicitní výraz, `<p>Age33</p>` je vykreslen.

Explicitní výrazy lze použít k vykreslení výstupu z obecných metod v souborech *. cshtml* . Následující kód ukazuje, jak opravit chybu zobrazenou dříve, způsobenou závorkami obecných v jazyce C#. Kód je zapsán jako explicitní výraz:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Kódování výrazu

Výrazy jazyka C#, které jsou vyhodnoceny jako řetězec, jsou kódovány HTML. Výrazy jazyka C#, které se vyhodnotí, se `IHtmlContent` vykreslují přímo prostřednictvím `IHtmlContent.WriteTo` . Výrazy jazyka C#, které nejsou vyhodnoceny, `IHtmlContent` jsou převedeny na řetězec pomocí `ToString` a kódovány před jejich vykreslením.

```cshtml
@("<span>Hello World</span>")
```

Předchozí kód vykresluje následující kód HTML:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

HTML se zobrazí v prohlížeči jako prostý text:

&lt;rozpětí &gt; Hello World &lt; /span&gt;

`HtmlHelper.Raw` výstup není kódovaný, ale vykresluje se jako kód HTML.

> [!WARNING]
> Použití `HtmlHelper.Raw` na neupraveném vstupu uživatele představuje bezpečnostní riziko. Vstup uživatele může obsahovat škodlivý JavaScript nebo jiné zneužití. Upravení vstupu uživatele je obtížné. Nepoužívejte `HtmlHelper.Raw` s uživatelským vstupem.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Kód vykresluje následující kód HTML:

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a>Razor bloky kódu

Razor bloky kódu začínají na `@` a jsou uzavřeny v `{}` . Na rozdíl od výrazů není kód C# uvnitř bloků kódu vykreslen. Bloky kódu a výrazy v zobrazení sdílejí stejný obor a jsou definovány v pořadí:

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

Kód vykresluje následující kód HTML:

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

V blocích kódu deklarujte [místní funkce](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) pomocí značek, které budou sloužit jako metody šablonování:

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

Kód vykresluje následující kód HTML:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a>Implicitní přechody

Výchozí jazyk v bloku kódu je C#, ale Razor Stránka může přejít zpět na kód HTML:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Explicitně oddělený přechod

Chcete-li definovat dílčí oddíl bloku kódu, který by měl vykreslovat kód HTML, uzavřete znaky pro vykreslování se Razor `<text>` značkou:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Pomocí tohoto přístupu můžete vykreslit HTML, které není obklopené značkou HTML. Bez HTML nebo Razor značky Razor dojde k chybě modulu runtime.

`<text>`Značka je užitečná pro řízení prázdných znaků při vykreslování obsahu:

* Vykreslí se jenom obsah mezi `<text>` značkou.
* Před nebo po `<text>` značce se ve výstupu HTML nezobrazí žádné prázdné znaky.

### <a name="explicit-line-transition"></a>Explicitní přechod řádku

Chcete-li vykreslit zbytek celého řádku jako HTML v bloku kódu, použijte `@:` syntaxi:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Bez `@:` kódu v Razor je vygenerována Běhová chyba.

Nadbytečné `@` znaky v Razor souboru mohou způsobit chyby kompilátoru v příkazech později v bloku. Tyto chyby kompilátoru mohou být obtížné pochopit, protože před nahlášenou chybou dojde k aktuální chybě. Tato chyba je společná po Zkombinování více implicitních nebo explicitních výrazů do jednoho bloku kódu.

## <a name="control-structures"></a>Řídicí struktury

Řídicí struktury jsou rozšíření bloků kódu. Všechny aspekty bloků kódu (přechod na kód, vložené C#) platí také pro následující struktury:

### <a name="conditionals-if-else-if-else-and-switch"></a>Podmíněné výrazy `@if, else if, else, and @switch`

`@if` ovládací prvky při spuštění kódu:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else` a `else if` nevyžadují `@` symbol:

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

Následující kód ukazuje, jak použít příkaz switch:

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

### <a name="looping-for-foreach-while-and-do-while"></a>Opakování `@for, @foreach, @while, and @do while`

HTML s šablonou lze vykreslit pomocí příkazů pro opakování. Vykreslit seznam osob:

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

Podporovány jsou následující příkazy smyček:

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

### <a name="compound-using"></a>Kombinovanou `@using`

V jazyce C# se `using` k zajištění uvolnění objektu používá příkaz. V nástroji se Razor stejný mechanismus používá k vytvoření pomocníků HTML, které obsahují další obsah. V následujícím kódu pomocník HTML vykresluje `<form>` značku s `@using` příkazem:

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### `@try, catch, finally`

Zpracování výjimek je podobné jazyku C#:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

Razor má možnost chránit kritické oddíly pomocí příkazů Lock:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Komentáře

Razor podporuje komentáře jazyka C# a HTML:

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

Kód vykresluje následující kód HTML:

```html
<!-- HTML comment -->
```

Razor před vykreslením webové stránky jsou komentáře odstraněny serverem. Razor používá `@*  *@` se k vymezení komentářů. Následující kód je zakomentován, takže server nevykresluje žádné značky:

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

Razor direktivy jsou reprezentovány implicitními výrazy s vyhrazenými klíčovými slovy za `@` symbolem. Direktiva obvykle mění způsob, jakým se analyzuje zobrazení, nebo umožňuje různé funkce.

Porozumění způsobu, jakým Razor generuje kód pro zobrazení, usnadňuje pochopení, jak direktivy fungují.

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

Kód generuje třídu podobnou této:

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

Později v tomto článku část [kontroluje Razor třídu jazyka C# vygenerovanou pro zobrazení](#inspect-the-razor-c-class-generated-for-a-view) vysvětluje, jak zobrazit tuto generovanou třídu.

### `@attribute`

`@attribute`Direktiva přidá daný atribut třídě vygenerované stránky nebo zobrazení. Následující příklad přidá `[Authorize]` atribut:

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*

`@code`Blok umožňuje [ Razor komponentě](xref:blazor/components/index) přidat členy jazyka C# (pole, vlastnosti a metody) do komponenty:

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

Pro Razor součásti `@code` je alias [`@functions`](#functions) a doporučený `@functions` . Je přípustný více než jeden `@code` blok.

::: moniker-end

### `@functions`

`@functions`Direktiva umožňuje přidat členy jazyka C# (pole, vlastnosti a metody) do generované třídy:

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

V [ Razor komponentách](xref:blazor/components/index)použijte `@code` více než `@functions` k přidání členů jazyka C#.

::: moniker-end

Příklad:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

Kód generuje následující kód HTML:

```html
<div>From method: Hello</div>
```

Následující kód je vygenerovaná Razor třída C#:

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

`@functions` metody slouží jako metody šablonování, pokud mají značky:

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

Kód vykresluje následující kód HTML:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

`@implements`Direktiva implementuje rozhraní pro generovanou třídu.

Následující příklad implementuje <xref:System.IDisposable?displayProperty=fullName> , aby <xref:System.IDisposable.Dispose*> mohla být metoda volána:

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

### `@inherits`

`@inherits`Direktiva poskytuje úplnou kontrolu nad třídou, kterou zobrazení dědí:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Následující kód je vlastní Razor typ stránky:

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

`CustomText`Je zobrazen v zobrazení:

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

Kód vykresluje následující kód HTML:

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model` a `@inherits` lze jej použít ve stejném zobrazení. `@inherits` může být v souboru *_ViewImports. cshtml* , který zobrazení importuje:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

Následující kód je příkladem zobrazení silného typu:

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

Pokud rick@contoso.com je v modelu předána možnost "", zobrazení generuje následující kód HTML:

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

`@inject`Direktiva umožňuje Razor stránce Vložit službu z [kontejneru služby](xref:fundamentals/dependency-injection) do zobrazení. Další informace najdete v tématu [vkládání závislostí do zobrazení](xref:mvc/views/dependency-injection).

::: moniker range=">= aspnetcore-3.0"

### `@layout`

*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*

`@layout`Direktiva určuje rozložení pro Razor komponentu. Komponenty rozložení se používají k zamezení Duplikace kódu a nekonzistenci. Další informace naleznete v tématu <xref:blazor/layouts>.

::: moniker-end

### `@model`

*Tento scénář se vztahuje pouze na zobrazení a Razor stránky MVC (. cshtml).*

`@model`Direktiva určuje typ modelu předaného na zobrazení nebo stránku:

```cshtml
@model TypeNameOfModel
```

V aplikaci ASP.NET Core MVC nebo Razor stránky vytvořené pomocí individuálních uživatelských účtů, *zobrazení/účet/přihlášení. cshtml* obsahuje následující deklaraci modelu:

```cshtml
@model LoginViewModel
```

Třída, ze které vygenerovala dědění `RazorPage<dynamic>` :

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor zpřístupní `Model` vlastnost pro přístup k modelu předanému zobrazení:

```cshtml
<div>The Login Email: @Model.Email</div>
```

`@model`Direktiva určuje typ `Model` Vlastnosti. Direktiva Určuje `T` , v jakém `RazorPage<T>` vygenerované třídě je odvozeno zobrazení. Pokud `@model` není zadána direktiva, `Model` je vlastnost typu `dynamic` . Další informace naleznete v tématu [modely silného typu a @model klíčové slovo](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### `@namespace`

`@namespace`Direktiva:

* Nastaví obor názvů třídy vygenerované Razor stránky, zobrazení MVC nebo Razor součásti.
* Nastaví kořenové obory názvů pro třídy stránky, zobrazení nebo komponenty z nejbližšího souboru importu ve stromové struktuře adresáře *_ViewImports. cshtml* (zobrazení nebo stránky) nebo *_Imports. Razor* ( Razor komponenty).

```cshtml
@namespace Your.Namespace.Here
```

Pro Razor příklad stránek zobrazených v následující tabulce:

* Každá stránka importuje *stránky/_ViewImports. cshtml*.
* *Stránky/_ViewImports. cshtml* obsahuje `@namespace Hello.World` .
* Každá stránka má `Hello.World` jako kořen oboru názvů IT.

| Stránka                                        | Obor názvů                             |
| ------------------------------------------- | ------------------------------------- |
| *Pages/index. cshtml*                        | `Hello.World`                         |
| *Pages/MorePages/Page. cshtml*               | `Hello.World.MorePages`               |
| *Pages/MorePages/EvenMorePages/Page. cshtml* | `Hello.World.MorePages.EvenMorePages` |

Předchozí relace platí pro import souborů používaných s zobrazeními a Razor komponentami MVC.

Pokud má více souborů importu `@namespace` direktivu, soubor, který je nejblíže stránce, zobrazení nebo komponentě v adresářovém stromu, slouží k nastavení kořenového oboru názvů.

Pokud má složka *EvenMorePages* v předchozím příkladu soubor Imports s `@namespace Another.Planet` (nebo soubor *Pages/MorePages/EvenMorePages/Page. cshtml* obsahuje `@namespace Another.Planet` ), výsledek je zobrazen v následující tabulce.

| Stránka                                        | Obor názvů               |
| ------------------------------------------- | ----------------------- |
| *Pages/index. cshtml*                        | `Hello.World`           |
| *Pages/MorePages/Page. cshtml*               | `Hello.World.MorePages` |
| *Pages/MorePages/EvenMorePages/Page. cshtml* | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

`@page`Direktiva má jiné účinky v závislosti na typu souboru, ve kterém se zobrazí. Direktiva:

* V souboru *. cshtml* znamená, že soubor je Razor Stránka. Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes) a <xref:razor-pages/index> .
* Určuje, že Razor komponenta má zpracovávat požadavky přímo. Další informace naleznete v tématu <xref:blazor/fundamentals/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

`@page`Direktiva na prvním řádku souboru *. cshtml* znamená, že soubor je Razor Stránka. Další informace naleznete v tématu <xref:razor-pages/index>.

::: moniker-end

### `@section`

*Tento scénář se vztahuje pouze na zobrazení a Razor stránky MVC (. cshtml).*

`@section`Direktiva se používá společně s [ Razor rozloženími MVC a stránky](xref:mvc/views/layout) k tomu, aby zobrazení nebo stránky vygenerovaly obsah v různých částech stránky HTML. Další informace naleznete v tématu <xref:mvc/views/layout>.

### `@using`

`@using`Direktiva přidá `using` direktivu jazyka C# do generovaného zobrazení:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

V [ Razor součástech](xref:blazor/components/index) `@using` také určuje, které součásti jsou v oboru.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Atributy direktiv

Razor atributy direktiv jsou reprezentovány implicitními výrazy s vyhrazenými klíčovými slovy za `@` symbolem. Atribut direktivy obvykle mění způsob, jakým je prvek analyzován, nebo umožňuje různé funkce.

### `@attributes`

*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*

`@attributes` umožňuje komponentě vykreslovat nedeklarované atributy. Další informace naleznete v tématu <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.

### `@bind`

*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*

Datové vazby v součástech se provádí s `@bind` atributem. Další informace naleznete v tématu <xref:blazor/components/data-binding>.

### `@on{EVENT}`

*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*

Razor poskytuje funkce pro zpracování událostí pro součásti. Další informace naleznete v tématu <xref:blazor/components/event-handling>.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*

Zabrání výchozí akci pro událost.

### `@on{EVENT}:stopPropagation`

*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*

Zastaví šíření události pro událost.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*

`@key`Atribut direktiva způsobí, že rozdílový algoritmus komponent garantuje zachování prvků nebo komponent na základě hodnoty klíče. Další informace naleznete v tématu <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.

### `@ref`

*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*

Odkazy na součásti ( `@ref` ) poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance. Další informace naleznete v tématu <xref:blazor/components/index#capture-references-to-components>.

### `@typeparam`

*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*

`@typeparam`Direktiva deklaruje parametr obecného typu pro generovanou třídu komponenty. Další informace naleznete v tématu <xref:blazor/components/templated-components#generic-typed-components>.

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a>Zástupci přidaných šablon Razor

Razor šablony umožňují definovat fragment uživatelského rozhraní v následujícím formátu:

```cshtml
@<tag>...</tag>
```

Následující příklad ukazuje, jak zadat delegáta s šablonou Razor jako <xref:System.Func%602> . [Dynamický typ](/dotnet/csharp/programming-guide/types/using-type-dynamic) je zadán pro parametr metody, kterou delegát zapouzdřuje. [Typ objektu](/dotnet/csharp/language-reference/keywords/object) je zadán jako návratová hodnota delegáta. Šablona se používá s <xref:System.Collections.Generic.List%601> `Pet` vlastností, která má `Name` vlastnost.

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

Šablona je vykreslena s `pets` dodaným `foreach` příkazem:

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

Vloženou šablonu můžete také dodat Razor jako argument metodě. V následujícím příkladu `Repeat` metoda obdrží Razor šablonu. Metoda používá šablonu k vytvoření obsahu HTML s opakováním položek dodaných ze seznamu:

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

Pomocí seznamu domácích zvířat z předchozího příkladu `Repeat` je metoda volána s:

* <xref:System.Collections.Generic.List%601> z `Pet` .
* Počet opakování každé PET.
* Vložená šablona, která se má použít pro položky seznamu neuspořádaného seznamu

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

*Tento scénář se vztahuje pouze na zobrazení a Razor stránky MVC (. cshtml).*

Existují tři direktivy, které se týkají [pomocníků značek](xref:mvc/views/tag-helpers/intro).

| Směrnici | Funkce |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | Zpřístupní pomocníkům značek zobrazení. |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Odebere pomocníky značek dříve přidané ze zobrazení. |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Určuje předponu značky pro povolení podpory značek pomocníka a k explicitnímu použití pomocníka značek. |

## <a name="no-locrazor-reserved-keywords"></a>Razor vyhrazená klíčová slova

### <a name="no-locrazor-keywords"></a>Razor klíčov

* `page` (Vyžaduje ASP.NET Core 2,1 nebo novější)
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* `helper` (Aktuálně není podporováno ASP.NET Core)

Razor Klíčová slova jsou uvozena znakem `@(Razor Keyword)` (například `@(functions)` ).

### <a name="c-no-locrazor-keywords"></a>RazorKlíčová slova jazyka C#

* `case`
* `do`
* `default`
* `for`
* `foreach`
* `if`
* `else`
* `lock`
* `switch`
* `try`
* `catch`
* `finally`
* `using`
* `while`

RazorKlíčová slova jazyka C# musí mít dvojité uvození řídicím znakem `@(@C# Razor Keyword)` (například `@(@case)` ). První `@` řídí Razor analyzátor. Druhý `@` řídí analyzátor jazyka C#.

### <a name="reserved-keywords-not-used-by-no-locrazor"></a>Vyhrazená klíčová slova, která nepoužívá Razor

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a>Kontrola Razor třídy jazyka C# vygenerované pro zobrazení

::: moniker range=">= aspnetcore-2.1"

Pomocí .NET Core SDK 2,1 nebo novější [ Razor sada SDK](xref:razor-pages/sdk) zpracovává kompilaci Razor souborů. Při sestavování projektu Razor generuje sada SDK objekt *obj/<build_configuration>/<target_framework_moniker>/ Razor * adresář v kořenovém adresáři projektu. Adresářová struktura v *Razor* adresáři zrcadlí strukturu adresáře projektu.

Vezměte v úvahu následující adresářovou strukturu v projektu stránky ASP.NET Core 2,1 s Razor cílením na rozhraní .NET Core 2,1:

```
 Areas/
   Admin/
     Pages/
       Index.cshtml
       Index.cshtml.cs
 Pages/
   Shared/
     _Layout.cshtml
   _ViewImports.cshtml
   _ViewStart.cshtml
   Index.cshtml
   Index.cshtml.cs
  ```

Sestavení projektu v konfiguraci *ladění* má za následek následující adresář *obj* :

```
 obj/
   Debug/
     netcoreapp2.1/
       Razor/
         Areas/
           Admin/
             Pages/
               Index.g.cshtml.cs
         Pages/
           Shared/
             _Layout.g.cshtml.cs
           _ViewImports.g.cshtml.cs
           _ViewStart.g.cshtml.cs
           Index.g.cshtml.cs
```

Chcete-li zobrazit vygenerovanou třídu pro *pages/index. cshtml*, otevřete *obj/ladění/netcoreapp 2.1/ Razor /pages/index.g.cshtml.cs*.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Přidejte následující třídu do projektu ASP.NET Core MVC:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

V aplikaci `Startup.ConfigureServices` přepište `RazorTemplateEngine` přidanou `CustomTemplateEngine` třídou MVC pomocí třídy:

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Nastavte zarážku na `return csharpDocument;` příkazu `CustomTemplateEngine` . Když se provádění programu zastaví na zarážce, zobrazte hodnotu `generatedCode` .

![Zobrazení Vizualizér textu pro generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Zobrazit hledání a rozlišovat velká a malá písmena

RazorModul zobrazení provádí pro zobrazení vyhledávání citlivá na velká a malá písmena. Skutečné vyhledávání je však určeno základním systémem souborů:

* Zdroj založený na souborech:
  * V operačních systémech se systémy souborů bez rozlišení velkých a malých písmen (například Windows) se u hledání fyzického zprostředkovatele souborů nerozlišují malá a velká písmena. Výsledkem je například `return View("Test")` shoda pro */views/Home/test.cshtml*, */views/Home/test.cshtml*a jakékoliv jiné varianty velikosti písmen.
  * V systémech souborů s rozlišováním velkých a malých písmen (například Linux, OSX a with `EmbeddedFileProvider` ) se při hledání rozlišují malá a velká písmena. `return View("Test")`Konkrétně například odpovídá */views/Home/test.cshtml*.
* Předkompilovaná zobrazení: u ASP.NET Core 2,0 a novějších, hledání předkompilovaných zobrazení rozlišuje velká a malá písmena ve všech operačních systémech. Chování je stejné jako chování poskytovatele fyzického souboru ve Windows. Pokud se dvě Předkompilovaná zobrazení liší pouze v případě, výsledek hledání není deterministický.

Vývojářům doporučujeme, aby se shodovaly s velkými písmeny názvů souborů a adresářů na velká a malá písmena:

* Názvy oblastí, kontrolérů a akcí.
* Razor Stránky.

V případě, že se rozlišuje velká a malá písmena, zajistíte jejich zobrazení bez ohledu na základní systém souborů.

## <a name="additional-resources"></a>Další zdroje informací

[Seznámení s ASP.NET webovým programováním pomocí Razor Syntaxe](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) poskytuje mnoho ukázek programování s Razor syntaxí.
