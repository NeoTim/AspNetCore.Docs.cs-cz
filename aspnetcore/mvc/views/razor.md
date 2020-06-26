---
title: Referenční dokumentace syntaxe Razor pro ASP.NET Core
author: rick-anderson
description: Přečtěte si o Razor syntaxi značek pro vkládání kódu založeného na serveru na webové stránky.
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/razor
ms.openlocfilehash: ba778c45c9a6f608b50a17a9f8c6d50484dbf07c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405897"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a>Razor<span data-ttu-id="5d642-103">Referenční informace k syntaxi pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5d642-103"> syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="5d642-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)a [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="5d642-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

Razor<span data-ttu-id="5d642-105">je syntaxe značek pro vložení kódu založeného na serveru na webové stránky.</span><span class="sxs-lookup"><span data-stu-id="5d642-105"> is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="5d642-106">RazorSyntaxe se skládá z Razor značek, C# a HTML.</span><span class="sxs-lookup"><span data-stu-id="5d642-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="5d642-107">Soubory, Razor které obsahují obecně, mají příponu *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="5d642-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> Razor<span data-ttu-id="5d642-108">se nachází také v souborech [ Razor komponent](xref:blazor/components/index) (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="5d642-108"> is also found in [Razor components](xref:blazor/components/index) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="5d642-109">Vykreslování kódu HTML</span><span class="sxs-lookup"><span data-stu-id="5d642-109">Rendering HTML</span></span>

<span data-ttu-id="5d642-110">Výchozí Razor jazyk je HTML.</span><span class="sxs-lookup"><span data-stu-id="5d642-110">The default Razor language is HTML.</span></span> <span data-ttu-id="5d642-111">Vykreslování HTML ze Razor značek se neliší od vykreslování HTML ze souboru HTML.</span><span class="sxs-lookup"><span data-stu-id="5d642-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="5d642-112">Kód HTML v souborech *. cshtml* Razor je vykreslen serverem beze změny.</span><span class="sxs-lookup"><span data-stu-id="5d642-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a>Razor<span data-ttu-id="5d642-113">syntaktick</span><span class="sxs-lookup"><span data-stu-id="5d642-113"> syntax</span></span>

Razor<span data-ttu-id="5d642-114">podporuje jazyk C# a používá `@` symbol k přechodu z formátu HTML do C#.</span><span class="sxs-lookup"><span data-stu-id="5d642-114"> supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> Razor<span data-ttu-id="5d642-115">vyhodnotí výrazy jazyka C# a vykresluje je ve výstupu HTML.</span><span class="sxs-lookup"><span data-stu-id="5d642-115"> evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="5d642-116">`@`Je-li symbol následován [ Razor vyhrazeným klíčovým slovem](#razor-reserved-keywords), přechází na Razor konkrétní značku.</span><span class="sxs-lookup"><span data-stu-id="5d642-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="5d642-117">V opačném případě přejde do jednoduchého jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="5d642-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="5d642-118">Chcete-li `@` v kódu řídicí znak Razor , použijte druhý `@` symbol:</span><span class="sxs-lookup"><span data-stu-id="5d642-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="5d642-119">Kód je vykreslen ve formátu HTML s jedním `@` symbolem:</span><span class="sxs-lookup"><span data-stu-id="5d642-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="5d642-120">Atributy HTML a obsah obsahující e-mailové adresy se symbol nepovažují `@` jako znak přechodu.</span><span class="sxs-lookup"><span data-stu-id="5d642-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="5d642-121">E-mailové adresy v následujícím příkladu nejsou vyretušované Razor analýzou:</span><span class="sxs-lookup"><span data-stu-id="5d642-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="5d642-122">Implicitní Razor výrazy</span><span class="sxs-lookup"><span data-stu-id="5d642-122">Implicit Razor expressions</span></span>

<span data-ttu-id="5d642-123">Implicitní Razor výrazy začínají `@` následováním kódu jazyka C#:</span><span class="sxs-lookup"><span data-stu-id="5d642-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="5d642-124">S výjimkou `await` klíčového slova jazyka C# nesmí implicitní výrazy obsahovat mezery.</span><span class="sxs-lookup"><span data-stu-id="5d642-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="5d642-125">Pokud má příkaz jazyka C# jasný konec, mezery mohou být smíšené:</span><span class="sxs-lookup"><span data-stu-id="5d642-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="5d642-126">Implicitní výrazy **nemůžou** obsahovat obecné typy C#, protože znaky uvnitř závorek ( `<>` ) jsou interpretovány jako značka jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="5d642-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="5d642-127">Následující kód **není platný:**</span><span class="sxs-lookup"><span data-stu-id="5d642-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="5d642-128">Předchozí kód vygeneruje chybu kompilátoru podobný jednomu z následujících:</span><span class="sxs-lookup"><span data-stu-id="5d642-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="5d642-129">Element int se nezavřel.</span><span class="sxs-lookup"><span data-stu-id="5d642-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="5d642-130">Všechny elementy musí být buď samouzavírací, nebo musí mít stejnou koncovou značku.</span><span class="sxs-lookup"><span data-stu-id="5d642-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="5d642-131">Nejde převést skupinu metod ' GenericMethod ' na Nedelegovaný typ ' Object '.</span><span class="sxs-lookup"><span data-stu-id="5d642-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="5d642-132">Chtěli jste vyvolat metodu?</span><span class="sxs-lookup"><span data-stu-id="5d642-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="5d642-133">Volání obecných metod musí být zabalena do [explicitního Razor výrazu](#explicit-razor-expressions) nebo [ Razor bloku kódu](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="5d642-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="5d642-134">Explicitní Razor výrazy</span><span class="sxs-lookup"><span data-stu-id="5d642-134">Explicit Razor expressions</span></span>

