---
title: Referenční dokumentace syntaxe Razor pro ASP.NET Core
author: rick-anderson
description: Přečtěte si o Razor syntaxi značek pro vkládání kódu založeného na serveru na webové stránky.
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/razor
ms.openlocfilehash: 2831fd2edd029043e9457cd213e32f1a82c2872e
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424426"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="44037-103">Odkaz na syntaxe Razor pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="44037-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="44037-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)a [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="44037-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="44037-105">Razor je syntaxe značek pro vkládání kódu založeného na serveru na webové stránky.</span><span class="sxs-lookup"><span data-stu-id="44037-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="44037-106">Syntaxe Razor se skládá ze značek Razor, C# a HTML.</span><span class="sxs-lookup"><span data-stu-id="44037-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="44037-107">Soubory obsahující Razor mají obecně příponu *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="44037-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="44037-108">Razor se nachází také v souborech [součástí Razor](xref:blazor/components) (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="44037-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="44037-109">Vykreslování kódu HTML</span><span class="sxs-lookup"><span data-stu-id="44037-109">Rendering HTML</span></span>

<span data-ttu-id="44037-110">Výchozím jazykem pro Razor je HTML.</span><span class="sxs-lookup"><span data-stu-id="44037-110">The default Razor language is HTML.</span></span> <span data-ttu-id="44037-111">Vykreslování HTML z kódu Razor se nijak neliší od vykreslování HTML ze souboru HTML.</span><span class="sxs-lookup"><span data-stu-id="44037-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="44037-112">Kód HTML v souboru *. cshtml* Razor je vykreslen serverem beze změny.</span><span class="sxs-lookup"><span data-stu-id="44037-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="44037-113">Syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="44037-113">Razor syntax</span></span>

<span data-ttu-id="44037-114">Razor podporuje jazyk C# a používá `@` symbol k přechodu z formátu HTML do C#.</span><span class="sxs-lookup"><span data-stu-id="44037-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="44037-115">Razor vyhodnocuje výrazy jazyka C# a vykresluje je ve výstupu HTML.</span><span class="sxs-lookup"><span data-stu-id="44037-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="44037-116">Je-li `@` symbol následován [rezervovaným klíčovým slovem Razor](#razor-reserved-keywords), přejde do kódu specifického pro Razor.</span><span class="sxs-lookup"><span data-stu-id="44037-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="44037-117">V opačném případě přejde do jednoduchého jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="44037-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="44037-118">Chcete-li `@` v kódu Razor použít řídicí znak, použijte druhý `@` symbol:</span><span class="sxs-lookup"><span data-stu-id="44037-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="44037-119">Kód je vykreslen ve formátu HTML s jedním `@` symbolem:</span><span class="sxs-lookup"><span data-stu-id="44037-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="44037-120">Atributy HTML a obsah obsahující e-mailové adresy se symbol nepovažují `@` jako znak přechodu.</span><span class="sxs-lookup"><span data-stu-id="44037-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="44037-121">E-mailové adresy v následujícím příkladu nejsou vyretušované analýzou Razor:</span><span class="sxs-lookup"><span data-stu-id="44037-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="44037-122">Implicitní výrazy Razor</span><span class="sxs-lookup"><span data-stu-id="44037-122">Implicit Razor expressions</span></span>

<span data-ttu-id="44037-123">Implicitní výrazy Razor začínají za `@` následováním kódu jazyka C#:</span><span class="sxs-lookup"><span data-stu-id="44037-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="44037-124">S výjimkou `await` klíčového slova jazyka C# nesmí implicitní výrazy obsahovat mezery.</span><span class="sxs-lookup"><span data-stu-id="44037-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="44037-125">Pokud má příkaz jazyka C# jasný konec, mezery mohou být smíšené:</span><span class="sxs-lookup"><span data-stu-id="44037-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="44037-126">Implicitní výrazy **nemůžou** obsahovat obecné typy C#, protože znaky uvnitř závorek ( `<>` ) jsou interpretovány jako značka jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="44037-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="44037-127">Následující kód **není platný:**</span><span class="sxs-lookup"><span data-stu-id="44037-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="44037-128">Předchozí kód vygeneruje chybu kompilátoru podobný jednomu z následujících:</span><span class="sxs-lookup"><span data-stu-id="44037-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="44037-129">Element int se nezavřel.</span><span class="sxs-lookup"><span data-stu-id="44037-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="44037-130">Všechny elementy musí být buď samouzavírací, nebo musí mít stejnou koncovou značku.</span><span class="sxs-lookup"><span data-stu-id="44037-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="44037-131">Nejde převést skupinu metod ' GenericMethod ' na Nedelegovaný typ ' Object '.</span><span class="sxs-lookup"><span data-stu-id="44037-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="44037-132">Chtěli jste vyvolat metodu?</span><span class="sxs-lookup"><span data-stu-id="44037-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="44037-133">Volání obecných metod musí být zabalena do [explicitního výrazu Razor](#explicit-razor-expressions) nebo [bloku kódu Razor](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="44037-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="44037-134">Explicitní výrazy Razor</span><span class="sxs-lookup"><span data-stu-id="44037-134">Explicit Razor expressions</span></span>

