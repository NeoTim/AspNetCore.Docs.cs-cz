---
title: Referenční příručka syntaxe Razor pro ASP.NET Core
author: rick-anderson
description: Další informace o syntaxi Razor kód pro vložení do webových stránek kód založený na serveru.
ms.author: riande
ms.date: 10/26/2018
uid: mvc/views/razor
ms.openlocfilehash: 53d4dc608fbfd45bcc015a3af83f5d87f86c7f15
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327362"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a>Referenční příručka syntaxe Razor pro ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylora MÜLLENA](https://twitter.com/ntaylormullen), a [Dan Vicarel](https://github.com/Rabadash8820)

Razor je syntaxe značek pro vkládání do webových stránek kód založený na serveru. Syntaxe Razor se skládá z kódu Razor C#a HTML. Máte soubory, které obvykle obsahují Razor *.cshtml* příponu souboru.

## <a name="rendering-html"></a>Vykreslování protokolu HTML

Výchozí jazyk Razor je ve formátu HTML. Vykreslování HTML z kód Razor se nijak neliší od vykreslování protokolu HTML ze souboru HTML. Značka jazyka HTML v *.cshtml* souborech Razor je vykreslen metodou serveru beze změny.

## <a name="razor-syntax"></a>Syntaxe Razor

Podporuje Razor C# a používá `@` symbol, který má přechod z HTML na C#. Vyhodnotí Razor C# výrazy a vykreslí je ve výstupu protokolu HTML.

