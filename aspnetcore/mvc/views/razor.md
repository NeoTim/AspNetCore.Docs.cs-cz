---
title: Razor syntax reference for ASP.NET Core
author: rick-anderson
description: Learn about Razor markup syntax for embedding server-based code into webpages.
ms.author: riande
ms.date: 09/28/2019
uid: mvc/views/razor
ms.openlocfilehash: d8d686c23ea61950947798f213c9846058f1812e
ms.sourcegitcommit: 4818385c3cfe0805e15138a2c1785b62deeaab90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73896900"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a>Razor syntax reference for ASP.NET Core

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)

Razor is a markup syntax for embedding server-based code into webpages. The Razor syntax consists of Razor markup, C#, and HTML. Files containing Razor generally have a *.cshtml* file extension. Razor is also found in [Razor components](xref:blazor/components) files ( *.razor*).

## <a name="rendering-html"></a>Rendering HTML

The default Razor language is HTML. Rendering HTML from Razor markup is no different than rendering HTML from an HTML file. HTML markup in *.cshtml* Razor files is rendered by the server unchanged.

## <a name="razor-syntax"></a>Syntaxe Razor

Razor supports C# and uses the `@` symbol to transition from HTML to C#. Razor evaluates C# expressions and renders them in the HTML output.

When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup. Otherwise, it transitions into plain C#.

To escape an `@` symbol in Razor markup, use a second `@` symbol:

```cshtml
<p>@@Username</p>
```

The code is rendered in HTML with a single `@` symbol:

```html
<p>@Username</p>
```

HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character. The email addresses in the following example are untouched by Razor parsing:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>Implicit Razor expressions

Implicit Razor expressions start with `@` followed by C# code:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

With the exception of the C# `await` keyword, implicit expressions must not contain spaces. If the C# statement has a clear ending, spaces can be intermingled:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag. The following code is **not** valid:

```cshtml
<p>@GenericMethod<int>()</p>
```

The preceding code generates a compiler error similar to one of the following:

* The "int" element wasn't closed. All elements must be either self-closing or have a matching end tag.
* Cannot convert method group 'GenericMethod' to non-delegate type 'object'. Did you intend to invoke the method?`

Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).

## <a name="explicit-razor-expressions"></a>Explicit Razor expressions

Explicit Razor expressions consist of an `@` symbol with balanced parenthesis. To render last week's time, the following Razor markup is used:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Any content within the `@()` parenthesis is evaluated and rendered to the output.

Implicit expressions, described in the previous section, generally can't contain spaces. In the following code, one week isn't subtracted from the current time:

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

Kód vykresluje následující kód HTML:

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Explicit expressions can be used to concatenate text with an expression result:

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered. When written as an explicit expression, `<p>Age33</p>` is rendered.

Explicit expressions can be used to render output from generic methods in *.cshtml* files. The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic. The code is written as an explicit expression:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Expression encoding

C# expressions that evaluate to a string are HTML encoded. C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`. C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.

```cshtml
@("<span>Hello World</span>")
```

Kód vykresluje následující kód HTML:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

The HTML is shown in the browser as:

```
<span>Hello World</span>
```