<span data-ttu-id="5d642-135">Explicitní Razor výrazy se skládají ze `@` symbolu s vyrovnanými závorkami.</span><span class="sxs-lookup"><span data-stu-id="5d642-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="5d642-136">Chcete-li vykreslit čas posledního týdne, Razor je použit následující kód:</span><span class="sxs-lookup"><span data-stu-id="5d642-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="5d642-137">Veškerý obsah v `@()` závorkách je vyhodnocen a vykreslen do výstupu.</span><span class="sxs-lookup"><span data-stu-id="5d642-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="5d642-138">Implicitní výrazy popsané v předchozí části všeobecně nemůžou obsahovat mezery.</span><span class="sxs-lookup"><span data-stu-id="5d642-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="5d642-139">V následujícím kódu se jeden týden odečte od aktuálního času:</span><span class="sxs-lookup"><span data-stu-id="5d642-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="5d642-140">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="5d642-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="5d642-141">Explicitní výrazy lze použít k zřetězení textu s výsledkem výrazu:</span><span class="sxs-lookup"><span data-stu-id="5d642-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="5d642-142">Bez explicitního výrazu `<p>Age@joe.Age</p>` je zpracována jako e-mailová adresa a `<p>Age@joe.Age</p>` je vykreslena.</span><span class="sxs-lookup"><span data-stu-id="5d642-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="5d642-143">Je-li zapsán jako explicitní výraz, `<p>Age33</p>` je vykreslen.</span><span class="sxs-lookup"><span data-stu-id="5d642-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="5d642-144">Explicitní výrazy lze použít k vykreslení výstupu z obecných metod v souborech *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="5d642-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="5d642-145">Následující kód ukazuje, jak opravit chybu zobrazenou dříve, způsobenou závorkami obecných v jazyce C#.</span><span class="sxs-lookup"><span data-stu-id="5d642-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="5d642-146">Kód je zapsán jako explicitní výraz:</span><span class="sxs-lookup"><span data-stu-id="5d642-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="5d642-147">Kódování výrazu</span><span class="sxs-lookup"><span data-stu-id="5d642-147">Expression encoding</span></span>

<span data-ttu-id="5d642-148">Výrazy jazyka C#, které jsou vyhodnoceny jako řetězec, jsou kódovány HTML.</span><span class="sxs-lookup"><span data-stu-id="5d642-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="5d642-149">Výrazy jazyka C#, které se vyhodnotí, se `IHtmlContent` vykreslují přímo prostřednictvím `IHtmlContent.WriteTo` .</span><span class="sxs-lookup"><span data-stu-id="5d642-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="5d642-150">Výrazy jazyka C#, které nejsou vyhodnoceny, `IHtmlContent` jsou převedeny na řetězec pomocí `ToString` a kódovány před jejich vykreslením.</span><span class="sxs-lookup"><span data-stu-id="5d642-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="5d642-151">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="5d642-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="5d642-152">HTML se zobrazí v prohlížeči jako:</span><span class="sxs-lookup"><span data-stu-id="5d642-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="5d642-153">`HtmlHelper.Raw`výstup není kódovaný, ale vykresluje se jako kód HTML.</span><span class="sxs-lookup"><span data-stu-id="5d642-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="5d642-154">Použití `HtmlHelper.Raw` na neupraveném vstupu uživatele představuje bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="5d642-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="5d642-155">Vstup uživatele může obsahovat škodlivý JavaScript nebo jiné zneužití.</span><span class="sxs-lookup"><span data-stu-id="5d642-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="5d642-156">Upravení vstupu uživatele je obtížné.</span><span class="sxs-lookup"><span data-stu-id="5d642-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="5d642-157">Nepoužívejte `HtmlHelper.Raw` s uživatelským vstupem.</span><span class="sxs-lookup"><span data-stu-id="5d642-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="5d642-158">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="5d642-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Razor<span data-ttu-id="5d642-159">bloky kódu</span><span class="sxs-lookup"><span data-stu-id="5d642-159"> code blocks</span></span>

Razor<span data-ttu-id="5d642-160">bloky kódu začínají na `@` a jsou uzavřeny v `{}` .</span><span class="sxs-lookup"><span data-stu-id="5d642-160"> code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="5d642-161">Na rozdíl od výrazů není kód C# uvnitř bloků kódu vykreslen.</span><span class="sxs-lookup"><span data-stu-id="5d642-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="5d642-162">Bloky kódu a výrazy v zobrazení sdílejí stejný obor a jsou definovány v pořadí:</span><span class="sxs-lookup"><span data-stu-id="5d642-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="5d642-163">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="5d642-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5d642-164">V blocích kódu deklarujte [místní funkce](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) pomocí značek, které budou sloužit jako metody šablonování:</span><span class="sxs-lookup"><span data-stu-id="5d642-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="5d642-165">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="5d642-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="5d642-166">Implicitní přechody</span><span class="sxs-lookup"><span data-stu-id="5d642-166">Implicit transitions</span></span>

