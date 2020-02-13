---
title: Referenční příručka syntaxe Razor pro ASP.NET Core
author: rick-anderson
description: Další informace o syntaxi Razor kód pro vložení do webových stránek kód založený na serveru.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/views/razor
ms.openlocfilehash: 2d47fbc33328ab454616bcabab796df089686d79
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171863"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a>Referenční příručka syntaxe Razor pro ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen)a [Dan Vicarel](https://github.com/Rabadash8820)

Razor je syntaxe značek pro vkládání do webových stránek kód založený na serveru. Syntaxe Razor se skládá z kódu Razor C#a HTML. Soubory obsahující Razor mají obecně příponu *. cshtml* . Razor se nachází také v souborech [součástí Razor](xref:blazor/components) ( *. Razor*).

## <a name="rendering-html"></a>Vykreslování protokolu HTML

Výchozí jazyk Razor je ve formátu HTML. Vykreslování HTML z kód Razor se nijak neliší od vykreslování protokolu HTML ze souboru HTML. Kód HTML v souboru *. cshtml* Razor je vykreslen serverem beze změny.

## <a name="razor-syntax"></a>Syntaxe Razor

Razor podporuje C# a používá symbol `@` k přechodu z formátu HTML na C#. Vyhodnotí Razor C# výrazy a vykreslí je ve výstupu protokolu HTML.