`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.

> [!WARNING]
> Using `HtmlHelper.Raw` on unsanitized user input is a security risk. User input might contain malicious JavaScript or other exploits. Sanitizing user input is difficult. Avoid using `HtmlHelper.Raw` with user input.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Kód vykresluje následující kód HTML:

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Razor code blocks

Razor code blocks start with `@` and are enclosed by `{}`. Unlike expressions, C# code inside code blocks isn't rendered. Code blocks and expressions in a view share the same scope and are defined in order:

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

In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:

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

### <a name="implicit-transitions"></a>Implicit transitions

The default language in a code block is C#, but the Razor Page can transition back to HTML:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Explicit delimited transition

To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Use this approach to render HTML that isn't surrounded by an HTML tag. Without an HTML or Razor tag, a Razor runtime error occurs.

The `<text>` tag is useful to control whitespace when rendering content:

* Only the content between the `<text>` tag is rendered.
* No whitespace before or after the `<text>` tag appears in the HTML output.

### <a name="explicit-line-transition"></a>Explicit line transition

To render the rest of an entire line as HTML inside a code block, use `@:` syntax:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Bez `@:` v kódu je vygenerována chyba modulu runtime Razor.

Nadbytečné `@` znaky v souboru Razor mohou způsobit chyby kompilátoru v příkazech později v bloku. Tyto chyby kompilátoru mohou být obtížné pochopit, protože před nahlášenou chybou dojde k aktuální chybě. Tato chyba je společná po Zkombinování více implicitních nebo explicitních výrazů do jednoho bloku kódu.

## <a name="control-structures"></a>Řídicí struktury

Řídicí struktury jsou rozšíření bloků kódu. Všechny aspekty bloků kódu (přechod na označení, vloženo C#) platí také pro následující struktury:

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

### <a name="looping-for-foreach-while-and-do-while"></a>Opakování \@pro, \@foreach, \@while a \@

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

### <a name="compound-using"></a>Složené \@using

V C#nástroji se k zajištění uvolnění objektu používá příkaz `using`. V Razor je stejný mechanismus použit k vytváření pomocníků HTML, které obsahují další obsah. V následujícím kódu pomocník HTML vykresluje značku `<form>` pomocí příkazu `@using`:

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

Zpracování výjimek je podobné jako C#:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>\@zámek

Razor má možnost chránit kritické oddíly pomocí příkazů Lock:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Komentáře

Razor podporuje C# a komentáře HTML:

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

Před vykreslením webové stránky jsou komentáře Razor odebrány serverem. Razor používá `@*  *@` k vymezení komentářů. Následující kód je zakomentován, takže server nevykresluje žádné značky:

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

Direktivy Razor jsou reprezentovány implicitními výrazy s vyhrazenými klíčovými slovy za symbolem `@`. Direktiva obvykle mění způsob, jakým se analyzuje zobrazení, nebo umožňuje různé funkce.

Pochopení, jak Razor generuje kód pro zobrazení, usnadňuje pochopení, jak direktivy fungují.

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

Později v tomto článku oddíl [kontroluje třídu Razor C# generovanou pro zobrazení](#inspect-the-razor-c-class-generated-for-a-view) vysvětluje, jak zobrazit tuto generovanou třídu.

### <a name="attribute"></a>atribut \@

Direktiva `@attribute` přidá daný atribut třídě vygenerované stránky nebo zobrazení. Následující příklad přidá atribut `[Authorize]`:

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a>kód \@

*This scenario only applies to Razor components (.razor).*

`@code` blok umožňuje [komponentě Razor](xref:blazor/components) přidat C# členy (pole, vlastnosti a metody) do komponenty:

```cshtml
@code {
    // C# members (fields, properties, and methods)
}
```

Pro součásti Razor je `@code` aliasem [@functions](#functions) a doporučuje se nad `@functions`. Je přípustný více než jeden blok `@code`.

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

Příklad:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

Kód generuje následující kód HTML:

```html
<div>From method: Hello</div>
```

Následující kód je vygenerovaná třída Razor C# :

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

Kód vykresluje následující kód HTML:

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model` a `@inherits` lze použít ve stejném zobrazení. `@inherits` může být v souboru *_ViewImports. cshtml* , který zobrazení importuje:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

Následující kód je příkladem zobrazení silného typu:

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

*This scenario only applies to Razor components (.razor).*

Direktiva `@layout` určuje rozložení součásti Razor. Komponenty rozložení se používají k zamezení Duplikace kódu a nekonzistenci. Další informace najdete v tématu <xref:blazor/layouts>.

::: moniker-end

### <a name="model"></a>model \@

*This scenario only applies to MVC views and Razor Pages (.cshtml).*

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

| Page                                        | Obor názvů                             |
| ------------------------------------------- | ------------------------------------- |
| *Pages/Index.cshtml*                        | `Hello.World`                         |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages`               |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Hello.World.MorePages.EvenMorePages` |

Předchozí relace platí pro soubory importu používané s zobrazeními MVC a komponentami Razor.

Pokud má několik importových souborů direktivu `@namespace`, soubor, který je nejblíže stránce, zobrazení nebo komponentě v adresářovém stromu, slouží k nastavení kořenového oboru názvů.

Pokud má složka *EvenMorePages* v předchozím příkladu soubor Imports s `@namespace Another.Planet` (nebo soubor *Pages/MorePages/EvenMorePages/Page. cshtml* obsahuje `@namespace Another.Planet`), výsledek je zobrazen v následující tabulce.

| Page                                        | Obor názvů               |
| ------------------------------------------- | ----------------------- |
| *Pages/Index.cshtml*                        | `Hello.World`           |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages` |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Another.Planet`        |

### <a name="page"></a>\@page

::: moniker range=">= aspnetcore-3.0"

The `@page` directive has different effects depending on the type of the file where it appears. The directive:

* In in a *.cshtml* file indicates that the file is a Razor Page. For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.
* Specifies that a Razor component should handle requests directly. Další informace najdete v tématu <xref:blazor/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page. Další informace najdete v tématu <xref:razor-pages/index>.

::: moniker-end

### <a name="section"></a>\@section

*This scenario only applies to MVC views and Razor Pages (.cshtml).*

The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page. Další informace najdete v tématu <xref:mvc/views/layout>.

### <a name="using"></a>\@using