<span data-ttu-id="5d642-167">Výchozí jazyk v bloku kódu je C#, ale Razor Stránka může přejít zpět na kód HTML:</span><span class="sxs-lookup"><span data-stu-id="5d642-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="5d642-168">Explicitně oddělený přechod</span><span class="sxs-lookup"><span data-stu-id="5d642-168">Explicit delimited transition</span></span>

<span data-ttu-id="5d642-169">Chcete-li definovat dílčí oddíl bloku kódu, který by měl vykreslovat kód HTML, uzavřete znaky pro vykreslování se Razor `<text>` značkou:</span><span class="sxs-lookup"><span data-stu-id="5d642-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="5d642-170">Pomocí tohoto přístupu můžete vykreslit HTML, které není obklopené značkou HTML.</span><span class="sxs-lookup"><span data-stu-id="5d642-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="5d642-171">Bez HTML nebo Razor značky Razor dojde k chybě modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="5d642-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="5d642-172">`<text>`Značka je užitečná pro řízení prázdných znaků při vykreslování obsahu:</span><span class="sxs-lookup"><span data-stu-id="5d642-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="5d642-173">Vykreslí se jenom obsah mezi `<text>` značkou.</span><span class="sxs-lookup"><span data-stu-id="5d642-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="5d642-174">Před nebo po `<text>` značce se ve výstupu HTML nezobrazí žádné prázdné znaky.</span><span class="sxs-lookup"><span data-stu-id="5d642-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="5d642-175">Explicitní přechod řádku</span><span class="sxs-lookup"><span data-stu-id="5d642-175">Explicit line transition</span></span>

<span data-ttu-id="5d642-176">Chcete-li vykreslit zbytek celého řádku jako HTML v bloku kódu, použijte `@:` syntaxi:</span><span class="sxs-lookup"><span data-stu-id="5d642-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="5d642-177">Bez `@:` kódu v Razor je vygenerována Běhová chyba.</span><span class="sxs-lookup"><span data-stu-id="5d642-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="5d642-178">Nadbytečné `@` znaky v Razor souboru mohou způsobit chyby kompilátoru v příkazech později v bloku.</span><span class="sxs-lookup"><span data-stu-id="5d642-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="5d642-179">Tyto chyby kompilátoru mohou být obtížné pochopit, protože před nahlášenou chybou dojde k aktuální chybě.</span><span class="sxs-lookup"><span data-stu-id="5d642-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="5d642-180">Tato chyba je společná po Zkombinování více implicitních nebo explicitních výrazů do jednoho bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="5d642-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="5d642-181">Řídicí struktury</span><span class="sxs-lookup"><span data-stu-id="5d642-181">Control structures</span></span>

<span data-ttu-id="5d642-182">Řídicí struktury jsou rozšíření bloků kódu.</span><span class="sxs-lookup"><span data-stu-id="5d642-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="5d642-183">Všechny aspekty bloků kódu (přechod na kód, vložené C#) platí také pro následující struktury:</span><span class="sxs-lookup"><span data-stu-id="5d642-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="5d642-184">Podmíněné výrazy`@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="5d642-184">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="5d642-185">`@if`ovládací prvky při spuštění kódu:</span><span class="sxs-lookup"><span data-stu-id="5d642-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="5d642-186">`else`a `else if` nevyžadují `@` symbol:</span><span class="sxs-lookup"><span data-stu-id="5d642-186">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="5d642-187">Následující kód ukazuje, jak použít příkaz switch:</span><span class="sxs-lookup"><span data-stu-id="5d642-187">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="5d642-188">Opakování`@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="5d642-188">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="5d642-189">HTML s šablonou lze vykreslit pomocí příkazů pro opakování.</span><span class="sxs-lookup"><span data-stu-id="5d642-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="5d642-190">Vykreslit seznam osob:</span><span class="sxs-lookup"><span data-stu-id="5d642-190">To render a list of people:</span></span>

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

<span data-ttu-id="5d642-191">Podporovány jsou následující příkazy smyček:</span><span class="sxs-lookup"><span data-stu-id="5d642-191">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="5d642-192">Kombinovanou`@using`</span><span class="sxs-lookup"><span data-stu-id="5d642-192">Compound `@using`</span></span>

<span data-ttu-id="5d642-193">V jazyce C# se `using` k zajištění uvolnění objektu používá příkaz.</span><span class="sxs-lookup"><span data-stu-id="5d642-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="5d642-194">V nástroji se Razor stejný mechanismus používá k vytvoření pomocníků HTML, které obsahují další obsah.</span><span class="sxs-lookup"><span data-stu-id="5d642-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="5d642-195">V následujícím kódu pomocník HTML vykresluje `<form>` značku s `@using` příkazem:</span><span class="sxs-lookup"><span data-stu-id="5d642-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

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

<span data-ttu-id="5d642-196">Zpracování výjimek je podobné jazyku C#:</span><span class="sxs-lookup"><span data-stu-id="5d642-196">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

Razor<span data-ttu-id="5d642-197">má možnost chránit kritické oddíly pomocí příkazů Lock:</span><span class="sxs-lookup"><span data-stu-id="5d642-197"> has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="5d642-198">Komentáře</span><span class="sxs-lookup"><span data-stu-id="5d642-198">Comments</span></span>