Je-li symbol `@` následovaný [rezervovaným klíčovým slovem Razor](#razor-reserved-keywords), přejde do kódu specifického pro Razor. V opačném případě bude přecházet do prostého C#.

Chcete-li v kódu Razor použít řídicí znak `@`, použijte druhý symbol `@`:

```cshtml
<p>@@Username</p>
```

Kód je vykreslen ve formátu HTML s jedním symbolem `@`:

```html
<p>@Username</p>
```

Atributy HTML a obsah obsahující e-mailové adresy nepovažují symbol `@` jako znak přechodu. E-mailové adresy v následujícím příkladu jsou zůstanou podle analýzy Razor:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>Implicitní výrazy Razor

Implicitní výrazy Razor začínají na C# `@` následovaný kódem:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

S výjimkou klíčového C# slova `await` nesmí implicitní výrazy obsahovat mezery. Pokud C# příkaz má vymazat koncové, může být intermingled mezery:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Implicitní výrazy **nemůžou** obsahovat C# obecné typy, protože znaky uvnitř závorek (`<>`) se interpretují jako značky HTML. Následující kód **není platný:**

```cshtml
<p>@GenericMethod<int>()</p>
```

Předcházející kód vygeneruje chybu kompilátoru podobně jako na jednu z následujících akcí:

* Prvek "int" není uzavřený. Všechny elementy musí být buď samouzavírací nebo koncová značka.
* Nelze převést skupinu metod 'GenericMethod' na nedelegující typ 'object'. Chtěli jste vyvolat metodu? "

Volání obecných metod musí být zabalena do [explicitního výrazu Razor](#explicit-razor-expressions) nebo [bloku kódu Razor](#razor-code-blocks).

## <a name="explicit-razor-expressions"></a>Explicitní výrazy Razor

Explicitní výrazy Razor se skládají z `@` symbolu se vyváženými závorkami. Pokud chcete zobrazit čas poslední týden, se používá následující kód Razor:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Veškerý obsah v `@()` závorkách se vyhodnotí a vykreslí do výstupu.

Obecně implicitní výrazů, je popsáno v předchozí části, nesmí obsahovat mezery. V následujícím kódu není jeden týden odečtena od aktuální čas:

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

Kód vykreslí následující kód HTML:

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Explicitní výrazy můžete použít ke zřetězení text s výsledek výrazu:

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Bez explicitního výrazu se `<p>Age@joe.Age</p>` považuje za e-mailovou adresu a vykreslí se `<p>Age@joe.Age</p>`. Když je zapsán jako explicitní výraz, `<p>Age33</p>` je vykreslen.

Explicitní výrazy lze použít k vykreslení výstupu z obecných metod v souborech *. cshtml* . Následující kód ukazuje, jak chcete-li zobrazit tato chyba dříve způsobené závorkami C# obecný. Kód je zapsán jako explicitní výraz:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Výraz kódování

C#jsou výrazy, které vedou na řetězec kódovaný jazykem HTML. C#výrazy, které se vyhodnotí jako `IHtmlContent`, se vykreslují přímo prostřednictvím `IHtmlContent.WriteTo`. C#výrazy, které nejsou vyhodnoceny jako `IHtmlContent` jsou převedeny na řetězec `ToString` a kódovány před jejich vykreslením.

```cshtml
@("<span>Hello World</span>")
```

Kód vykreslí následující kód HTML:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

Kód HTML je zobrazená v prohlížeči jako:

```html
<span>Hello World</span>
```

`HtmlHelper.Raw` výstup není kódovaný, ale vykresluje se jako kód HTML.

> [!WARNING]
> Používání `HtmlHelper.Raw` v neupraveném uživatelském vstupu je bezpečnostní riziko. Uživatelský vstup může obsahovat další zneužití nebo škodlivý jazyka JavaScript. Sanitaci uživatelský vstup je obtížné. Vyhněte se použití `HtmlHelper.Raw` s uživatelským vstupem.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Kód vykreslí následující kód HTML:

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Bloky kódu Razor

Bloky kódu Razor začínají na `@` a jsou uzavřeny pomocí `{}`. Na rozdíl od výrazy C# kód uvnitř bloků kódu není vykresleno. Bloky kódu a výrazy v zobrazení sdílejí stejný obor a jsou definované v pořadí:

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

Kód vykreslí následující kód HTML:

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

Kód vykreslí následující kód HTML:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a>Implicitní přechody

Je výchozí jazyk v bloku kódu C#, ale stránka Razor můžete přejít zpět na HTML:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Explicitní převod s oddělovači

Chcete-li definovat dílčí oddíl bloku kódu, který by měl vykreslovat kód HTML, uzavřete znaky pro vykreslování pomocí značky `<text>` Razor:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Tuto metodu použijte k vykreslení HTML, který není obklopený značky jazyka HTML. Bez značky jazyka HTML nebo Razor dojde k chybě modulu runtime Razor.

Značka `<text>` je užitečná pro řízení prázdných znaků při vykreslování obsahu:

* Vykreslí se jenom obsah mezi `<text>` značkou.
* Žádný prázdný znak před nebo za `<text>` značka se zobrazí ve výstupu HTML.

### <a name="explicit-line-transition"></a>Explicitní přechod řádku

Chcete-li vykreslit zbytek celého řádku jako HTML v bloku kódu, použijte `@:` syntaxi:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Bez `@:` v kódu je vygenerována chyba modulu runtime Razor.

Nadbytečné `@` znaky v souboru Razor mohou způsobit chyby kompilátoru v příkazech později v bloku. Tyto chyby kompilátoru může být obtížné pochopit, protože Skutečná chyba předchází oznámenou chybu. K této chybě dochází po kombinování více implicitního/explicitního výrazů v jednom bloku kódu.

## <a name="control-structures"></a>Řídicí struktury

Řídicí struktury jsou rozšíření bloky kódu. Všechny aspekty bloky kódu (Přechod na značky vložené C#) platí také pro následující struktury:

### <a name="conditionals-if-else-if-else-and-switch"></a>Podmíněné \@if, else if, else a \@Switch

`@if` ovládací prvky při spuštění kódu:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else` a `else if` nevyžadují symbol `@`:

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

Následující kód ukazuje použití příkazu switch:

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

### <a name="looping-for-foreach-while-and-do-while"></a>Opakování \@pro, \@foreach, \@while a \@

Bez vizuálního vzhledu HTML lze vykreslit s opakování ve smyčce řídicí příkazy. Pokud chcete zobrazit seznam lidí:

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

Podporují se následující příkazy opakování:

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

### <a name="compound-using"></a>Složené \@using

V C#nástroji se k zajištění uvolnění objektu používá příkaz `using`. V prostředí Razor stejný mechanismus slouží k vytvoření pomocné rutiny HTML, které obsahují další obsah. V následujícím kódu pomocník HTML vykresluje značku `<form>` pomocí příkazu `@using`:

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a>\@try, catch, finally

Zpracování výjimek je podobný C#:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>\@zámek

Razor má schopnost chránit kritické oddíly s příkazy uzamčení:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Komentáře

Podporuje Razor C# a komentáře HTML:

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

Kód vykreslí následující kód HTML:

```html
<!-- HTML comment -->
```

Komentáře syntaxe Razor jsou odebrána serverem, než se zobrazí webová stránka. Razor používá `@*  *@` k vymezení komentářů. Následující kód je zakomentovaný, tak server nevykreslí žádné značky:

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

Direktivy Razor jsou reprezentovány implicitními výrazy s vyhrazenými klíčovými slovy za symbolem `@`. Direktivu obvykle mění způsob zobrazení je analyzován nebo jinou funkci povolí.

Vysvětlení způsobu, jakým Razor generuje kód pro zobrazení usnadňuje pochopení fungování direktivy.

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

Kód vygeneruje třídu podobný následujícímu:

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

Později v tomto článku oddíl [kontroluje třídu Razor C# generovanou pro zobrazení](#inspect-the-razor-c-class-generated-for-a-view) vysvětluje, jak zobrazit tuto generovanou třídu.

### <a name="attribute"></a>atribut \@

Direktiva `@attribute` přidá daný atribut třídě vygenerované stránky nebo zobrazení. Následující příklad přidá atribut `[Authorize]`:

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a>kód \@

*Tento scénář platí pouze pro součásti Razor (. Razor).*

`@code` blok umožňuje [komponentě Razor](xref:blazor/components) přidat C# členy (pole, vlastnosti a metody) do komponenty:

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

Pro součásti Razor je `@code` aliasem [`@functions`](#functions) a doporučuje se nad `@functions`. Je přípustný více než jeden blok `@code`.

::: moniker-end

### <a name="functions"></a>funkce \@

Direktiva `@functions` umožňuje přidat C# členy (pole, vlastnosti a metody) do generované třídy:

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

V [součástech Razor](xref:blazor/components)použijte `@code` přes `@functions` k přidání C# členů.

::: moniker-end

Například:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

Kód generuje následující kód HTML:

```html
<div>From method: Hello</div>
```

Následující kód je vygenerovaný Razor C# třídy:

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

Kód vykreslí následující kód HTML:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a>\@implementuje

Direktiva `@implements` implementuje rozhraní pro generovanou třídu.

Následující příklad implementuje <xref:System.IDisposable?displayProperty=fullName> tak, aby mohla být volána metoda <xref:System.IDisposable.Dispose*>:

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

### <a name="inherits"></a>\@dědí

Direktiva `@inherits` poskytuje úplnou kontrolu nad třídou, kterou zobrazení dědí:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Následující kód je vlastní typ stránky Razor:

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

`CustomText` se zobrazí v zobrazení:

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

Kód vykreslí následující kód HTML:

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model` a `@inherits` lze použít ve stejném zobrazení. `@inherits` může být v souboru *_ViewImports. cshtml* , který zobrazení importuje:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

Následující kód je příkladem zobrazení se silnými typy:

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

Pokud je v modelu předána možnostrick@contoso.com, zobrazení generuje následující kód HTML:

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a>\@vkládání

Direktiva `@inject` umožňuje, aby stránka Razor mohla vložit službu z [kontejneru služby](xref:fundamentals/dependency-injection) do zobrazení. Další informace najdete v tématu [vkládání závislostí do zobrazení](xref:mvc/views/dependency-injection).

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a>rozložení \@

*Tento scénář platí pouze pro součásti Razor (. Razor).*

Direktiva `@layout` určuje rozložení součásti Razor. Komponenty rozložení se používají k zamezení Duplikace kódu a nekonzistenci. Další informace naleznete v tématu <xref:blazor/layouts>.

::: moniker-end

### <a name="model"></a>model \@

*Tento scénář se vztahuje pouze na zobrazení MVC a Razor Pages (. cshtml).*

Direktiva `@model` určuje typ modelu předaného zobrazení nebo stránce:

```cshtml
@model TypeNameOfModel
```

V ASP.NET Core MVC nebo aplikace Razor Pages vytvořené pomocí individuálních uživatelských účtů, *zobrazení/účet/přihlášení. cshtml* obsahuje následující deklaraci modelu:

```cshtml
@model LoginViewModel
```

Třída vygenerovaná z `RazorPage<dynamic>`dědí:

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor zpřístupňuje vlastnost `Model` pro přístup k modelu předanému zobrazení:

```cshtml
<div>The Login Email: @Model.Email</div>
```

Direktiva `@model` určuje typ vlastnosti `Model`. Direktiva určuje `T` v `RazorPage<T>`, ze které byla vygenerována třída, ze které je odvozeno zobrazení. Pokud není zadána direktiva `@model`, je vlastnost `Model` typu `dynamic`. Další informace naleznete v tématu [modely silného typu a klíčové slovo @model](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### <a name="namespace"></a>obor názvů \@

Direktiva `@namespace`:

* Nastaví obor názvů třídy vygenerované stránky Razor, zobrazení MVC nebo komponenty Razor.
* Nastaví kořenové obory názvů pro třídy stránky, zobrazení nebo komponenty z nejbližšího souboru importu ve stromové struktuře, *_ViewImports. cshtml* (zobrazení nebo stránky) nebo *_Imports. Razor* (komponenty Razor).

```cshtml
@namespace Your.Namespace.Here
```

Pro Razor Pages příklad zobrazený v následující tabulce:

* Každá stránka importuje *stránky/_ViewImports. cshtml*.
* *Pages/_ViewImports. cshtml* obsahuje `@namespace Hello.World`.
* Každá stránka má `Hello.World` jako kořen oboru názvů IT.

| Stránka                                        | Obor názvů                             |
| ------------------------------------------- | ------------------------------------- |
| *Pages/index. cshtml*                        | `Hello.World`                         |
| *Pages/MorePages/Page. cshtml*               | `Hello.World.MorePages`               |
| *Pages/MorePages/EvenMorePages/Page. cshtml* | `Hello.World.MorePages.EvenMorePages` |

Předchozí relace platí pro soubory importu používané s zobrazeními MVC a komponentami Razor.

Pokud má několik importových souborů direktivu `@namespace`, soubor, který je nejblíže stránce, zobrazení nebo komponentě v adresářovém stromu, slouží k nastavení kořenového oboru názvů.

Pokud má složka *EvenMorePages* v předchozím příkladu soubor Imports s `@namespace Another.Planet` (nebo soubor *Pages/MorePages/EvenMorePages/Page. cshtml* obsahuje `@namespace Another.Planet`), výsledek je zobrazen v následující tabulce.

| Stránka                                        | Obor názvů               |
| ------------------------------------------- | ----------------------- |
| *Pages/index. cshtml*                        | `Hello.World`           |
| *Pages/MorePages/Page. cshtml*               | `Hello.World.MorePages` |
| *Pages/MorePages/EvenMorePages/Page. cshtml* | `Another.Planet`        |

### <a name="page"></a>Stránka \@

::: moniker range=">= aspnetcore-3.0"

Direktiva `@page` má různé účinky v závislosti na typu souboru, ve kterém se zobrazí. Direktiva:

* V v souboru *. cshtml* znamená, že soubor je stránka Razor. Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes) a <xref:razor-pages/index>.
* Určuje, že komponenta Razor by měla zpracovávat požadavky přímo. Další informace naleznete v tématu <xref:blazor/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Direktiva `@page` na prvním řádku souboru *. cshtml* znamená, že soubor je stránka Razor. Další informace naleznete v tématu <xref:razor-pages/index>.

::: moniker-end

### <a name="section"></a>\@oddíl

*Tento scénář se vztahuje pouze na zobrazení MVC a Razor Pages (. cshtml).*

Direktiva `@section` se používá ve spojení s [MVC a Razor Pages rozloženími](xref:mvc/views/layout) k tomu, aby zobrazení nebo stránky vygenerovaly obsah v různých částech stránky HTML. Další informace naleznete v tématu <xref:mvc/views/layout>.

### <a name="using"></a>\@pomocí

Direktiva `@using` přidá do C# generovaného zobrazení direktivu `using`:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

V [součástech Razor](xref:blazor/components)`@using` také určuje, které součásti jsou v oboru.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Atributy direktiv

### <a name="attributes"></a>atributy \@

*Tento scénář platí pouze pro součásti Razor (. Razor).*

`@attributes` umožňuje komponentě vykreslovat nedeklarované atributy. Další informace naleznete v tématu <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.

### <a name="bind"></a>vazba \@

*Tento scénář platí pouze pro součásti Razor (. Razor).*

Datovou vazbu v součástech lze provést pomocí atributu `@bind`. Další informace naleznete v tématu <xref:blazor/components#data-binding>.

### <a name="onevent"></a>\@v události {EVENT}

*Tento scénář platí pouze pro součásti Razor (. Razor).*

Razor poskytuje funkce pro zpracování událostí pro součásti. Další informace naleznete v tématu <xref:blazor/components#event-handling>.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a>\@události {EVENT}:p reventDefault

*Tento scénář platí pouze pro součásti Razor (. Razor).*

Zabrání výchozí akci pro událost.

### <a name="oneventstoppropagation"></a>\@v události {EVENT}: stopPropagation

*Tento scénář platí pouze pro součásti Razor (. Razor).*

Zastaví šíření události pro událost.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a>\@klíč

*Tento scénář platí pouze pro součásti Razor (. Razor).*

Atribut direktivy `@key` způsobuje rozdíl mezi komponentami, které zajišťují uchovávání prvků nebo komponent na základě hodnoty klíče. Další informace naleznete v tématu <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.

### <a name="ref"></a>\@odkaz

*Tento scénář platí pouze pro součásti Razor (. Razor).*

Odkazy na součásti (`@ref`) poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance. Další informace naleznete v tématu <xref:blazor/components#capture-references-to-components>.

### <a name="typeparam"></a>\@typeparam

*Tento scénář platí pouze pro součásti Razor (. Razor).*

Direktiva `@typeparam` deklaruje parametr obecného typu pro generovanou třídu komponenty. Další informace naleznete v tématu <xref:blazor/components#generic-typed-components>.

::: moniker-end

## <a name="templated-razor-delegates"></a>Šablony Razor delegátů

Šablony Razor umožňují definovat fragment uživatelského rozhraní v následujícím formátu:

```cshtml
@<tag>...</tag>
```

Následující příklad ukazuje, jak zadat delegáta Razor s šablonou jako <xref:System.Func%602>. [Dynamický typ](/dotnet/csharp/programming-guide/types/using-type-dynamic) je zadán pro parametr metody, kterou delegát zapouzdřuje. [Typ objektu](/dotnet/csharp/language-reference/keywords/object) je zadán jako návratová hodnota delegáta. Šablona se používá s <xref:System.Collections.Generic.List%601> `Pet`, která má vlastnost `Name`.

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

Šablona je vykreslena pomocí `pets` dodaného příkazem `foreach`:

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

Vykresleného výstupu:

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

Šablony Razor vložené lze také zadat jako argument pro metodu. V následujícím příkladu metoda `Repeat` obdrží šablonu Razor. Metoda používá šablony k vytvoření HTML obsah s opakování zadaný ze seznamu položek:

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

Pomocí seznamu domácích zvířat z předchozího příkladu je metoda `Repeat` volána s:

* <xref:System.Collections.Generic.List%601> `Pet`.
* Počet opakování každou mazlíčků.
* Vložená šablona použitá pro položky seznamu neuspořádaný seznam.

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

Vykresleného výstupu:

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

*Tento scénář se vztahuje pouze na zobrazení MVC a Razor Pages (. cshtml).*

Existují tři direktivy, které se týkají [pomocníků značek](xref:mvc/views/tag-helpers/intro).

| – Direktiva | Funkce |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | Zpřístupní pomocných rutin značek k zobrazení. |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Odebere ze zobrazení přidali dříve pomocných rutin značek. |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Určuje předponu značky k povolení podpory pomocné rutiny značky a aby explicitní použití pomocné rutiny značky. |

## <a name="razor-reserved-keywords"></a>Razor vyhrazená klíčová slova

### <a name="razor-keywords"></a>Klíčová slova Razor

* Page (vyžaduje ASP.NET Core 2,1 nebo novější)
* Obor názvů
* – funkce
* Dědí
* model
* section
* pomocné rutiny (aktuálně se nepodporuje ASP.NET Core)

Klíčová slova Razor jsou uvozená pomocí `@(Razor Keyword)` (například `@(functions)`).

### <a name="c-razor-keywords"></a>C#Klíčová slova Razor

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
* catch
* finally
* using
* while

C#Klíčová slova Razor musí mít dvakrát uvození řídicím znakem `@(@C# Razor Keyword)` (například `@(@case)`). První `@` řídí analyzátor Razor. Druhý `@` řídí C# analyzátor.

### <a name="reserved-keywords-not-used-by-razor"></a>Vyhrazená klíčová slova nepoužívá Razor

* třída

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a>Zkontrolovat syntaxi Razor C# třída vygenerovaná pro zobrazení

::: moniker range=">= aspnetcore-2.1"

V .NET Core SDK 2,1 nebo novější sadě [SDK Razor](xref:razor-pages/sdk) zpracovává kompilaci souborů Razor. Při sestavování projektu sada Razor SDK generuje v kořenu projektu *build_configuration > </< target_framework_moniker >* adresář. Adresářová struktura v adresáři *Razor* zrcadlí strukturu adresáře projektu.

Vezměte v úvahu následující adresářovou strukturu v projektu aplikace ASP.NET Core 2.1 Razor Pages cílí na .NET Core 2.1:

* **Místa**
  * **Správ**
    * **Stránky**
      * *Index. cshtml*
      * *Index.cshtml.cs*
* **Stránky**
  * **Sdíleného**
    * *_Layout. cshtml*
  * *_ViewImports. cshtml*
  * *_ViewStart. cshtml*
  * *Index. cshtml*
  * *Index.cshtml.cs*

Sestavení projektu v konfiguraci *ladění* má za následek následující adresář *obj* :

* **objektu**
  * **Ladí**
    * **netcoreapp 2.1/**
      * **Syntaxi**
        * **Místa**
          * **Správ**
            * **Stránky**
              * *Index.g.cshtml.cs*
        * **Stránky**
          * **Sdíleného**
            * *_Layout. g. cshtml. cs*
          * *_ViewImports. g. cshtml. cs*
          * *_ViewStart. g. cshtml. cs*
          * *Index.g.cshtml.cs*

Chcete-li zobrazit vygenerovanou třídu pro *pages/index. cshtml*, otevřete *obj/Debug/Netcoreapp 2.1/Razor/pages/index. g. cshtml. cs*.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Přidejte následující třídu do projektu ASP.NET Core MVC:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

V `Startup.ConfigureServices`přepište `RazorTemplateEngine` přidaných pomocí MVC pomocí třídy `CustomTemplateEngine`:

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Nastavte zarážku na `return csharpDocument;` příkazu `CustomTemplateEngine`. Když se provádění programu zastaví na zarážce, zobrazí se hodnota `generatedCode`.

![Text Visualizer zobrazení generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Zobrazení vyhledávání a rozlišování velikosti písmen

Zobrazovací modul Razor provádí velká a malá písmena vyhledávání pro zobrazení. Nicméně skutečné vyhledávání je určeno podkladový systém souborů:

* Na základě zdrojového souboru:
  * V operačních systémech se systémy souborů malá a velká písmena (například Windows) fyzický soubor poskytovatele vyhledávání jsou malá a velká písmena. Například `return View("Test")` výsledky odpovídají pro */views/Home/test.cshtml*, */views/Home/test.cshtml*a jakékoliv jiné varianty velikosti písmen.
  * V systémech souborů s rozlišováním velkých a malých písmen (například Linux, OSX a s `EmbeddedFileProvider`) se při hledání rozlišují malá a velká písmena. Například `return View("Test")` konkrétně odpovídá */views/Home/test.cshtml*.
* Předkompilované zobrazení: pomocí ASP.NET Core 2.0 a vyšší, hledání předkompilované zobrazení velká a malá písmena na všechny operační systémy. Chování se shoduje s chování zprostředkovatele fyzického souboru ve Windows. Pokud se zobrazeními předkompilované liší pouze v případě, výsledek vyhledávání je Nedeterministický.

Vývojáři nepodnikovým tak, aby odpovídaly malých a velkých písmen názvů použití malých a velkých souborů a adresářů:

* Názvy oblastí, kontroleru a akce.
* Stránky Razor.

Odpovídající případ zajistí, že pro nasazení své názory, bez ohledu na podkladový systém souborů.