<span data-ttu-id="44037-135">Explicitní výrazy Razor se skládají ze `@` symbolu s vyrovnanou závorkou.</span><span class="sxs-lookup"><span data-stu-id="44037-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="44037-136">Chcete-li vykreslit čas posledního týdne, je použit následující kód Razor:</span><span class="sxs-lookup"><span data-stu-id="44037-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="44037-137">Veškerý obsah v `@()` závorkách je vyhodnocen a vykreslen do výstupu.</span><span class="sxs-lookup"><span data-stu-id="44037-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="44037-138">Implicitní výrazy popsané v předchozí části všeobecně nemůžou obsahovat mezery.</span><span class="sxs-lookup"><span data-stu-id="44037-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="44037-139">V následujícím kódu se jeden týden odečte od aktuálního času:</span><span class="sxs-lookup"><span data-stu-id="44037-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="44037-140">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="44037-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="44037-141">Explicitní výrazy lze použít k zřetězení textu s výsledkem výrazu:</span><span class="sxs-lookup"><span data-stu-id="44037-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="44037-142">Bez explicitního výrazu `<p>Age@joe.Age</p>` je zpracována jako e-mailová adresa a `<p>Age@joe.Age</p>` je vykreslena.</span><span class="sxs-lookup"><span data-stu-id="44037-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="44037-143">Je-li zapsán jako explicitní výraz, `<p>Age33</p>` je vykreslen.</span><span class="sxs-lookup"><span data-stu-id="44037-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="44037-144">Explicitní výrazy lze použít k vykreslení výstupu z obecných metod v souborech *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="44037-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="44037-145">Následující kód ukazuje, jak opravit chybu zobrazenou dříve, způsobenou závorkami obecných v jazyce C#.</span><span class="sxs-lookup"><span data-stu-id="44037-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="44037-146">Kód je zapsán jako explicitní výraz:</span><span class="sxs-lookup"><span data-stu-id="44037-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="44037-147">Kódování výrazu</span><span class="sxs-lookup"><span data-stu-id="44037-147">Expression encoding</span></span>

<span data-ttu-id="44037-148">Výrazy jazyka C#, které jsou vyhodnoceny jako řetězec, jsou kódovány HTML.</span><span class="sxs-lookup"><span data-stu-id="44037-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="44037-149">Výrazy jazyka C#, které se vyhodnotí, se `IHtmlContent` vykreslují přímo prostřednictvím `IHtmlContent.WriteTo` .</span><span class="sxs-lookup"><span data-stu-id="44037-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="44037-150">Výrazy jazyka C#, které nejsou vyhodnoceny, `IHtmlContent` jsou převedeny na řetězec pomocí `ToString` a kódovány před jejich vykreslením.</span><span class="sxs-lookup"><span data-stu-id="44037-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="44037-151">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="44037-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="44037-152">HTML se zobrazí v prohlížeči jako:</span><span class="sxs-lookup"><span data-stu-id="44037-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="44037-153">`HtmlHelper.Raw`výstup není kódovaný, ale vykresluje se jako kód HTML.</span><span class="sxs-lookup"><span data-stu-id="44037-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="44037-154">Použití `HtmlHelper.Raw` na neupraveném vstupu uživatele představuje bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="44037-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="44037-155">Vstup uživatele může obsahovat škodlivý JavaScript nebo jiné zneužití.</span><span class="sxs-lookup"><span data-stu-id="44037-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="44037-156">Upravení vstupu uživatele je obtížné.</span><span class="sxs-lookup"><span data-stu-id="44037-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="44037-157">Nepoužívejte `HtmlHelper.Raw` s uživatelským vstupem.</span><span class="sxs-lookup"><span data-stu-id="44037-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="44037-158">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="44037-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="44037-159">Bloky kódu Razor</span><span class="sxs-lookup"><span data-stu-id="44037-159">Razor code blocks</span></span>

<span data-ttu-id="44037-160">Bloky kódu Razor začínají `@` na a jsou uzavřeny v `{}` .</span><span class="sxs-lookup"><span data-stu-id="44037-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="44037-161">Na rozdíl od výrazů není kód C# uvnitř bloků kódu vykreslen.</span><span class="sxs-lookup"><span data-stu-id="44037-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="44037-162">Bloky kódu a výrazy v zobrazení sdílejí stejný obor a jsou definovány v pořadí:</span><span class="sxs-lookup"><span data-stu-id="44037-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="44037-163">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="44037-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="44037-164">V blocích kódu deklarujte [místní funkce](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) pomocí značek, které budou sloužit jako metody šablonování:</span><span class="sxs-lookup"><span data-stu-id="44037-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="44037-165">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="44037-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="44037-166">Implicitní přechody</span><span class="sxs-lookup"><span data-stu-id="44037-166">Implicit transitions</span></span>

<span data-ttu-id="44037-167">Výchozí jazyk v bloku kódu je C#, ale stránka Razor může přejít zpět na kód HTML:</span><span class="sxs-lookup"><span data-stu-id="44037-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="44037-168">Explicitně oddělený přechod</span><span class="sxs-lookup"><span data-stu-id="44037-168">Explicit delimited transition</span></span>

<span data-ttu-id="44037-169">Chcete-li definovat dílčí oddíl bloku kódu, který by měl vykreslovat kód HTML, uzavřete znaky pro vykreslování se `<text>` značkou Razor:</span><span class="sxs-lookup"><span data-stu-id="44037-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="44037-170">Pomocí tohoto přístupu můžete vykreslit HTML, které není obklopené značkou HTML.</span><span class="sxs-lookup"><span data-stu-id="44037-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="44037-171">Bez značky HTML nebo Razor dojde k chybě modulu runtime Razor.</span><span class="sxs-lookup"><span data-stu-id="44037-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="44037-172">`<text>`Značka je užitečná pro řízení prázdných znaků při vykreslování obsahu:</span><span class="sxs-lookup"><span data-stu-id="44037-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="44037-173">Vykreslí se jenom obsah mezi `<text>` značkou.</span><span class="sxs-lookup"><span data-stu-id="44037-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="44037-174">Před nebo po `<text>` značce se ve výstupu HTML nezobrazí žádné prázdné znaky.</span><span class="sxs-lookup"><span data-stu-id="44037-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="44037-175">Explicitní přechod řádku</span><span class="sxs-lookup"><span data-stu-id="44037-175">Explicit line transition</span></span>

<span data-ttu-id="44037-176">Chcete-li vykreslit zbytek celého řádku jako HTML v bloku kódu, použijte `@:` syntaxi:</span><span class="sxs-lookup"><span data-stu-id="44037-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="44037-177">Bez `@:` v kódu je vygenerována chyba modulu runtime Razor.</span><span class="sxs-lookup"><span data-stu-id="44037-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="44037-178">Nadbytečné `@` znaky v souboru Razor mohou způsobit chyby kompilátoru v příkazech později v bloku.</span><span class="sxs-lookup"><span data-stu-id="44037-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="44037-179">Tyto chyby kompilátoru mohou být obtížné pochopit, protože před nahlášenou chybou dojde k aktuální chybě.</span><span class="sxs-lookup"><span data-stu-id="44037-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="44037-180">Tato chyba je společná po Zkombinování více implicitních nebo explicitních výrazů do jednoho bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="44037-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="44037-181">Řídicí struktury</span><span class="sxs-lookup"><span data-stu-id="44037-181">Control structures</span></span>