Razor<span data-ttu-id="5d642-199">podporuje komentáře jazyka C# a HTML:</span><span class="sxs-lookup"><span data-stu-id="5d642-199"> supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="5d642-200">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="5d642-200">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

Razor<span data-ttu-id="5d642-201">před vykreslením webové stránky jsou komentáře odstraněny serverem.</span><span class="sxs-lookup"><span data-stu-id="5d642-201"> comments are removed by the server before the webpage is rendered.</span></span> Razor<span data-ttu-id="5d642-202">používá `@*  *@` se k vymezení komentářů.</span><span class="sxs-lookup"><span data-stu-id="5d642-202"> uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="5d642-203">Následující kód je zakomentován, takže server nevykresluje žádné značky:</span><span class="sxs-lookup"><span data-stu-id="5d642-203">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="5d642-204">Direktivy</span><span class="sxs-lookup"><span data-stu-id="5d642-204">Directives</span></span>

Razor<span data-ttu-id="5d642-205">direktivy jsou reprezentovány implicitními výrazy s vyhrazenými klíčovými slovy za `@` symbolem.</span><span class="sxs-lookup"><span data-stu-id="5d642-205"> directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="5d642-206">Direktiva obvykle mění způsob, jakým se analyzuje zobrazení, nebo umožňuje různé funkce.</span><span class="sxs-lookup"><span data-stu-id="5d642-206">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="5d642-207">Porozumění způsobu, jakým Razor generuje kód pro zobrazení, usnadňuje pochopení, jak direktivy fungují.</span><span class="sxs-lookup"><span data-stu-id="5d642-207">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="5d642-208">Kód generuje třídu podobnou této:</span><span class="sxs-lookup"><span data-stu-id="5d642-208">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="5d642-209">Později v tomto článku část [kontroluje Razor třídu jazyka C# vygenerovanou pro zobrazení](#inspect-the-razor-c-class-generated-for-a-view) vysvětluje, jak zobrazit tuto generovanou třídu.</span><span class="sxs-lookup"><span data-stu-id="5d642-209">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="5d642-210">`@attribute`Direktiva přidá daný atribut třídě vygenerované stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="5d642-210">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="5d642-211">Následující příklad přidá `[Authorize]` atribut:</span><span class="sxs-lookup"><span data-stu-id="5d642-211">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="5d642-212">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="5d642-212">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="5d642-213">`@code`Blok umožňuje [ Razor komponentě](xref:blazor/components/index) přidat členy jazyka C# (pole, vlastnosti a metody) do komponenty:</span><span class="sxs-lookup"><span data-stu-id="5d642-213">The `@code` block enables a [Razor component](xref:blazor/components/index) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="5d642-214">Pro Razor součásti `@code` je alias [`@functions`](#functions) a doporučený `@functions` .</span><span class="sxs-lookup"><span data-stu-id="5d642-214">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="5d642-215">Je přípustný více než jeden `@code` blok.</span><span class="sxs-lookup"><span data-stu-id="5d642-215">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="5d642-216">`@functions`Direktiva umožňuje přidat členy jazyka C# (pole, vlastnosti a metody) do generované třídy:</span><span class="sxs-lookup"><span data-stu-id="5d642-216">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5d642-217">V [ Razor komponentách](xref:blazor/components/index)použijte `@code` více než `@functions` k přidání členů jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="5d642-217">In [Razor components](xref:blazor/components/index), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="5d642-218">Například:</span><span class="sxs-lookup"><span data-stu-id="5d642-218">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="5d642-219">Kód generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="5d642-219">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="5d642-220">Následující kód je vygenerovaná Razor třída C#:</span><span class="sxs-lookup"><span data-stu-id="5d642-220">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5d642-221">`@functions`metody slouží jako metody šablonování, pokud mají značky:</span><span class="sxs-lookup"><span data-stu-id="5d642-221">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="5d642-222">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="5d642-222">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="5d642-223">`@implements`Direktiva implementuje rozhraní pro generovanou třídu.</span><span class="sxs-lookup"><span data-stu-id="5d642-223">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="5d642-224">Následující příklad implementuje <xref:System.IDisposable?displayProperty=fullName> , aby <xref:System.IDisposable.Dispose*> mohla být metoda volána:</span><span class="sxs-lookup"><span data-stu-id="5d642-224">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

<span data-ttu-id="5d642-225">`@inherits`Direktiva poskytuje úplnou kontrolu nad třídou, kterou zobrazení dědí:</span><span class="sxs-lookup"><span data-stu-id="5d642-225">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="5d642-226">Následující kód je vlastní Razor typ stránky:</span><span class="sxs-lookup"><span data-stu-id="5d642-226">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="5d642-227">`CustomText`Je zobrazen v zobrazení:</span><span class="sxs-lookup"><span data-stu-id="5d642-227">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="5d642-228">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="5d642-228">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="5d642-229">`@model`a `@inherits` lze jej použít ve stejném zobrazení.</span><span class="sxs-lookup"><span data-stu-id="5d642-229">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="5d642-230">`@inherits`může být v souboru *_ViewImports. cshtml* , který zobrazení importuje:</span><span class="sxs-lookup"><span data-stu-id="5d642-230">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="5d642-231">Následující kód je příkladem zobrazení silného typu:</span><span class="sxs-lookup"><span data-stu-id="5d642-231">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="5d642-232">Pokud rick@contoso.com je v modelu předána možnost "", zobrazení generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="5d642-232">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="5d642-233">`@inject`Direktiva umožňuje Razor stránce Vložit službu z [kontejneru služby](xref:fundamentals/dependency-injection) do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="5d642-233">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="5d642-234">Další informace najdete v tématu [vkládání závislostí do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5d642-234">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="5d642-235">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="5d642-235">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="5d642-236">`@layout`Direktiva určuje rozložení pro Razor komponentu.</span><span class="sxs-lookup"><span data-stu-id="5d642-236">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="5d642-237">Komponenty rozložení se používají k zamezení Duplikace kódu a nekonzistenci.</span><span class="sxs-lookup"><span data-stu-id="5d642-237">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="5d642-238">Další informace naleznete v tématu <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="5d642-238">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="5d642-239">*Tento scénář se vztahuje pouze na zobrazení a Razor stránky MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="5d642-239">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="5d642-240">`@model`Direktiva určuje typ modelu předaného na zobrazení nebo stránku:</span><span class="sxs-lookup"><span data-stu-id="5d642-240">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="5d642-241">V aplikaci ASP.NET Core MVC nebo Razor stránky vytvořené pomocí individuálních uživatelských účtů, *zobrazení/účet/přihlášení. cshtml* obsahuje následující deklaraci modelu:</span><span class="sxs-lookup"><span data-stu-id="5d642-241">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="5d642-242">Třída, ze které vygenerovala dědění `RazorPage<dynamic>` :</span><span class="sxs-lookup"><span data-stu-id="5d642-242">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor<span data-ttu-id="5d642-243">zpřístupní `Model` vlastnost pro přístup k modelu předanému zobrazení:</span><span class="sxs-lookup"><span data-stu-id="5d642-243"> exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="5d642-244">`@model`Direktiva určuje typ `Model` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5d642-244">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="5d642-245">Direktiva Určuje `T` , v jakém `RazorPage<T>` vygenerované třídě je odvozeno zobrazení.</span><span class="sxs-lookup"><span data-stu-id="5d642-245">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="5d642-246">Pokud `@model` není zadána direktiva, `Model` je vlastnost typu `dynamic` .</span><span class="sxs-lookup"><span data-stu-id="5d642-246">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="5d642-247">Další informace naleznete v tématu [modely silného typu a @model klíčové slovo](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="5d642-247">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="5d642-248">`@namespace`Direktiva:</span><span class="sxs-lookup"><span data-stu-id="5d642-248">The `@namespace` directive:</span></span>

* <span data-ttu-id="5d642-249">Nastaví obor názvů třídy vygenerované Razor stránky, zobrazení MVC nebo Razor součásti.</span><span class="sxs-lookup"><span data-stu-id="5d642-249">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="5d642-250">Nastaví kořenové obory názvů pro třídy stránky, zobrazení nebo komponenty z nejbližšího souboru importu ve stromové struktuře adresáře *_ViewImports. cshtml* (zobrazení nebo stránky) nebo *_Imports. Razor* ( Razor komponenty).</span><span class="sxs-lookup"><span data-stu-id="5d642-250">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="5d642-251">Pro Razor příklad stránek zobrazených v následující tabulce:</span><span class="sxs-lookup"><span data-stu-id="5d642-251">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="5d642-252">Každá stránka importuje *stránky/_ViewImports. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5d642-252">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="5d642-253">*Stránky/_ViewImports. cshtml* obsahuje `@namespace Hello.World` .</span><span class="sxs-lookup"><span data-stu-id="5d642-253">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="5d642-254">Každá stránka má `Hello.World` jako kořen oboru názvů IT.</span><span class="sxs-lookup"><span data-stu-id="5d642-254">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="5d642-255">stránka</span><span class="sxs-lookup"><span data-stu-id="5d642-255">Page</span></span>                                        | <span data-ttu-id="5d642-256">Obor názvů</span><span class="sxs-lookup"><span data-stu-id="5d642-256">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="5d642-257">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="5d642-257">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="5d642-258">*Pages/MorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="5d642-258">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="5d642-259">*Pages/MorePages/EvenMorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="5d642-259">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="5d642-260">Předchozí relace platí pro import souborů používaných s zobrazeními a Razor komponentami MVC.</span><span class="sxs-lookup"><span data-stu-id="5d642-260">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="5d642-261">Pokud má více souborů importu `@namespace` direktivu, soubor, který je nejblíže stránce, zobrazení nebo komponentě v adresářovém stromu, slouží k nastavení kořenového oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="5d642-261">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="5d642-262">Pokud má složka *EvenMorePages* v předchozím příkladu soubor Imports s `@namespace Another.Planet` (nebo soubor *Pages/MorePages/EvenMorePages/Page. cshtml* obsahuje `@namespace Another.Planet` ), výsledek je zobrazen v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="5d642-262">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="5d642-263">stránka</span><span class="sxs-lookup"><span data-stu-id="5d642-263">Page</span></span>                                        | <span data-ttu-id="5d642-264">Obor názvů</span><span class="sxs-lookup"><span data-stu-id="5d642-264">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="5d642-265">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="5d642-265">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="5d642-266">*Pages/MorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="5d642-266">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="5d642-267">*Pages/MorePages/EvenMorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="5d642-267">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5d642-268">`@page`Direktiva má jiné účinky v závislosti na typu souboru, ve kterém se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="5d642-268">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="5d642-269">Direktiva:</span><span class="sxs-lookup"><span data-stu-id="5d642-269">The directive:</span></span>

* <span data-ttu-id="5d642-270">V v souboru *. cshtml* znamená, že soubor je Razor Stránka.</span><span class="sxs-lookup"><span data-stu-id="5d642-270">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="5d642-271">Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes) a <xref:razor-pages/index> .</span><span class="sxs-lookup"><span data-stu-id="5d642-271">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="5d642-272">Určuje, že Razor komponenta má zpracovávat požadavky přímo.</span><span class="sxs-lookup"><span data-stu-id="5d642-272">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="5d642-273">Další informace naleznete v tématu <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="5d642-273">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5d642-274">`@page`Direktiva na prvním řádku souboru *. cshtml* znamená, že soubor je Razor Stránka.</span><span class="sxs-lookup"><span data-stu-id="5d642-274">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="5d642-275">Další informace naleznete v tématu <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="5d642-275">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="5d642-276">*Tento scénář se vztahuje pouze na zobrazení a Razor stránky MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="5d642-276">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="5d642-277">`@section`Direktiva se používá společně s [ Razor rozloženími MVC a stránky](xref:mvc/views/layout) k tomu, aby zobrazení nebo stránky vygenerovaly obsah v různých částech stránky HTML.</span><span class="sxs-lookup"><span data-stu-id="5d642-277">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="5d642-278">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="5d642-278">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="5d642-279">`@using`Direktiva přidá `using` direktivu jazyka C# do generovaného zobrazení:</span><span class="sxs-lookup"><span data-stu-id="5d642-279">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5d642-280">V [ Razor součástech](xref:blazor/components/index) `@using` také určuje, které součásti jsou v oboru.</span><span class="sxs-lookup"><span data-stu-id="5d642-280">In [Razor components](xref:blazor/components/index), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="5d642-281">Atributy direktiv</span><span class="sxs-lookup"><span data-stu-id="5d642-281">Directive attributes</span></span>

Razor<span data-ttu-id="5d642-282">atributy direktiv jsou reprezentovány implicitními výrazy s vyhrazenými klíčovými slovy za `@` symbolem.</span><span class="sxs-lookup"><span data-stu-id="5d642-282"> directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="5d642-283">Atribut direktivy obvykle mění způsob, jakým je prvek analyzován, nebo umožňuje různé funkce.</span><span class="sxs-lookup"><span data-stu-id="5d642-283">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="5d642-284">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="5d642-284">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="5d642-285">`@attributes`umožňuje komponentě vykreslovat nedeklarované atributy.</span><span class="sxs-lookup"><span data-stu-id="5d642-285">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="5d642-286">Další informace naleznete v tématu <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="5d642-286">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="5d642-287">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="5d642-287">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="5d642-288">Datové vazby v součástech se provádí s `@bind` atributem.</span><span class="sxs-lookup"><span data-stu-id="5d642-288">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="5d642-289">Další informace naleznete v tématu <xref:blazor/components/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="5d642-289">For more information, see <xref:blazor/components/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="5d642-290">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="5d642-290">*This scenario only applies to Razor components (.razor).*</span></span>

Razor<span data-ttu-id="5d642-291">poskytuje funkce pro zpracování událostí pro součásti.</span><span class="sxs-lookup"><span data-stu-id="5d642-291"> provides event handling features for components.</span></span> <span data-ttu-id="5d642-292">Další informace naleznete v tématu <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="5d642-292">For more information, see <xref:blazor/components/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="5d642-293">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="5d642-293">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="5d642-294">Zabrání výchozí akci pro událost.</span><span class="sxs-lookup"><span data-stu-id="5d642-294">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="5d642-295">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="5d642-295">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="5d642-296">Zastaví šíření události pro událost.</span><span class="sxs-lookup"><span data-stu-id="5d642-296">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="5d642-297">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="5d642-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="5d642-298">`@key`Atribut direktiva způsobí, že rozdílový algoritmus komponent garantuje zachování prvků nebo komponent na základě hodnoty klíče.</span><span class="sxs-lookup"><span data-stu-id="5d642-298">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="5d642-299">Další informace naleznete v tématu <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="5d642-299">For more information, see <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="5d642-300">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="5d642-300">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="5d642-301">Odkazy na součásti ( `@ref` ) poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance.</span><span class="sxs-lookup"><span data-stu-id="5d642-301">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="5d642-302">Další informace naleznete v tématu <xref:blazor/components/index#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="5d642-302">For more information, see <xref:blazor/components/index#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="5d642-303">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="5d642-303">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="5d642-304">`@typeparam`Direktiva deklaruje parametr obecného typu pro generovanou třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="5d642-304">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="5d642-305">Další informace naleznete v tématu <xref:blazor/components/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="5d642-305">For more information, see <xref:blazor/components/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="5d642-306">Zástupci přidaných šablon Razor</span><span class="sxs-lookup"><span data-stu-id="5d642-306">Templated Razor delegates</span></span>

Razor<span data-ttu-id="5d642-307">šablony umožňují definovat fragment uživatelského rozhraní v následujícím formátu:</span><span class="sxs-lookup"><span data-stu-id="5d642-307"> templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="5d642-308">Následující příklad ukazuje, jak zadat delegáta s šablonou Razor jako <xref:System.Func%602> .</span><span class="sxs-lookup"><span data-stu-id="5d642-308">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="5d642-309">[Dynamický typ](/dotnet/csharp/programming-guide/types/using-type-dynamic) je zadán pro parametr metody, kterou delegát zapouzdřuje.</span><span class="sxs-lookup"><span data-stu-id="5d642-309">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="5d642-310">[Typ objektu](/dotnet/csharp/language-reference/keywords/object) je zadán jako návratová hodnota delegáta.</span><span class="sxs-lookup"><span data-stu-id="5d642-310">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="5d642-311">Šablona se používá s <xref:System.Collections.Generic.List%601> `Pet` vlastností, která má `Name` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5d642-311">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="5d642-312">Šablona je vykreslena s `pets` dodaným `foreach` příkazem:</span><span class="sxs-lookup"><span data-stu-id="5d642-312">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="5d642-313">Vykreslený výstup:</span><span class="sxs-lookup"><span data-stu-id="5d642-313">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="5d642-314">Vloženou šablonu můžete také dodat Razor jako argument metodě.</span><span class="sxs-lookup"><span data-stu-id="5d642-314">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="5d642-315">V následujícím příkladu `Repeat` metoda obdrží Razor šablonu.</span><span class="sxs-lookup"><span data-stu-id="5d642-315">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="5d642-316">Metoda používá šablonu k vytvoření obsahu HTML s opakováním položek dodaných ze seznamu:</span><span class="sxs-lookup"><span data-stu-id="5d642-316">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="5d642-317">Pomocí seznamu domácích zvířat z předchozího příkladu `Repeat` je metoda volána s:</span><span class="sxs-lookup"><span data-stu-id="5d642-317">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="5d642-318"><xref:System.Collections.Generic.List%601>z `Pet` .</span><span class="sxs-lookup"><span data-stu-id="5d642-318"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="5d642-319">Počet opakování každé PET.</span><span class="sxs-lookup"><span data-stu-id="5d642-319">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="5d642-320">Vložená šablona, která se má použít pro položky seznamu neuspořádaného seznamu</span><span class="sxs-lookup"><span data-stu-id="5d642-320">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="5d642-321">Vykreslený výstup:</span><span class="sxs-lookup"><span data-stu-id="5d642-321">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="5d642-322">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="5d642-322">Tag Helpers</span></span>

<span data-ttu-id="5d642-323">*Tento scénář se vztahuje pouze na zobrazení a Razor stránky MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="5d642-323">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="5d642-324">Existují tři direktivy, které se týkají [pomocníků značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="5d642-324">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="5d642-325">Směrnici</span><span class="sxs-lookup"><span data-stu-id="5d642-325">Directive</span></span> | <span data-ttu-id="5d642-326">Funkce</span><span class="sxs-lookup"><span data-stu-id="5d642-326">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="5d642-327">Zpřístupní pomocníkům značek zobrazení.</span><span class="sxs-lookup"><span data-stu-id="5d642-327">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="5d642-328">Odebere pomocníky značek dříve přidané ze zobrazení.</span><span class="sxs-lookup"><span data-stu-id="5d642-328">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="5d642-329">Určuje předponu značky pro povolení podpory značek pomocníka a k explicitnímu použití pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="5d642-329">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a>Razor<span data-ttu-id="5d642-330">vyhrazená klíčová slova</span><span class="sxs-lookup"><span data-stu-id="5d642-330"> reserved keywords</span></span>

### <a name="razor-keywords"></a>Razor<span data-ttu-id="5d642-331">klíčov</span><span class="sxs-lookup"><span data-stu-id="5d642-331"> keywords</span></span>

* <span data-ttu-id="5d642-332">`page`(Vyžaduje ASP.NET Core 2,1 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="5d642-332">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="5d642-333">`helper`(Aktuálně není podporováno ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="5d642-333">`helper` (Not currently supported by ASP.NET Core)</span></span>

Razor<span data-ttu-id="5d642-334">Klíčová slova jsou uvozena znakem `@(Razor Keyword)` (například `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="5d642-334"> keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="5d642-335">RazorKlíčová slova jazyka C#</span><span class="sxs-lookup"><span data-stu-id="5d642-335">C# Razor keywords</span></span>

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

<span data-ttu-id="5d642-336">RazorKlíčová slova jazyka C# musí mít dvojité uvození řídicím znakem `@(@C# Razor Keyword)` (například `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="5d642-336">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="5d642-337">První `@` řídí Razor analyzátor.</span><span class="sxs-lookup"><span data-stu-id="5d642-337">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="5d642-338">Druhý `@` řídí analyzátor jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="5d642-338">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="5d642-339">Vyhrazená klíčová slova, která nepoužíváRazor</span><span class="sxs-lookup"><span data-stu-id="5d642-339">Reserved keywords not used by Razor</span></span>

* `class`

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="5d642-340">Kontrola Razor třídy jazyka C# vygenerované pro zobrazení</span><span class="sxs-lookup"><span data-stu-id="5d642-340">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="5d642-341">Pomocí .NET Core SDK 2,1 nebo novější [ Razor sada SDK](xref:razor-pages/sdk) zpracovává kompilaci Razor souborů.</span><span class="sxs-lookup"><span data-stu-id="5d642-341">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="5d642-342">Při sestavování projektu Razor generuje sada SDK objekt \*obj/<build_configuration>/<target_framework_moniker>/ Razor \* adresář v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="5d642-342">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="5d642-343">Adresářová struktura v *Razor* adresáři zrcadlí strukturu adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="5d642-343">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="5d642-344">Vezměte v úvahu následující adresářovou strukturu v projektu stránky ASP.NET Core 2,1 s Razor cílením na rozhraní .NET Core 2,1:</span><span class="sxs-lookup"><span data-stu-id="5d642-344">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

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

<span data-ttu-id="5d642-345">Sestavení projektu v konfiguraci *ladění* má za následek následující adresář *obj* :</span><span class="sxs-lookup"><span data-stu-id="5d642-345">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

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

<span data-ttu-id="5d642-346">Chcete-li zobrazit vygenerovanou třídu pro *pages/index. cshtml*, otevřete *obj/ladění/netcoreapp 2.1/ Razor /pages/index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="5d642-346">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="5d642-347">Přidejte následující třídu do projektu ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="5d642-347">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="5d642-348">V aplikaci `Startup.ConfigureServices` přepište `RazorTemplateEngine` přidanou `CustomTemplateEngine` třídou MVC pomocí třídy:</span><span class="sxs-lookup"><span data-stu-id="5d642-348">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="5d642-349">Nastavte zarážku na `return csharpDocument;` příkazu `CustomTemplateEngine` .</span><span class="sxs-lookup"><span data-stu-id="5d642-349">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="5d642-350">Když se provádění programu zastaví na zarážce, zobrazte hodnotu `generatedCode` .</span><span class="sxs-lookup"><span data-stu-id="5d642-350">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Zobrazení Vizualizér textu pro generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="5d642-352">Zobrazit hledání a rozlišovat velká a malá písmena</span><span class="sxs-lookup"><span data-stu-id="5d642-352">View lookups and case sensitivity</span></span>

<span data-ttu-id="5d642-353">RazorModul zobrazení provádí pro zobrazení vyhledávání citlivá na velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="5d642-353">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="5d642-354">Skutečné vyhledávání je však určeno základním systémem souborů:</span><span class="sxs-lookup"><span data-stu-id="5d642-354">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="5d642-355">Zdroj založený na souborech:</span><span class="sxs-lookup"><span data-stu-id="5d642-355">File based source:</span></span>
  * <span data-ttu-id="5d642-356">V operačních systémech se systémy souborů bez rozlišení velkých a malých písmen (například Windows) se u hledání fyzického zprostředkovatele souborů nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="5d642-356">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="5d642-357">Výsledkem je například `return View("Test")` shoda pro */views/Home/test.cshtml*, */views/Home/test.cshtml*a jakékoliv jiné varianty velikosti písmen.</span><span class="sxs-lookup"><span data-stu-id="5d642-357">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="5d642-358">V systémech souborů s rozlišováním velkých a malých písmen (například Linux, OSX a with `EmbeddedFileProvider` ) se při hledání rozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="5d642-358">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="5d642-359">`return View("Test")`Konkrétně například odpovídá */views/Home/test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5d642-359">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="5d642-360">Předkompilovaná zobrazení: u ASP.NET Core 2,0 a novějších, hledání předkompilovaných zobrazení rozlišuje velká a malá písmena ve všech operačních systémech.</span><span class="sxs-lookup"><span data-stu-id="5d642-360">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="5d642-361">Chování je stejné jako chování poskytovatele fyzického souboru ve Windows.</span><span class="sxs-lookup"><span data-stu-id="5d642-361">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="5d642-362">Pokud se dvě Předkompilovaná zobrazení liší pouze v případě, výsledek hledání není deterministický.</span><span class="sxs-lookup"><span data-stu-id="5d642-362">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="5d642-363">Vývojářům doporučujeme, aby se shodovaly s velkými písmeny názvů souborů a adresářů na velká a malá písmena:</span><span class="sxs-lookup"><span data-stu-id="5d642-363">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="5d642-364">Názvy oblastí, kontrolérů a akcí.</span><span class="sxs-lookup"><span data-stu-id="5d642-364">Area, controller, and action names.</span></span>
* Razor<span data-ttu-id="5d642-365">Stránky.</span><span class="sxs-lookup"><span data-stu-id="5d642-365"> Pages.</span></span>

<span data-ttu-id="5d642-366">V případě, že se rozlišuje velká a malá písmena, zajistíte jejich zobrazení bez ohledu na základní systém souborů.</span><span class="sxs-lookup"><span data-stu-id="5d642-366">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5d642-367">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5d642-367">Additional resources</span></span>

<span data-ttu-id="5d642-368">[Seznámení s ASP.NET webovým programováním pomocí Razor Syntaxe](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) poskytuje mnoho ukázek programování s Razor syntaxí.</span><span class="sxs-lookup"><span data-stu-id="5d642-368">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