The `@using` directive adds the C# `using` directive to the generated view:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Directive attributes

### <a name="attributes"></a>\@attributes

*This scenario only applies to Razor components (.razor).*

`@attributes` allows a component to render non-declared attributes. Další informace najdete v tématu <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.

### <a name="bind"></a>\@bind

*This scenario only applies to Razor components (.razor).*

Data binding in components is accomplished with the `@bind` attribute. Další informace najdete v tématu <xref:blazor/components#data-binding>.

### <a name="onevent"></a>\@on{event}

*This scenario only applies to Razor components (.razor).*

Razor provides event handling features for components. Další informace najdete v tématu <xref:blazor/components#event-handling>.

### <a name="key"></a>\@key

*This scenario only applies to Razor components (.razor).*

The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value. Další informace najdete v tématu <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.

### <a name="ref"></a>\@ref

*This scenario only applies to Razor components (.razor).*

Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance. Další informace najdete v tématu <xref:blazor/components#capture-references-to-components>.

### <a name="typeparam"></a>\@typeparam

*This scenario only applies to Razor components (.razor).*

The `@typeparam` directive declares a generic type parameter for the generated component class. Další informace najdete v tématu <xref:blazor/components#generic-typed-components>.

::: moniker-end

## <a name="templated-razor-delegates"></a>Templated Razor delegates

Razor templates allow you to define a UI snippet with the following format:

```cshtml
@<tag>...</tag>
```

The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>. The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates. An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate. The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.

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

The template is rendered with `pets` supplied by a `foreach` statement:

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

Rendered output:

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

You can also supply an inline Razor template as an argument to a method. In the following example, the `Repeat` method receives a Razor template. The method uses the template to produce HTML content with repeats of items supplied from a list:

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

Using the list of pets from the prior example, the `Repeat` method is called with:

* <xref:System.Collections.Generic.List%601> of `Pet`.
* Number of times to repeat each pet.
* Inline template to use for the list items of an unordered list.

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

Rendered output:

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

*This scenario only applies to MVC views and Razor Pages (.cshtml).*

There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).

| Directive | Funkce |
| --------- | -------- |
| [@addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) | Makes Tag Helpers available to a view. |
| [@removeTagHelper](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Removes Tag Helpers previously added from a view. |
| [@tagHelperPrefix](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit. |

## <a name="razor-reserved-keywords"></a>Razor reserved keywords

### <a name="razor-keywords"></a>Razor keywords

* page (Requires ASP.NET Core 2.1 or later)
* – obor názvů
* – funkce
* inherits
* model
* section
* helper (Not currently supported by ASP.NET Core)

Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).

### <a name="c-razor-keywords"></a>C# Razor keywords

* case
* do
* default
* pro
* foreach
* if
* else
* lock
* – přepínač
* try
* catch
* finally
* používání
* while

C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`). The first `@` escapes the Razor parser. The second `@` escapes the C# parser.

### <a name="reserved-keywords-not-used-by-razor"></a>Reserved keywords not used by Razor

* třída

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a>Inspect the Razor C# class generated for a view

::: moniker range=">= aspnetcore-2.1"

With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files. When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root. The directory structure within the *Razor* directory mirrors the project's directory structure.

Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:

* **Areas/**
  * **Admin/**
    * **Pages/**
      * *Index.cshtml*
      * *Index.cshtml.cs*
* **Pages/**
  * **Shared/**
    * *_Layout.cshtml*
  * *_ViewImports.cshtml*
  * *_ViewStart.cshtml*
  * *Index.cshtml*
  * *Index.cshtml.cs*

Building the project in *Debug* configuration yields the following *obj* directory:

* **obj/**
  * **Debug/**
    * **netcoreapp2.1/**
      * **Razor/**
        * **Areas/**
          * **Admin/**
            * **Pages/**
              * *Index.g.cshtml.cs*
        * **Pages/**
          * **Shared/**
            * *_Layout.g.cshtml.cs*
          * *_ViewImports.g.cshtml.cs*
          * *_ViewStart.g.cshtml.cs*
          * *Index.g.cshtml.cs*

To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Add the following class to the ASP.NET Core MVC project:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`. When program execution stops at the breakpoint, view the value of `generatedCode`.

![Text Visualizer view of generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>View lookups and case sensitivity

The Razor view engine performs case-sensitive lookups for views. However, the actual lookup is determined by the underlying file system:

* File based source:
  * On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive. For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.
  * On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive. For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.
* Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems. The behavior is identical to physical file provider's behavior on Windows. If two precompiled views differ only in case, the result of lookup is non-deterministic.

Developers are encouraged to match the casing of file and directory names to the casing of:

* Area, controller, and action names.
* Razor Pages.

Matching case ensures the deployments find their views regardless of the underlying file system.