<span data-ttu-id="44037-182">Řídicí struktury jsou rozšíření bloků kódu.</span><span class="sxs-lookup"><span data-stu-id="44037-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="44037-183">Všechny aspekty bloků kódu (přechod na kód, vložené C#) platí také pro následující struktury:</span><span class="sxs-lookup"><span data-stu-id="44037-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="44037-184">Podmíněný parametr \@ if, else if, else a \@ Switch</span><span class="sxs-lookup"><span data-stu-id="44037-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="44037-185">`@if`ovládací prvky při spuštění kódu:</span><span class="sxs-lookup"><span data-stu-id="44037-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="44037-186">`else`a `else if` nevyžadují `@` symbol:</span><span class="sxs-lookup"><span data-stu-id="44037-186">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="44037-187">Následující kód ukazuje, jak použít příkaz switch:</span><span class="sxs-lookup"><span data-stu-id="44037-187">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="44037-188">Smyčky \@ pro, \@ foreach, \@ while a \@ while</span><span class="sxs-lookup"><span data-stu-id="44037-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="44037-189">HTML s šablonou lze vykreslit pomocí příkazů pro opakování.</span><span class="sxs-lookup"><span data-stu-id="44037-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="44037-190">Vykreslit seznam osob:</span><span class="sxs-lookup"><span data-stu-id="44037-190">To render a list of people:</span></span>

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

<span data-ttu-id="44037-191">Podporovány jsou následující příkazy smyček:</span><span class="sxs-lookup"><span data-stu-id="44037-191">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="44037-192">Složené \@ pomocí</span><span class="sxs-lookup"><span data-stu-id="44037-192">Compound \@using</span></span>

<span data-ttu-id="44037-193">V jazyce C# se `using` k zajištění uvolnění objektu používá příkaz.</span><span class="sxs-lookup"><span data-stu-id="44037-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="44037-194">V Razor je stejný mechanismus použit k vytváření pomocníků HTML, které obsahují další obsah.</span><span class="sxs-lookup"><span data-stu-id="44037-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="44037-195">V následujícím kódu pomocník HTML vykresluje `<form>` značku s `@using` příkazem:</span><span class="sxs-lookup"><span data-stu-id="44037-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="44037-196">\@try, catch, finally</span><span class="sxs-lookup"><span data-stu-id="44037-196">\@try, catch, finally</span></span>

<span data-ttu-id="44037-197">Zpracování výjimek je podobné jazyku C#:</span><span class="sxs-lookup"><span data-stu-id="44037-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="44037-198">\@lock</span><span class="sxs-lookup"><span data-stu-id="44037-198">\@lock</span></span>

<span data-ttu-id="44037-199">Razor má možnost chránit kritické oddíly pomocí příkazů Lock:</span><span class="sxs-lookup"><span data-stu-id="44037-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="44037-200">Komentáře</span><span class="sxs-lookup"><span data-stu-id="44037-200">Comments</span></span>

<span data-ttu-id="44037-201">Razor podporuje komentáře C# a HTML:</span><span class="sxs-lookup"><span data-stu-id="44037-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="44037-202">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="44037-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="44037-203">Před vykreslením webové stránky jsou komentáře Razor odebrány serverem.</span><span class="sxs-lookup"><span data-stu-id="44037-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="44037-204">Nástroj Razor používá `@*  *@` k vymezení komentářů.</span><span class="sxs-lookup"><span data-stu-id="44037-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="44037-205">Následující kód je zakomentován, takže server nevykresluje žádné značky:</span><span class="sxs-lookup"><span data-stu-id="44037-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="44037-206">Direktivy</span><span class="sxs-lookup"><span data-stu-id="44037-206">Directives</span></span>

<span data-ttu-id="44037-207">Direktivy Razor jsou reprezentovány implicitními výrazy s vyhrazenými klíčovými slovy za `@` symbolem.</span><span class="sxs-lookup"><span data-stu-id="44037-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="44037-208">Direktiva obvykle mění způsob, jakým se analyzuje zobrazení, nebo umožňuje různé funkce.</span><span class="sxs-lookup"><span data-stu-id="44037-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="44037-209">Pochopení, jak Razor generuje kód pro zobrazení, usnadňuje pochopení, jak direktivy fungují.</span><span class="sxs-lookup"><span data-stu-id="44037-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="44037-210">Kód generuje třídu podobnou této:</span><span class="sxs-lookup"><span data-stu-id="44037-210">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="44037-211">Později v tomto článku najdete v části [Kontrola třídy jazyka Razor, která je vygenerovaná pro zobrazení](#inspect-the-razor-c-class-generated-for-a-view) , vysvětluje, jak zobrazit tuto generovanou třídu.</span><span class="sxs-lookup"><span data-stu-id="44037-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="44037-212">\@přidělen</span><span class="sxs-lookup"><span data-stu-id="44037-212">\@attribute</span></span>

<span data-ttu-id="44037-213">`@attribute`Direktiva přidá daný atribut třídě vygenerované stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="44037-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="44037-214">Následující příklad přidá `[Authorize]` atribut:</span><span class="sxs-lookup"><span data-stu-id="44037-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="44037-215">\@znakovou</span><span class="sxs-lookup"><span data-stu-id="44037-215">\@code</span></span>