Když `@` následuje symbol [Razor rezervované klíčové slovo](#razor-reserved-keywords), přechází do kódu specifické pro syntaxi Razor. V opačném případě bude přecházet do prostého C#.

Řídicí `@` symbol v kódu Razor, použijte druhý `@` symbolů:

```cshtml
<p>@@Username</p>
```

Kód se vykreslí v HTML pomocí jediného `@` symbolů:

```html
<p>@Username</p>
```

Atributy HTML a obsah, který obsahuje e-mailové adresy nejsou s pracovat `@` symbol jako znak přechod. E-mailové adresy v následujícím příkladu jsou zůstanou podle analýzy Razor:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>Implicitní výrazy Razor

Implicitní Razor výrazy začínat `@` následovaný C# kódu:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

S výjimkou produktů C# `await` – klíčové slovo, implicitní výrazů nesmí obsahovat mezery. Pokud C# příkaz má vymazat koncové, může být intermingled mezery:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Implicitní výrazy **nelze** obsahovat C# obecných typů, jako znaky uvnitř hranatých závorek (`<>`) jsou interpretovány jako značku jazyka HTML. Následující kód je **není** platné:

```cshtml
<p>@GenericMethod<int>()</p>
```

Předcházející kód vygeneruje chybu kompilátoru podobně jako na jednu z následujících akcí:

* Prvek "int" není uzavřený. Všechny elementy musí být buď samouzavírací nebo koncová značka.
* Nelze převést skupinu metod 'GenericMethod' na nedelegující typ 'object'. Chtěli jste vyvolat metodu? "

Volání obecné metody musí být uzavřen do [explicitní výraz Razor](#explicit-razor-expressions) nebo [blok kódu Razor](#razor-code-blocks).

## <a name="explicit-razor-expressions"></a>Explicitní výrazy Razor

Explicitní syntaxe Razor výrazů se skládají z `@` symbol vyvážené závorky. Pokud chcete zobrazit čas poslední týden, se používá následující kód Razor:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Veškerý obsah v rámci `@()` závorky se vyhodnotí a vykreslí do výstupu.

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

Bez explicitní výraz `<p>Age@joe.Age</p>` je považován za e-mailovou adresu a `<p>Age@joe.Age</p>` vykreslením. Při zápisu jako explicitní výraz `<p>Age33</p>` vykreslením.

Explicitní výrazy můžete použít k vykreslení výstup z obecných metod v *.cshtml* soubory. Následující kód ukazuje, jak chcete-li zobrazit tato chyba dříve způsobené závorkami C# obecný. Kód je zapsán jako explicitní výraz:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Výraz kódování

C#jsou výrazy, které vedou na řetězec kódovaný jazykem HTML. C#výrazy, které vedou k `IHtmlContent` jsou generovány přímo pomocí `IHtmlContent.WriteTo`. C#výrazy, které není vyhodnocen na `IHtmlContent` jsou převedeny na řetězec pomocí `ToString` a kódováním než se zobrazí.

```cshtml
@("<span>Hello World</span>")
```

Kód vykreslí následující kód HTML:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

Kód HTML je zobrazená v prohlížeči jako:

```
<span>Hello World</span>
```

`HtmlHelper.Raw` výstup není kódovaný ale se vykresluje jako značka jazyka HTML.

> [!WARNING]
> Pomocí `HtmlHelper.Raw` unsanitized uživatele vstup představuje bezpečnostní riziko. Uživatelský vstup může obsahovat další zneužití nebo škodlivý jazyka JavaScript. Sanitaci uživatelský vstup je obtížné. Vyhněte se použití `HtmlHelper.Raw` s uživatelským vstupem.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Kód vykreslí následující kód HTML:

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Bloky kódu Razor

Bloky kódu Razor začínat `@` a jsou uzavřeny ve `{}`. Na rozdíl od výrazy C# kód uvnitř bloků kódu není vykresleno. Bloky kódu a výrazy v zobrazení sdílejí stejný obor a jsou definované v pořadí:

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

### <a name="implicit-transitions"></a>Implicitní přechody

Je výchozí jazyk v bloku kódu C#, ale stránka Razor můžete přejít zpět na HTML:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Explicitní převod s oddělovači

Pokud chcete definovat dílčí část objektu bloku kódu, který vykreslovat kód HTML, obklopit fragmentem syntaxi Razor znaků pro vykreslování  **\<text >** značky:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Tuto metodu použijte k vykreslení HTML, který není obklopený značky jazyka HTML. Bez značky jazyka HTML nebo Razor dojde k chybě modulu runtime Razor.

**\<Text >** značka je vhodné pro řízení prázdné znaky, při vykreslování obsahu:

* Pouze obsah mezi  **\<text >** je vykreslen.
* Žádné prázdné znaky před nebo po  **\<text >** značky se zobrazí ve výstupu protokolu HTML.

### <a name="explicit-line-transition-with-"></a>Explicitní řádek přechodu se @:

K vykreslení rest celý řádek jako kód HTML uvnitř bloku kódu, použijte `@:` syntaxi:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Bez `@:` v kódu, je vygenerována chyba za běhu Razor.

Upozornění: Navíc `@` znaky v souboru Razor mohou způsobit chyby kompilátoru příkazy v bloku. Tyto chyby kompilátoru může být obtížné pochopit, protože Skutečná chyba předchází oznámenou chybu. K této chybě dochází po kombinování více implicitního/explicitního výrazů v jednom bloku kódu.

## <a name="control-structures"></a>Řídicí struktury

Řídicí struktury jsou rozšíření bloky kódu. Všechny aspekty bloky kódu (Přechod na značky vložené C#) platí také pro následující struktury:

### <a name="conditionals-if-else-if-else-and-switch"></a>Podmíněné výrazy @if, else if, else, a @switch

`@if` ovládací prvky, pokud kód se spustí:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else` a `else if` nevyžadují `@` symbolů:

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

### <a name="looping-for-foreach-while-and-do-while"></a>Opakování ve smyčce @for, @foreach, @while, a @do při

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

### <a name="compound-using"></a>Složené @using

V C#, `using` prohlášení se používá k zajištění uvolněn objekt. V prostředí Razor stejný mechanismus slouží k vytvoření pomocné rutiny HTML, které obsahují další obsah. V následujícím kódu pomocných rutin HTML, vykreslení značky formuláře s `@using` – příkaz:

```cshtml
@using (Html.BeginForm())
{
    <div>
        email:
        <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

Akce na úrovni oboru, můžete to provést pomocí [pomocných rutin značek](xref:mvc/views/tag-helpers/intro).

### <a name="try-catch-finally"></a>@try, catch, finally

Zpracování výjimek je podobný C#:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>@lock

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

Komentáře syntaxe Razor jsou odebrána serverem, než se zobrazí webová stránka. Používá syntaxi Razor `@*  *@` pro vymezení komentáře. Následující kód je zakomentovaný, tak server nevykreslí žádné značky:

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

Direktivy Razor jsou reprezentovány implicitní výrazy s následující vyhrazená klíčová slova `@` symbol. Direktivu obvykle mění způsob zobrazení je analyzován nebo jinou funkci povolí.

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

Dále v tomto článku v části [Zkontrolujte syntaxi Razor C# třída vygenerovaná pro zobrazení](#inspect-the-razor-c-class-generated-for-a-view) vysvětluje, jak zobrazit tento generované třídy.

<a name="using"></a>

### <a name="using"></a>@using

`@using` Direktivy přidává C# `using` direktiv generované zobrazení:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

### <a name="model"></a>@model

`@model` Direktiva Určuje typ modelu předána do zobrazení:

```cshtml
@model TypeNameOfModel
```

V aplikaci ASP.NET Core MVC vytvořené pomocí jednotlivých uživatelských účtů *Views/Account/Login.cshtml* zobrazení obsahuje následující deklaraci modelu:

```cshtml
@model LoginViewModel
```

Dědí třídu vygenerovanou z `RazorPage<dynamic>`:

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Zpřístupňuje Razor `Model` předána do zobrazení, vlastnosti pro přístup k modelu:

```cshtml
<div>The Login Email: @Model.Email</div>
```

`@model` Direktiva Určuje typ této vlastnosti. Direktiva Určuje, `T` v `RazorPage<T>` , že generované třídy, která zobrazení je odvozena z. Pokud `@model` – direktiva není zadán, `Model` vlastnost je typu `dynamic`. Z kontroleru zobrazení je předána hodnota modelu. Další informace najdete v tématu [se silnými typy modelů a &commat;– klíčové slovo modelu](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### <a name="inherits"></a>@inherits

`@inherits` – Direktiva poskytuje plnou kontrolu nad třída dědí zobrazení:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Následující kód je vlastní typ stránky Razor:

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

`CustomText` Se zobrazí v zobrazení:

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

Kód vykreslí následující kód HTML:

```html
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

 `@model` a `@inherits` je možné ve stejném zobrazení. `@inherits` může být v *_ViewImports.cshtml* soubor, který importuje zobrazení:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

Následující kód je příkladem zobrazení se silnými typy:

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

Pokud "rick@contoso.com" je předán zobrazení v modelu, generuje následující kód HTML:

```html
<div>The Login Email: rick@contoso.com</div>
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

### <a name="inject"></a>@inject

`@inject` Direktiva umožňuje vložit služby z stránky Razor [kontejneru služby](xref:fundamentals/dependency-injection) do zobrazení. Další informace najdete v tématu [injektáž závislostí do zobrazení](xref:mvc/views/dependency-injection).

### <a name="functions"></a>@functions

`@functions` Direktiva umožňuje stránky Razor a přidat C# blok kódu do zobrazení:

```cshtml
@functions { // C# Code }
```

Příklad:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

Kód generuje následující kód HTML:

```html
<div>From method: Hello</div>
```

Následující kód je vygenerovaný Razor C# třídy:

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### <a name="section"></a>@section

`@section` – Direktiva se používá ve spojení s [rozložení](xref:mvc/views/layout) povolit zobrazení k vykreslení obsahu v různých částech stránky HTML. Další informace najdete v tématu [oddíly](xref:mvc/views/layout#layout-sections-label).

## <a name="templated-razor-delegates"></a>Šablony Razor delegátů

Šablony Razor umožňují definovat fragment uživatelského rozhraní v následujícím formátu:

```cshtml
@<tag>...</tag>
```

Následující příklad ukazuje, jak zadat bez vizuálního vzhledu Razor delegáta jako <xref:System.Func%602>. [Dynamického typu](/dotnet/csharp/programming-guide/types/using-type-dynamic) je zadaná pro parametr metody, která zapouzdřuje delegáta. [Typ objektu](/dotnet/csharp/language-reference/keywords/object) je zadán jako návratový typ delegáta. Šablona se používá s <xref:System.Collections.Generic.List%601> z `Pet` , který má `Name` vlastnost.

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

Šablona je vykreslen pomocí `pets` poskytnutých `foreach` – příkaz:

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

Šablony Razor vložené lze také zadat jako argument pro metodu. V následujícím příkladu `Repeat` metoda obdrží šablona Razor. Metoda používá šablony k vytvoření HTML obsah s opakování zadaný ze seznamu položek:

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

Pomocí seznam mazlíčků z předchozího příkladu `Repeat` metoda je volána pomocí:

* <xref:System.Collections.Generic.List%601> z `Pet`.
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

Existují tři direktivy, které se týkají [pomocných rutin značek](xref:mvc/views/tag-helpers/intro).

| – Direktiva | Funkce |
| --------- | -------- |
| [&commat;addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) | Zpřístupní pomocných rutin značek k zobrazení. |
| [&commat;removeTagHelper](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Odebere ze zobrazení přidali dříve pomocných rutin značek. |
| [&commat;tagHelperPrefix](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Určuje předponu značky k povolení podpory pomocné rutiny značky a aby explicitní použití pomocné rutiny značky. |

## <a name="razor-reserved-keywords"></a>Razor vyhrazená klíčová slova

### <a name="razor-keywords"></a>Klíčová slova Razor

* stránka (vyžaduje ASP.NET Core 2.0 a novější)
* – obor názvů
* – funkce
* Dědí
* model
* section
* pomocné rutiny (aktuálně se nepodporuje ASP.NET Core)

Klíčová slova Razor jsou uvozeny řídicími znaky s `@(Razor Keyword)` (například `@(functions)`).

### <a name="c-razor-keywords"></a>C#Klíčová slova Razor

* case
* do
* default
* pro
* foreach
* if
* else
* lock
* – přepínač
* Zkuste
* catch
* finally
* používání
* while

C#Klíčová slova Razor musí být uvozena double s `@(@C# Razor Keyword)` (například `@(@case)`). První `@` řídicí sekvence analyzátor Razor. Druhá `@` řídicí sekvence C# analyzátor.

### <a name="reserved-keywords-not-used-by-razor"></a>Vyhrazená klíčová slova nepoužívá Razor

* třída

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a>Zkontrolovat syntaxi Razor C# třída vygenerovaná pro zobrazení

::: moniker range=">= aspnetcore-2.1"

S .NET Core SDK 2.1 nebo novější [Razor SDK](xref:razor-pages/sdk) zpracovává kompilace souborech Razor. Při sestavování projektu, generuje Razor SDK *obj / < build_configuration > / < target_framework_moniker > / Razor* adresáře v kořenové složce projektu. Struktura adresářů v rámci *Razor* directory zrcadlí adresářovou strukturu projektu.

Vezměte v úvahu následující adresářovou strukturu v projektu aplikace ASP.NET Core 2.1 Razor Pages cílí na .NET Core 2.1:

* **Oblasti /**
  * **Správce /**
    * **Stránky /**
      * *Index.cshtml*
      * *Index.cshtml.cs*
* **Stránky /**
  * **Sdílené /**
    * *_Layout.cshtml*
  * *_ViewImports.cshtml*
  * *_ViewStart.cshtml*
  * *Index.cshtml*
  * *Index.cshtml.cs*

Vytvoření projektu *ladění* konfigurace provede následující *obj* adresáře:

* **obj /**
  * **Ladění /**
    * **netcoreapp2.1 /**
      * **Razor /**
        * **Oblasti /**
          * **Správce /**
            * **Stránky /**
              * *Index.g.cshtml.cs*
        * **Stránky /**
          * **Sdílené /**
            * *_Layout.g.cshtml.cs*
          * *_ViewImports.g.cshtml.cs*
          * *_ViewStart.g.cshtml.cs*
          * *Index.g.cshtml.cs*

Chcete-li zobrazit vygenerované třídy pro *Pages/Index.cshtml*, otevřete *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Přidejte následující třídu do projektu ASP.NET Core MVC:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

V `Startup.ConfigureServices`, přepsat `RazorTemplateEngine` přidal MVC s `CustomTemplateEngine` třídy:

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Nastavit zarážku na `return csharpDocument;` příkazem `CustomTemplateEngine`. Při spuštění programu se zastaví na zarážce, zobrazit hodnotu `generatedCode`.

![Text Visualizer zobrazení generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Zobrazení vyhledávání a rozlišování velikosti písmen

Zobrazovací modul Razor provádí velká a malá písmena vyhledávání pro zobrazení. Nicméně skutečné vyhledávání je určeno podkladový systém souborů:

* Na základě zdrojového souboru:
  * V operačních systémech se systémy souborů malá a velká písmena (například Windows) fyzický soubor poskytovatele vyhledávání jsou malá a velká písmena. Například `return View("Test")` výsledkem shody */Views/Home/Test.cshtml*, */Views/home/test.cshtml*a další varianty velká a malá písmena.
  * V systémech souborů s malá a velká písmena (například Linux, OSX a s `EmbeddedFileProvider`), hledání jsou malá a velká písmena. Například `return View("Test")` konkrétně shody */Views/Home/Test.cshtml*.
* Předkompilované zobrazení: S ASP.NET Core 2.0 nebo novější hledání předkompilované zobrazení velká a malá písmena na všechny operační systémy. Chování se shoduje s chování zprostředkovatele fyzického souboru ve Windows. Pokud se zobrazeními předkompilované liší pouze v případě, výsledek vyhledávání je Nedeterministický.

Vývojáři nepodnikovým tak, aby odpovídaly malých a velkých písmen názvů použití malých a velkých souborů a adresářů:

* Názvy oblastí, kontroleru a akce.
* Stránky Razor.

Odpovídající případ zajistí, že pro nasazení své názory, bez ohledu na podkladový systém souborů.