<span data-ttu-id="44037-216">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="44037-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="44037-217">`@code`Blok umožňuje [komponentě Razor](xref:blazor/components) přidat členy C# (pole, vlastnosti a metody) do komponenty:</span><span class="sxs-lookup"><span data-stu-id="44037-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="44037-218">Pro součásti Razor `@code` je alias [`@functions`](#functions) a doporučený `@functions` .</span><span class="sxs-lookup"><span data-stu-id="44037-218">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="44037-219">Je přípustný více než jeden `@code` blok.</span><span class="sxs-lookup"><span data-stu-id="44037-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="44037-220">\@POZVYHLEDAT</span><span class="sxs-lookup"><span data-stu-id="44037-220">\@functions</span></span>

<span data-ttu-id="44037-221">`@functions`Direktiva umožňuje přidat členy jazyka C# (pole, vlastnosti a metody) do generované třídy:</span><span class="sxs-lookup"><span data-stu-id="44037-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="44037-222">V [součástech Razor](xref:blazor/components)použijte `@code` více než `@functions` pro přidání členů jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="44037-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="44037-223">Například:</span><span class="sxs-lookup"><span data-stu-id="44037-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="44037-224">Kód generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="44037-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="44037-225">Následující kód je vygenerovaná třída jazyka C# jazyka Razor:</span><span class="sxs-lookup"><span data-stu-id="44037-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="44037-226">`@functions`metody slouží jako metody šablonování, pokud mají značky:</span><span class="sxs-lookup"><span data-stu-id="44037-226">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="44037-227">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="44037-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="44037-228">\@implementace</span><span class="sxs-lookup"><span data-stu-id="44037-228">\@implements</span></span>

<span data-ttu-id="44037-229">`@implements`Direktiva implementuje rozhraní pro generovanou třídu.</span><span class="sxs-lookup"><span data-stu-id="44037-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="44037-230">Následující příklad implementuje <xref:System.IDisposable?displayProperty=fullName> , aby <xref:System.IDisposable.Dispose*> mohla být metoda volána:</span><span class="sxs-lookup"><span data-stu-id="44037-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

### <a name="inherits"></a><span data-ttu-id="44037-231">\@zdědí</span><span class="sxs-lookup"><span data-stu-id="44037-231">\@inherits</span></span>

<span data-ttu-id="44037-232">`@inherits`Direktiva poskytuje úplnou kontrolu nad třídou, kterou zobrazení dědí:</span><span class="sxs-lookup"><span data-stu-id="44037-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="44037-233">Následující kód je vlastní typ stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="44037-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="44037-234">`CustomText`Je zobrazen v zobrazení:</span><span class="sxs-lookup"><span data-stu-id="44037-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="44037-235">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="44037-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="44037-236">`@model`a `@inherits` lze jej použít ve stejném zobrazení.</span><span class="sxs-lookup"><span data-stu-id="44037-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="44037-237">`@inherits`může být v souboru *_ViewImports. cshtml* , který zobrazení importuje:</span><span class="sxs-lookup"><span data-stu-id="44037-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="44037-238">Následující kód je příkladem zobrazení silného typu:</span><span class="sxs-lookup"><span data-stu-id="44037-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="44037-239">Pokud rick@contoso.com je v modelu předána možnost "", zobrazení generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="44037-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="44037-240">\@vstřik</span><span class="sxs-lookup"><span data-stu-id="44037-240">\@inject</span></span>

<span data-ttu-id="44037-241">`@inject`Direktiva umožňuje, aby stránka Razor mohla vložit službu z [kontejneru služby](xref:fundamentals/dependency-injection) do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="44037-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="44037-242">Další informace najdete v tématu [vkládání závislostí do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="44037-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="44037-243">\@rozložení</span><span class="sxs-lookup"><span data-stu-id="44037-243">\@layout</span></span>

<span data-ttu-id="44037-244">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="44037-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="44037-245">`@layout`Direktiva určuje rozložení součásti Razor.</span><span class="sxs-lookup"><span data-stu-id="44037-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="44037-246">Komponenty rozložení se používají k zamezení Duplikace kódu a nekonzistenci.</span><span class="sxs-lookup"><span data-stu-id="44037-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="44037-247">Další informace naleznete v tématu <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="44037-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="44037-248">\@vzorový</span><span class="sxs-lookup"><span data-stu-id="44037-248">\@model</span></span>

<span data-ttu-id="44037-249">*Tento scénář se vztahuje pouze na zobrazení MVC a Razor Pages (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="44037-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="44037-250">`@model`Direktiva určuje typ modelu předaného na zobrazení nebo stránku:</span><span class="sxs-lookup"><span data-stu-id="44037-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="44037-251">V ASP.NET Core MVC nebo aplikace Razor Pages vytvořené pomocí individuálních uživatelských účtů, *zobrazení/účet/přihlášení. cshtml* obsahuje následující deklaraci modelu:</span><span class="sxs-lookup"><span data-stu-id="44037-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="44037-252">Třída, ze které vygenerovala dědění `RazorPage<dynamic>` :</span><span class="sxs-lookup"><span data-stu-id="44037-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="44037-253">Razor zpřístupňuje `Model` vlastnost pro přístup k modelu předanému zobrazení:</span><span class="sxs-lookup"><span data-stu-id="44037-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="44037-254">`@model`Direktiva určuje typ `Model` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="44037-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="44037-255">Direktiva Určuje `T` , v jakém `RazorPage<T>` vygenerované třídě je odvozeno zobrazení.</span><span class="sxs-lookup"><span data-stu-id="44037-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="44037-256">Pokud `@model` není zadána direktiva, `Model` je vlastnost typu `dynamic` .</span><span class="sxs-lookup"><span data-stu-id="44037-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="44037-257">Další informace naleznete v tématu [modely silného typu a @model klíčové slovo](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="44037-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="44037-258">\@hosting</span><span class="sxs-lookup"><span data-stu-id="44037-258">\@namespace</span></span>

<span data-ttu-id="44037-259">`@namespace`Direktiva:</span><span class="sxs-lookup"><span data-stu-id="44037-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="44037-260">Nastaví obor názvů třídy vygenerované stránky Razor, zobrazení MVC nebo komponenty Razor.</span><span class="sxs-lookup"><span data-stu-id="44037-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="44037-261">Nastaví kořenové obory názvů pro třídy stránky, zobrazení nebo komponenty z nejbližšího souboru importu ve stromové struktuře, *_ViewImports. cshtml* (zobrazení nebo stránky) nebo *_Imports. Razor* (komponenty Razor).</span><span class="sxs-lookup"><span data-stu-id="44037-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="44037-262">Pro Razor Pages příklad zobrazený v následující tabulce:</span><span class="sxs-lookup"><span data-stu-id="44037-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="44037-263">Každá stránka importuje *stránky/_ViewImports. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="44037-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="44037-264">*Stránky/_ViewImports. cshtml* obsahuje `@namespace Hello.World` .</span><span class="sxs-lookup"><span data-stu-id="44037-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="44037-265">Každá stránka má `Hello.World` jako kořen oboru názvů IT.</span><span class="sxs-lookup"><span data-stu-id="44037-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="44037-266">stránka</span><span class="sxs-lookup"><span data-stu-id="44037-266">Page</span></span>                                        | <span data-ttu-id="44037-267">Obor názvů</span><span class="sxs-lookup"><span data-stu-id="44037-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="44037-268">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="44037-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="44037-269">*Pages/MorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="44037-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="44037-270">*Pages/MorePages/EvenMorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="44037-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="44037-271">Předchozí relace platí pro soubory importu používané s zobrazeními MVC a komponentami Razor.</span><span class="sxs-lookup"><span data-stu-id="44037-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="44037-272">Pokud má více souborů importu `@namespace` direktivu, soubor, který je nejblíže stránce, zobrazení nebo komponentě v adresářovém stromu, slouží k nastavení kořenového oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="44037-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="44037-273">Pokud má složka *EvenMorePages* v předchozím příkladu soubor Imports s `@namespace Another.Planet` (nebo soubor *Pages/MorePages/EvenMorePages/Page. cshtml* obsahuje `@namespace Another.Planet` ), výsledek je zobrazen v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="44037-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="44037-274">stránka</span><span class="sxs-lookup"><span data-stu-id="44037-274">Page</span></span>                                        | <span data-ttu-id="44037-275">Obor názvů</span><span class="sxs-lookup"><span data-stu-id="44037-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="44037-276">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="44037-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="44037-277">*Pages/MorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="44037-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="44037-278">*Pages/MorePages/EvenMorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="44037-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="44037-279">\@Page</span><span class="sxs-lookup"><span data-stu-id="44037-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="44037-280">`@page`Direktiva má jiné účinky v závislosti na typu souboru, ve kterém se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="44037-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="44037-281">Direktiva:</span><span class="sxs-lookup"><span data-stu-id="44037-281">The directive:</span></span>

* <span data-ttu-id="44037-282">V v souboru *. cshtml* znamená, že soubor je stránka Razor.</span><span class="sxs-lookup"><span data-stu-id="44037-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="44037-283">Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes) a <xref:razor-pages/index> .</span><span class="sxs-lookup"><span data-stu-id="44037-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="44037-284">Určuje, že komponenta Razor by měla zpracovávat požadavky přímo.</span><span class="sxs-lookup"><span data-stu-id="44037-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="44037-285">Další informace naleznete v tématu <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="44037-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="44037-286">`@page`Direktiva na prvním řádku souboru *. cshtml* znamená, že soubor je stránka Razor.</span><span class="sxs-lookup"><span data-stu-id="44037-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="44037-287">Další informace naleznete v tématu <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="44037-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="44037-288">\@section</span><span class="sxs-lookup"><span data-stu-id="44037-288">\@section</span></span>

<span data-ttu-id="44037-289">*Tento scénář se vztahuje pouze na zobrazení MVC a Razor Pages (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="44037-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="44037-290">`@section`Direktiva se používá společně s [MVC a Razor Pages rozloženími](xref:mvc/views/layout) k tomu, aby zobrazení nebo stránky vygenerovaly obsah v různých částech stránky HTML.</span><span class="sxs-lookup"><span data-stu-id="44037-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="44037-291">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="44037-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="44037-292">\@using</span><span class="sxs-lookup"><span data-stu-id="44037-292">\@using</span></span>

<span data-ttu-id="44037-293">`@using`Direktiva přidá `using` direktivu jazyka C# do generovaného zobrazení:</span><span class="sxs-lookup"><span data-stu-id="44037-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="44037-294">V [součástech Razor](xref:blazor/components) `@using` také řídí, které součásti jsou v oboru.</span><span class="sxs-lookup"><span data-stu-id="44037-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="44037-295">Atributy direktiv</span><span class="sxs-lookup"><span data-stu-id="44037-295">Directive attributes</span></span>

<span data-ttu-id="44037-296">Atributy direktivy Razor jsou reprezentovány implicitními výrazy s vyhrazenými klíčovými slovy za `@` symbolem.</span><span class="sxs-lookup"><span data-stu-id="44037-296">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="44037-297">Atribut direktivy obvykle mění způsob, jakým je prvek analyzován, nebo umožňuje různé funkce.</span><span class="sxs-lookup"><span data-stu-id="44037-297">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### <a name="attributes"></a><span data-ttu-id="44037-298">\@atribut</span><span class="sxs-lookup"><span data-stu-id="44037-298">\@attributes</span></span>

<span data-ttu-id="44037-299">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="44037-299">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="44037-300">`@attributes`umožňuje komponentě vykreslovat nedeklarované atributy.</span><span class="sxs-lookup"><span data-stu-id="44037-300">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="44037-301">Další informace naleznete v tématu <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="44037-301">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="44037-302">\@zapisovat</span><span class="sxs-lookup"><span data-stu-id="44037-302">\@bind</span></span>

<span data-ttu-id="44037-303">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="44037-303">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="44037-304">Datové vazby v součástech se provádí s `@bind` atributem.</span><span class="sxs-lookup"><span data-stu-id="44037-304">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="44037-305">Další informace naleznete v tématu <xref:blazor/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="44037-305">For more information, see <xref:blazor/data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="44037-306">\@v události {EVENT}</span><span class="sxs-lookup"><span data-stu-id="44037-306">\@on{EVENT}</span></span>

<span data-ttu-id="44037-307">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="44037-307">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="44037-308">Razor poskytuje funkce pro zpracování událostí pro součásti.</span><span class="sxs-lookup"><span data-stu-id="44037-308">Razor provides event handling features for components.</span></span> <span data-ttu-id="44037-309">Další informace naleznete v tématu <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="44037-309">For more information, see <xref:blazor/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a><span data-ttu-id="44037-310">\@v události {EVENT}:p reventDefault</span><span class="sxs-lookup"><span data-stu-id="44037-310">\@on{EVENT}:preventDefault</span></span>

<span data-ttu-id="44037-311">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="44037-311">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="44037-312">Zabrání výchozí akci pro událost.</span><span class="sxs-lookup"><span data-stu-id="44037-312">Prevents the default action for the event.</span></span>

### <a name="oneventstoppropagation"></a><span data-ttu-id="44037-313">\@v {EVENT}: stopPropagation</span><span class="sxs-lookup"><span data-stu-id="44037-313">\@on{EVENT}:stopPropagation</span></span>

<span data-ttu-id="44037-314">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="44037-314">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="44037-315">Zastaví šíření události pro událost.</span><span class="sxs-lookup"><span data-stu-id="44037-315">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a><span data-ttu-id="44037-316">\@Klíč</span><span class="sxs-lookup"><span data-stu-id="44037-316">\@key</span></span>

<span data-ttu-id="44037-317">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="44037-317">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="44037-318">`@key`Atribut direktiva způsobí, že rozdílový algoritmus komponent garantuje zachování prvků nebo komponent na základě hodnoty klíče.</span><span class="sxs-lookup"><span data-stu-id="44037-318">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="44037-319">Další informace naleznete v tématu <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="44037-319">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="44037-320">\@ref</span><span class="sxs-lookup"><span data-stu-id="44037-320">\@ref</span></span>

<span data-ttu-id="44037-321">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="44037-321">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="44037-322">Odkazy na součásti ( `@ref` ) poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance.</span><span class="sxs-lookup"><span data-stu-id="44037-322">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="44037-323">Další informace naleznete v tématu <xref:blazor/components#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="44037-323">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="44037-324">\@typeparam</span><span class="sxs-lookup"><span data-stu-id="44037-324">\@typeparam</span></span>

<span data-ttu-id="44037-325">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="44037-325">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="44037-326">`@typeparam`Direktiva deklaruje parametr obecného typu pro generovanou třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="44037-326">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="44037-327">Další informace naleznete v tématu <xref:blazor/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="44037-327">For more information, see <xref:blazor/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="44037-328">Delegáti Razor s šablonou</span><span class="sxs-lookup"><span data-stu-id="44037-328">Templated Razor delegates</span></span>

<span data-ttu-id="44037-329">Šablony Razor umožňují definovat fragment uživatelského rozhraní v následujícím formátu:</span><span class="sxs-lookup"><span data-stu-id="44037-329">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="44037-330">Následující příklad ukazuje, jak zadat delegáta Razor s šablonou jako <xref:System.Func%602> .</span><span class="sxs-lookup"><span data-stu-id="44037-330">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="44037-331">[Dynamický typ](/dotnet/csharp/programming-guide/types/using-type-dynamic) je zadán pro parametr metody, kterou delegát zapouzdřuje.</span><span class="sxs-lookup"><span data-stu-id="44037-331">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="44037-332">[Typ objektu](/dotnet/csharp/language-reference/keywords/object) je zadán jako návratová hodnota delegáta.</span><span class="sxs-lookup"><span data-stu-id="44037-332">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="44037-333">Šablona se používá s <xref:System.Collections.Generic.List%601> `Pet` vlastností, která má `Name` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="44037-333">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="44037-334">Šablona je vykreslena s `pets` dodaným `foreach` příkazem:</span><span class="sxs-lookup"><span data-stu-id="44037-334">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="44037-335">Vykreslený výstup:</span><span class="sxs-lookup"><span data-stu-id="44037-335">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="44037-336">Vloženou šablonu Razor můžete také dodat jako argument metodě.</span><span class="sxs-lookup"><span data-stu-id="44037-336">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="44037-337">V následujícím příkladu `Repeat` metoda obdrží šablonu Razor.</span><span class="sxs-lookup"><span data-stu-id="44037-337">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="44037-338">Metoda používá šablonu k vytvoření obsahu HTML s opakováním položek dodaných ze seznamu:</span><span class="sxs-lookup"><span data-stu-id="44037-338">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="44037-339">Pomocí seznamu domácích zvířat z předchozího příkladu `Repeat` je metoda volána s:</span><span class="sxs-lookup"><span data-stu-id="44037-339">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="44037-340"><xref:System.Collections.Generic.List%601>z `Pet` .</span><span class="sxs-lookup"><span data-stu-id="44037-340"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="44037-341">Počet opakování každé PET.</span><span class="sxs-lookup"><span data-stu-id="44037-341">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="44037-342">Vložená šablona, která se má použít pro položky seznamu neuspořádaného seznamu</span><span class="sxs-lookup"><span data-stu-id="44037-342">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="44037-343">Vykreslený výstup:</span><span class="sxs-lookup"><span data-stu-id="44037-343">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="44037-344">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="44037-344">Tag Helpers</span></span>

<span data-ttu-id="44037-345">*Tento scénář se vztahuje pouze na zobrazení MVC a Razor Pages (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="44037-345">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="44037-346">Existují tři direktivy, které se týkají [pomocníků značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="44037-346">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="44037-347">Směrnici</span><span class="sxs-lookup"><span data-stu-id="44037-347">Directive</span></span> | <span data-ttu-id="44037-348">Funkce</span><span class="sxs-lookup"><span data-stu-id="44037-348">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="44037-349">Zpřístupní pomocníkům značek zobrazení.</span><span class="sxs-lookup"><span data-stu-id="44037-349">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="44037-350">Odebere pomocníky značek dříve přidané ze zobrazení.</span><span class="sxs-lookup"><span data-stu-id="44037-350">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="44037-351">Určuje předponu značky pro povolení podpory značek pomocníka a k explicitnímu použití pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="44037-351">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a>Razor<span data-ttu-id="44037-352">vyhrazená klíčová slova</span><span class="sxs-lookup"><span data-stu-id="44037-352"> reserved keywords</span></span>

### <a name="razor-keywords"></a>Razor<span data-ttu-id="44037-353">klíčov</span><span class="sxs-lookup"><span data-stu-id="44037-353"> keywords</span></span>

* <span data-ttu-id="44037-354">Page (vyžaduje ASP.NET Core 2,1 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="44037-354">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="44037-355">namespace</span><span class="sxs-lookup"><span data-stu-id="44037-355">namespace</span></span>
* <span data-ttu-id="44037-356"> – funkce</span><span class="sxs-lookup"><span data-stu-id="44037-356">functions</span></span>
* <span data-ttu-id="44037-357">zdědí</span><span class="sxs-lookup"><span data-stu-id="44037-357">inherits</span></span>
* <span data-ttu-id="44037-358">model</span><span class="sxs-lookup"><span data-stu-id="44037-358">model</span></span>
* <span data-ttu-id="44037-359">section</span><span class="sxs-lookup"><span data-stu-id="44037-359">section</span></span>
* <span data-ttu-id="44037-360">pomocný objekt (aktuálně není podporován ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="44037-360">helper (Not currently supported by ASP.NET Core)</span></span>

Razor<span data-ttu-id="44037-361">Klíčová slova jsou uvozena znakem `@(Razor Keyword)` (například `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="44037-361"> keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="44037-362">RazorKlíčová slova jazyka C#</span><span class="sxs-lookup"><span data-stu-id="44037-362">C# Razor keywords</span></span>

* <span data-ttu-id="44037-363">case</span><span class="sxs-lookup"><span data-stu-id="44037-363">case</span></span>
* <span data-ttu-id="44037-364">do</span><span class="sxs-lookup"><span data-stu-id="44037-364">do</span></span>
* <span data-ttu-id="44037-365">default</span><span class="sxs-lookup"><span data-stu-id="44037-365">default</span></span>
* <span data-ttu-id="44037-366">pro</span><span class="sxs-lookup"><span data-stu-id="44037-366">for</span></span>
* <span data-ttu-id="44037-367">foreach</span><span class="sxs-lookup"><span data-stu-id="44037-367">foreach</span></span>
* <span data-ttu-id="44037-368">if</span><span class="sxs-lookup"><span data-stu-id="44037-368">if</span></span>
* <span data-ttu-id="44037-369">else</span><span class="sxs-lookup"><span data-stu-id="44037-369">else</span></span>
* <span data-ttu-id="44037-370">lock</span><span class="sxs-lookup"><span data-stu-id="44037-370">lock</span></span>
* <span data-ttu-id="44037-371">switch</span><span class="sxs-lookup"><span data-stu-id="44037-371">switch</span></span>
* <span data-ttu-id="44037-372">vyzkoušení</span><span class="sxs-lookup"><span data-stu-id="44037-372">try</span></span>
* <span data-ttu-id="44037-373">catch</span><span class="sxs-lookup"><span data-stu-id="44037-373">catch</span></span>
* <span data-ttu-id="44037-374">finally</span><span class="sxs-lookup"><span data-stu-id="44037-374">finally</span></span>
* <span data-ttu-id="44037-375">using</span><span class="sxs-lookup"><span data-stu-id="44037-375">using</span></span>
* <span data-ttu-id="44037-376">while</span><span class="sxs-lookup"><span data-stu-id="44037-376">while</span></span>

<span data-ttu-id="44037-377">RazorKlíčová slova jazyka C# musí mít dvojité uvození řídicím znakem `@(@C# Razor Keyword)` (například `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="44037-377">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="44037-378">První `@` řídí Razor analyzátor.</span><span class="sxs-lookup"><span data-stu-id="44037-378">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="44037-379">Druhý `@` řídí analyzátor jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="44037-379">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="44037-380">Vyhrazená klíčová slova, která nepoužíváRazor</span><span class="sxs-lookup"><span data-stu-id="44037-380">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="44037-381">class</span><span class="sxs-lookup"><span data-stu-id="44037-381">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="44037-382">Kontrola Razor třídy jazyka C# vygenerované pro zobrazení</span><span class="sxs-lookup"><span data-stu-id="44037-382">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="44037-383">Pomocí .NET Core SDK 2,1 nebo novější [ Razor sada SDK](xref:razor-pages/sdk) zpracovává kompilaci Razor souborů.</span><span class="sxs-lookup"><span data-stu-id="44037-383">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="44037-384">Při sestavování projektu Razor generuje sada SDK objekt \*obj/<build_configuration>/<target_framework_moniker>/ Razor \* adresář v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="44037-384">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="44037-385">Adresářová struktura v *Razor* adresáři zrcadlí strukturu adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="44037-385">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="44037-386">Vezměte v úvahu následující adresářovou strukturu v projektu stránky ASP.NET Core 2,1 s Razor cílením na rozhraní .NET Core 2,1:</span><span class="sxs-lookup"><span data-stu-id="44037-386">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="44037-387">**Místa**</span><span class="sxs-lookup"><span data-stu-id="44037-387">**Areas/**</span></span>
  * <span data-ttu-id="44037-388">**Správ**</span><span class="sxs-lookup"><span data-stu-id="44037-388">**Admin/**</span></span>
    * <span data-ttu-id="44037-389">**Stránky**</span><span class="sxs-lookup"><span data-stu-id="44037-389">**Pages/**</span></span>
      * <span data-ttu-id="44037-390">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="44037-390">*Index.cshtml*</span></span>
      * <span data-ttu-id="44037-391">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="44037-391">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="44037-392">**Stránky**</span><span class="sxs-lookup"><span data-stu-id="44037-392">**Pages/**</span></span>
  * <span data-ttu-id="44037-393">**Sdíleného**</span><span class="sxs-lookup"><span data-stu-id="44037-393">**Shared/**</span></span>
    * <span data-ttu-id="44037-394">*Soubor _Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="44037-394">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="44037-395">*_ViewImports. cshtml*</span><span class="sxs-lookup"><span data-stu-id="44037-395">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="44037-396">*_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="44037-396">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="44037-397">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="44037-397">*Index.cshtml*</span></span>
  * <span data-ttu-id="44037-398">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="44037-398">*Index.cshtml.cs*</span></span>

<span data-ttu-id="44037-399">Sestavení projektu v konfiguraci *ladění* má za následek následující adresář *obj* :</span><span class="sxs-lookup"><span data-stu-id="44037-399">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="44037-400">**objektu**</span><span class="sxs-lookup"><span data-stu-id="44037-400">**obj/**</span></span>
  * <span data-ttu-id="44037-401">**Ladí**</span><span class="sxs-lookup"><span data-stu-id="44037-401">**Debug/**</span></span>
    * <span data-ttu-id="44037-402">**netcoreapp 2.1/**</span><span class="sxs-lookup"><span data-stu-id="44037-402">**netcoreapp2.1/**</span></span>
      * **Razor/**
        * <span data-ttu-id="44037-403">**Místa**</span><span class="sxs-lookup"><span data-stu-id="44037-403">**Areas/**</span></span>
          * <span data-ttu-id="44037-404">**Správ**</span><span class="sxs-lookup"><span data-stu-id="44037-404">**Admin/**</span></span>
            * <span data-ttu-id="44037-405">**Stránky**</span><span class="sxs-lookup"><span data-stu-id="44037-405">**Pages/**</span></span>
              * <span data-ttu-id="44037-406">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="44037-406">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="44037-407">**Stránky**</span><span class="sxs-lookup"><span data-stu-id="44037-407">**Pages/**</span></span>
          * <span data-ttu-id="44037-408">**Sdíleného**</span><span class="sxs-lookup"><span data-stu-id="44037-408">**Shared/**</span></span>
            * <span data-ttu-id="44037-409">*_Layout. g. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="44037-409">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="44037-410">*_ViewImports. g. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="44037-410">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="44037-411">*_ViewStart. g. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="44037-411">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="44037-412">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="44037-412">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="44037-413">Chcete-li zobrazit vygenerovanou třídu pro *pages/index. cshtml*, otevřete *obj/ladění/netcoreapp 2.1/ Razor /pages/index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="44037-413">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="44037-414">Přidejte následující třídu do projektu ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="44037-414">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="44037-415">V aplikaci `Startup.ConfigureServices` přepište `RazorTemplateEngine` přidanou `CustomTemplateEngine` třídou MVC pomocí třídy:</span><span class="sxs-lookup"><span data-stu-id="44037-415">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="44037-416">Nastavte zarážku na `return csharpDocument;` příkazu `CustomTemplateEngine` .</span><span class="sxs-lookup"><span data-stu-id="44037-416">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="44037-417">Když se provádění programu zastaví na zarážce, zobrazte hodnotu `generatedCode` .</span><span class="sxs-lookup"><span data-stu-id="44037-417">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Zobrazení Vizualizér textu pro generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="44037-419">Zobrazit hledání a rozlišovat velká a malá písmena</span><span class="sxs-lookup"><span data-stu-id="44037-419">View lookups and case sensitivity</span></span>

<span data-ttu-id="44037-420">RazorModul zobrazení provádí pro zobrazení vyhledávání citlivá na velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="44037-420">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="44037-421">Skutečné vyhledávání je však určeno základním systémem souborů:</span><span class="sxs-lookup"><span data-stu-id="44037-421">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="44037-422">Zdroj založený na souborech:</span><span class="sxs-lookup"><span data-stu-id="44037-422">File based source:</span></span>
  * <span data-ttu-id="44037-423">V operačních systémech se systémy souborů bez rozlišení velkých a malých písmen (například Windows) se u hledání fyzického zprostředkovatele souborů nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="44037-423">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="44037-424">Výsledkem je například `return View("Test")` shoda pro */views/Home/test.cshtml*, */views/Home/test.cshtml*a jakékoliv jiné varianty velikosti písmen.</span><span class="sxs-lookup"><span data-stu-id="44037-424">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="44037-425">V systémech souborů s rozlišováním velkých a malých písmen (například Linux, OSX a with `EmbeddedFileProvider` ) se při hledání rozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="44037-425">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="44037-426">`return View("Test")`Konkrétně například odpovídá */views/Home/test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="44037-426">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="44037-427">Předkompilovaná zobrazení: u ASP.NET Core 2,0 a novějších, hledání předkompilovaných zobrazení rozlišuje velká a malá písmena ve všech operačních systémech.</span><span class="sxs-lookup"><span data-stu-id="44037-427">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="44037-428">Chování je stejné jako chování poskytovatele fyzického souboru ve Windows.</span><span class="sxs-lookup"><span data-stu-id="44037-428">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="44037-429">Pokud se dvě Předkompilovaná zobrazení liší pouze v případě, výsledek hledání není deterministický.</span><span class="sxs-lookup"><span data-stu-id="44037-429">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="44037-430">Vývojářům doporučujeme, aby se shodovaly s velkými písmeny názvů souborů a adresářů na velká a malá písmena:</span><span class="sxs-lookup"><span data-stu-id="44037-430">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="44037-431">Názvy oblastí, kontrolérů a akcí.</span><span class="sxs-lookup"><span data-stu-id="44037-431">Area, controller, and action names.</span></span>
* Razor<span data-ttu-id="44037-432">Stránky.</span><span class="sxs-lookup"><span data-stu-id="44037-432"> Pages.</span></span>

<span data-ttu-id="44037-433">V případě, že se rozlišuje velká a malá písmena, zajistíte jejich zobrazení bez ohledu na základní systém souborů.</span><span class="sxs-lookup"><span data-stu-id="44037-433">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="44037-434">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="44037-434">Additional resources</span></span>

<span data-ttu-id="44037-435">[Seznámení s ASP.NET webovým programováním pomocí Razor Syntaxe](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) poskytuje mnoho ukázek programování s Razor syntaxí.</span><span class="sxs-lookup"><span data-stu-id="44037-435">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
