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
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="f89db-103">Razor Referenční informace k syntaxi pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f89db-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="f89db-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)a [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="f89db-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="f89db-105">Razor je syntaxe značek pro vložení kódu založeného na serveru na webové stránky.</span><span class="sxs-lookup"><span data-stu-id="f89db-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="f89db-106">RazorSyntaxe se skládá z Razor značek, C# a HTML.</span><span class="sxs-lookup"><span data-stu-id="f89db-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="f89db-107">Soubory, Razor které obsahují obecně, mají příponu *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f89db-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="f89db-108">Razorse nachází také v souborech [ Razor komponent](xref:blazor/components/index) (*. Razor*).</span><span class="sxs-lookup"><span data-stu-id="f89db-108">Razor is also found in [Razor components](xref:blazor/components/index) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="f89db-109">Vykreslování kódu HTML</span><span class="sxs-lookup"><span data-stu-id="f89db-109">Rendering HTML</span></span>

<span data-ttu-id="f89db-110">Výchozí Razor jazyk je HTML.</span><span class="sxs-lookup"><span data-stu-id="f89db-110">The default Razor language is HTML.</span></span> <span data-ttu-id="f89db-111">Vykreslování HTML ze Razor značek se neliší od vykreslování HTML ze souboru HTML.</span><span class="sxs-lookup"><span data-stu-id="f89db-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="f89db-112">Kód HTML v souborech *. cshtml* Razor je vykreslen serverem beze změny.</span><span class="sxs-lookup"><span data-stu-id="f89db-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="no-locrazor-syntax"></a><span data-ttu-id="f89db-113">Razor syntaktick</span><span class="sxs-lookup"><span data-stu-id="f89db-113">Razor syntax</span></span>

<span data-ttu-id="f89db-114">Razor podporuje jazyk C# a používá `@` symbol k přechodu z formátu HTML do C#.</span><span class="sxs-lookup"><span data-stu-id="f89db-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="f89db-115">Razor vyhodnotí výrazy jazyka C# a vykresluje je ve výstupu HTML.</span><span class="sxs-lookup"><span data-stu-id="f89db-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="f89db-116">`@`Je-li symbol následován [ Razor vyhrazeným klíčovým slovem](#razor-reserved-keywords), přechází na Razor konkrétní značku.</span><span class="sxs-lookup"><span data-stu-id="f89db-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="f89db-117">V opačném případě přejde do jednoduchého jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="f89db-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="f89db-118">Chcete-li `@` v kódu řídicí znak Razor , použijte druhý `@` symbol:</span><span class="sxs-lookup"><span data-stu-id="f89db-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="f89db-119">Kód je vykreslen ve formátu HTML s jedním `@` symbolem:</span><span class="sxs-lookup"><span data-stu-id="f89db-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="f89db-120">Atributy HTML a obsah obsahující e-mailové adresy se symbol nepovažují `@` jako znak přechodu.</span><span class="sxs-lookup"><span data-stu-id="f89db-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="f89db-121">E-mailové adresy v následujícím příkladu nejsou vyretušované Razor analýzou:</span><span class="sxs-lookup"><span data-stu-id="f89db-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a><span data-ttu-id="f89db-122">Implicitní Razor výrazy</span><span class="sxs-lookup"><span data-stu-id="f89db-122">Implicit Razor expressions</span></span>

<span data-ttu-id="f89db-123">Implicitní Razor výrazy začínají `@` následováním kódu jazyka C#:</span><span class="sxs-lookup"><span data-stu-id="f89db-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="f89db-124">S výjimkou `await` klíčového slova jazyka C# nesmí implicitní výrazy obsahovat mezery.</span><span class="sxs-lookup"><span data-stu-id="f89db-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="f89db-125">Pokud má příkaz jazyka C# jasný konec, mezery mohou být smíšené:</span><span class="sxs-lookup"><span data-stu-id="f89db-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="f89db-126">Implicitní výrazy **nemůžou** obsahovat obecné typy C#, protože znaky uvnitř závorek ( `<>` ) jsou interpretovány jako značka jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="f89db-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="f89db-127">Následující kód **není platný:**</span><span class="sxs-lookup"><span data-stu-id="f89db-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="f89db-128">Předchozí kód vygeneruje chybu kompilátoru podobný jednomu z následujících:</span><span class="sxs-lookup"><span data-stu-id="f89db-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="f89db-129">Element int se nezavřel.</span><span class="sxs-lookup"><span data-stu-id="f89db-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="f89db-130">Všechny elementy musí být buď samouzavírací, nebo musí mít stejnou koncovou značku.</span><span class="sxs-lookup"><span data-stu-id="f89db-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="f89db-131">Nejde převést skupinu metod ' GenericMethod ' na Nedelegovaný typ ' Object '.</span><span class="sxs-lookup"><span data-stu-id="f89db-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="f89db-132">Chtěli jste vyvolat metodu?</span><span class="sxs-lookup"><span data-stu-id="f89db-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="f89db-133">Volání obecných metod musí být zabalena do [explicitního Razor výrazu](#explicit-razor-expressions) nebo [ Razor bloku kódu](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="f89db-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-no-locrazor-expressions"></a><span data-ttu-id="f89db-134">Explicitní Razor výrazy</span><span class="sxs-lookup"><span data-stu-id="f89db-134">Explicit Razor expressions</span></span>

<span data-ttu-id="f89db-135">Explicitní Razor výrazy se skládají ze `@` symbolu s vyrovnanými závorkami.</span><span class="sxs-lookup"><span data-stu-id="f89db-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="f89db-136">Chcete-li vykreslit čas posledního týdne, Razor je použit následující kód:</span><span class="sxs-lookup"><span data-stu-id="f89db-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="f89db-137">Veškerý obsah v `@()` závorkách je vyhodnocen a vykreslen do výstupu.</span><span class="sxs-lookup"><span data-stu-id="f89db-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="f89db-138">Implicitní výrazy popsané v předchozí části všeobecně nemůžou obsahovat mezery.</span><span class="sxs-lookup"><span data-stu-id="f89db-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="f89db-139">V následujícím kódu se jeden týden odečte od aktuálního času:</span><span class="sxs-lookup"><span data-stu-id="f89db-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="f89db-140">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="f89db-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="f89db-141">Explicitní výrazy lze použít k zřetězení textu s výsledkem výrazu:</span><span class="sxs-lookup"><span data-stu-id="f89db-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="f89db-142">Bez explicitního výrazu `<p>Age@joe.Age</p>` je zpracována jako e-mailová adresa a `<p>Age@joe.Age</p>` je vykreslena.</span><span class="sxs-lookup"><span data-stu-id="f89db-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="f89db-143">Je-li zapsán jako explicitní výraz, `<p>Age33</p>` je vykreslen.</span><span class="sxs-lookup"><span data-stu-id="f89db-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="f89db-144">Explicitní výrazy lze použít k vykreslení výstupu z obecných metod v souborech *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f89db-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="f89db-145">Následující kód ukazuje, jak opravit chybu zobrazenou dříve, způsobenou závorkami obecných v jazyce C#.</span><span class="sxs-lookup"><span data-stu-id="f89db-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="f89db-146">Kód je zapsán jako explicitní výraz:</span><span class="sxs-lookup"><span data-stu-id="f89db-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="f89db-147">Kódování výrazu</span><span class="sxs-lookup"><span data-stu-id="f89db-147">Expression encoding</span></span>

<span data-ttu-id="f89db-148">Výrazy jazyka C#, které jsou vyhodnoceny jako řetězec, jsou kódovány HTML.</span><span class="sxs-lookup"><span data-stu-id="f89db-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="f89db-149">Výrazy jazyka C#, které se vyhodnotí, se `IHtmlContent` vykreslují přímo prostřednictvím `IHtmlContent.WriteTo` .</span><span class="sxs-lookup"><span data-stu-id="f89db-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="f89db-150">Výrazy jazyka C#, které nejsou vyhodnoceny, `IHtmlContent` jsou převedeny na řetězec pomocí `ToString` a kódovány před jejich vykreslením.</span><span class="sxs-lookup"><span data-stu-id="f89db-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="f89db-151">Předchozí kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="f89db-151">The preceding code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="f89db-152">HTML se zobrazí v prohlížeči jako prostý text:</span><span class="sxs-lookup"><span data-stu-id="f89db-152">The HTML is shown in the browser as plain text:</span></span>

<span data-ttu-id="f89db-153">&lt;rozpětí &gt; Hello World &lt; /span&gt;</span><span class="sxs-lookup"><span data-stu-id="f89db-153">&lt;span&gt;Hello World&lt;/span&gt;</span></span>

<span data-ttu-id="f89db-154">`HtmlHelper.Raw` výstup není kódovaný, ale vykresluje se jako kód HTML.</span><span class="sxs-lookup"><span data-stu-id="f89db-154">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="f89db-155">Použití `HtmlHelper.Raw` na neupraveném vstupu uživatele představuje bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="f89db-155">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="f89db-156">Vstup uživatele může obsahovat škodlivý JavaScript nebo jiné zneužití.</span><span class="sxs-lookup"><span data-stu-id="f89db-156">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="f89db-157">Upravení vstupu uživatele je obtížné.</span><span class="sxs-lookup"><span data-stu-id="f89db-157">Sanitizing user input is difficult.</span></span> <span data-ttu-id="f89db-158">Nepoužívejte `HtmlHelper.Raw` s uživatelským vstupem.</span><span class="sxs-lookup"><span data-stu-id="f89db-158">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="f89db-159">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="f89db-159">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a><span data-ttu-id="f89db-160">Razor bloky kódu</span><span class="sxs-lookup"><span data-stu-id="f89db-160">Razor code blocks</span></span>

<span data-ttu-id="f89db-161">Razor bloky kódu začínají na `@` a jsou uzavřeny v `{}` .</span><span class="sxs-lookup"><span data-stu-id="f89db-161">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="f89db-162">Na rozdíl od výrazů není kód C# uvnitř bloků kódu vykreslen.</span><span class="sxs-lookup"><span data-stu-id="f89db-162">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="f89db-163">Bloky kódu a výrazy v zobrazení sdílejí stejný obor a jsou definovány v pořadí:</span><span class="sxs-lookup"><span data-stu-id="f89db-163">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="f89db-164">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="f89db-164">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f89db-165">V blocích kódu deklarujte [místní funkce](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) pomocí značek, které budou sloužit jako metody šablonování:</span><span class="sxs-lookup"><span data-stu-id="f89db-165">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="f89db-166">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="f89db-166">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="f89db-167">Implicitní přechody</span><span class="sxs-lookup"><span data-stu-id="f89db-167">Implicit transitions</span></span>

<span data-ttu-id="f89db-168">Výchozí jazyk v bloku kódu je C#, ale Razor Stránka může přejít zpět na kód HTML:</span><span class="sxs-lookup"><span data-stu-id="f89db-168">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="f89db-169">Explicitně oddělený přechod</span><span class="sxs-lookup"><span data-stu-id="f89db-169">Explicit delimited transition</span></span>

<span data-ttu-id="f89db-170">Chcete-li definovat dílčí oddíl bloku kódu, který by měl vykreslovat kód HTML, uzavřete znaky pro vykreslování se Razor `<text>` značkou:</span><span class="sxs-lookup"><span data-stu-id="f89db-170">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="f89db-171">Pomocí tohoto přístupu můžete vykreslit HTML, které není obklopené značkou HTML.</span><span class="sxs-lookup"><span data-stu-id="f89db-171">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="f89db-172">Bez HTML nebo Razor značky Razor dojde k chybě modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="f89db-172">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="f89db-173">`<text>`Značka je užitečná pro řízení prázdných znaků při vykreslování obsahu:</span><span class="sxs-lookup"><span data-stu-id="f89db-173">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="f89db-174">Vykreslí se jenom obsah mezi `<text>` značkou.</span><span class="sxs-lookup"><span data-stu-id="f89db-174">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="f89db-175">Před nebo po `<text>` značce se ve výstupu HTML nezobrazí žádné prázdné znaky.</span><span class="sxs-lookup"><span data-stu-id="f89db-175">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="f89db-176">Explicitní přechod řádku</span><span class="sxs-lookup"><span data-stu-id="f89db-176">Explicit line transition</span></span>

<span data-ttu-id="f89db-177">Chcete-li vykreslit zbytek celého řádku jako HTML v bloku kódu, použijte `@:` syntaxi:</span><span class="sxs-lookup"><span data-stu-id="f89db-177">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="f89db-178">Bez `@:` kódu v Razor je vygenerována Běhová chyba.</span><span class="sxs-lookup"><span data-stu-id="f89db-178">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="f89db-179">Nadbytečné `@` znaky v Razor souboru mohou způsobit chyby kompilátoru v příkazech později v bloku.</span><span class="sxs-lookup"><span data-stu-id="f89db-179">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="f89db-180">Tyto chyby kompilátoru mohou být obtížné pochopit, protože před nahlášenou chybou dojde k aktuální chybě.</span><span class="sxs-lookup"><span data-stu-id="f89db-180">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="f89db-181">Tato chyba je společná po Zkombinování více implicitních nebo explicitních výrazů do jednoho bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="f89db-181">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="f89db-182">Řídicí struktury</span><span class="sxs-lookup"><span data-stu-id="f89db-182">Control structures</span></span>

<span data-ttu-id="f89db-183">Řídicí struktury jsou rozšíření bloků kódu.</span><span class="sxs-lookup"><span data-stu-id="f89db-183">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="f89db-184">Všechny aspekty bloků kódu (přechod na kód, vložené C#) platí také pro následující struktury:</span><span class="sxs-lookup"><span data-stu-id="f89db-184">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="f89db-185">Podmíněné výrazy `@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="f89db-185">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="f89db-186">`@if` ovládací prvky při spuštění kódu:</span><span class="sxs-lookup"><span data-stu-id="f89db-186">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="f89db-187">`else` a `else if` nevyžadují `@` symbol:</span><span class="sxs-lookup"><span data-stu-id="f89db-187">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="f89db-188">Následující kód ukazuje, jak použít příkaz switch:</span><span class="sxs-lookup"><span data-stu-id="f89db-188">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="f89db-189">Opakování `@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="f89db-189">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="f89db-190">HTML s šablonou lze vykreslit pomocí příkazů pro opakování.</span><span class="sxs-lookup"><span data-stu-id="f89db-190">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="f89db-191">Vykreslit seznam osob:</span><span class="sxs-lookup"><span data-stu-id="f89db-191">To render a list of people:</span></span>

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

<span data-ttu-id="f89db-192">Podporovány jsou následující příkazy smyček:</span><span class="sxs-lookup"><span data-stu-id="f89db-192">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="f89db-193">Kombinovanou `@using`</span><span class="sxs-lookup"><span data-stu-id="f89db-193">Compound `@using`</span></span>

<span data-ttu-id="f89db-194">V jazyce C# se `using` k zajištění uvolnění objektu používá příkaz.</span><span class="sxs-lookup"><span data-stu-id="f89db-194">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="f89db-195">V nástroji se Razor stejný mechanismus používá k vytvoření pomocníků HTML, které obsahují další obsah.</span><span class="sxs-lookup"><span data-stu-id="f89db-195">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="f89db-196">V následujícím kódu pomocník HTML vykresluje `<form>` značku s `@using` příkazem:</span><span class="sxs-lookup"><span data-stu-id="f89db-196">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

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

<span data-ttu-id="f89db-197">Zpracování výjimek je podobné jazyku C#:</span><span class="sxs-lookup"><span data-stu-id="f89db-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="f89db-198">Razor má možnost chránit kritické oddíly pomocí příkazů Lock:</span><span class="sxs-lookup"><span data-stu-id="f89db-198">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="f89db-199">Komentáře</span><span class="sxs-lookup"><span data-stu-id="f89db-199">Comments</span></span>

<span data-ttu-id="f89db-200">Razor podporuje komentáře jazyka C# a HTML:</span><span class="sxs-lookup"><span data-stu-id="f89db-200">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="f89db-201">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="f89db-201">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="f89db-202">Razor před vykreslením webové stránky jsou komentáře odstraněny serverem.</span><span class="sxs-lookup"><span data-stu-id="f89db-202">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="f89db-203">Razor používá `@*  *@` se k vymezení komentářů.</span><span class="sxs-lookup"><span data-stu-id="f89db-203">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="f89db-204">Následující kód je zakomentován, takže server nevykresluje žádné značky:</span><span class="sxs-lookup"><span data-stu-id="f89db-204">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="f89db-205">Direktivy</span><span class="sxs-lookup"><span data-stu-id="f89db-205">Directives</span></span>

<span data-ttu-id="f89db-206">Razor direktivy jsou reprezentovány implicitními výrazy s vyhrazenými klíčovými slovy za `@` symbolem.</span><span class="sxs-lookup"><span data-stu-id="f89db-206">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="f89db-207">Direktiva obvykle mění způsob, jakým se analyzuje zobrazení, nebo umožňuje různé funkce.</span><span class="sxs-lookup"><span data-stu-id="f89db-207">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="f89db-208">Porozumění způsobu, jakým Razor generuje kód pro zobrazení, usnadňuje pochopení, jak direktivy fungují.</span><span class="sxs-lookup"><span data-stu-id="f89db-208">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="f89db-209">Kód generuje třídu podobnou této:</span><span class="sxs-lookup"><span data-stu-id="f89db-209">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="f89db-210">Později v tomto článku část [kontroluje Razor třídu jazyka C# vygenerovanou pro zobrazení](#inspect-the-razor-c-class-generated-for-a-view) vysvětluje, jak zobrazit tuto generovanou třídu.</span><span class="sxs-lookup"><span data-stu-id="f89db-210">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="f89db-211">`@attribute`Direktiva přidá daný atribut třídě vygenerované stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f89db-211">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="f89db-212">Následující příklad přidá `[Authorize]` atribut:</span><span class="sxs-lookup"><span data-stu-id="f89db-212">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="f89db-213">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="f89db-213">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f89db-214">`@code`Blok umožňuje [ Razor komponentě](xref:blazor/components/index) přidat členy jazyka C# (pole, vlastnosti a metody) do komponenty:</span><span class="sxs-lookup"><span data-stu-id="f89db-214">The `@code` block enables a [Razor component](xref:blazor/components/index) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="f89db-215">Pro Razor součásti `@code` je alias [`@functions`](#functions) a doporučený `@functions` .</span><span class="sxs-lookup"><span data-stu-id="f89db-215">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="f89db-216">Je přípustný více než jeden `@code` blok.</span><span class="sxs-lookup"><span data-stu-id="f89db-216">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="f89db-217">`@functions`Direktiva umožňuje přidat členy jazyka C# (pole, vlastnosti a metody) do generované třídy:</span><span class="sxs-lookup"><span data-stu-id="f89db-217">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f89db-218">V [ Razor komponentách](xref:blazor/components/index)použijte `@code` více než `@functions` k přidání členů jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="f89db-218">In [Razor components](xref:blazor/components/index), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="f89db-219">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f89db-219">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="f89db-220">Kód generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="f89db-220">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="f89db-221">Následující kód je vygenerovaná Razor třída C#:</span><span class="sxs-lookup"><span data-stu-id="f89db-221">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f89db-222">`@functions` metody slouží jako metody šablonování, pokud mají značky:</span><span class="sxs-lookup"><span data-stu-id="f89db-222">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="f89db-223">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="f89db-223">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="f89db-224">`@implements`Direktiva implementuje rozhraní pro generovanou třídu.</span><span class="sxs-lookup"><span data-stu-id="f89db-224">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="f89db-225">Následující příklad implementuje <xref:System.IDisposable?displayProperty=fullName> , aby <xref:System.IDisposable.Dispose*> mohla být metoda volána:</span><span class="sxs-lookup"><span data-stu-id="f89db-225">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

<span data-ttu-id="f89db-226">`@inherits`Direktiva poskytuje úplnou kontrolu nad třídou, kterou zobrazení dědí:</span><span class="sxs-lookup"><span data-stu-id="f89db-226">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="f89db-227">Následující kód je vlastní Razor typ stránky:</span><span class="sxs-lookup"><span data-stu-id="f89db-227">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="f89db-228">`CustomText`Je zobrazen v zobrazení:</span><span class="sxs-lookup"><span data-stu-id="f89db-228">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="f89db-229">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="f89db-229">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="f89db-230">`@model` a `@inherits` lze jej použít ve stejném zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f89db-230">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="f89db-231">`@inherits` může být v souboru *_ViewImports. cshtml* , který zobrazení importuje:</span><span class="sxs-lookup"><span data-stu-id="f89db-231">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="f89db-232">Následující kód je příkladem zobrazení silného typu:</span><span class="sxs-lookup"><span data-stu-id="f89db-232">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="f89db-233">Pokud rick@contoso.com je v modelu předána možnost "", zobrazení generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="f89db-233">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="f89db-234">`@inject`Direktiva umožňuje Razor stránce Vložit službu z [kontejneru služby](xref:fundamentals/dependency-injection) do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f89db-234">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="f89db-235">Další informace najdete v tématu [vkládání závislostí do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f89db-235">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="f89db-236">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="f89db-236">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f89db-237">`@layout`Direktiva určuje rozložení pro Razor komponentu.</span><span class="sxs-lookup"><span data-stu-id="f89db-237">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="f89db-238">Komponenty rozložení se používají k zamezení Duplikace kódu a nekonzistenci.</span><span class="sxs-lookup"><span data-stu-id="f89db-238">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="f89db-239">Další informace naleznete v tématu <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="f89db-239">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="f89db-240">*Tento scénář se vztahuje pouze na zobrazení a Razor stránky MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="f89db-240">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="f89db-241">`@model`Direktiva určuje typ modelu předaného na zobrazení nebo stránku:</span><span class="sxs-lookup"><span data-stu-id="f89db-241">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="f89db-242">V aplikaci ASP.NET Core MVC nebo Razor stránky vytvořené pomocí individuálních uživatelských účtů, *zobrazení/účet/přihlášení. cshtml* obsahuje následující deklaraci modelu:</span><span class="sxs-lookup"><span data-stu-id="f89db-242">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="f89db-243">Třída, ze které vygenerovala dědění `RazorPage<dynamic>` :</span><span class="sxs-lookup"><span data-stu-id="f89db-243">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="f89db-244">Razor zpřístupní `Model` vlastnost pro přístup k modelu předanému zobrazení:</span><span class="sxs-lookup"><span data-stu-id="f89db-244">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="f89db-245">`@model`Direktiva určuje typ `Model` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="f89db-245">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="f89db-246">Direktiva Určuje `T` , v jakém `RazorPage<T>` vygenerované třídě je odvozeno zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f89db-246">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="f89db-247">Pokud `@model` není zadána direktiva, `Model` je vlastnost typu `dynamic` .</span><span class="sxs-lookup"><span data-stu-id="f89db-247">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="f89db-248">Další informace naleznete v tématu [modely silného typu a @model klíčové slovo](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="f89db-248">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="f89db-249">`@namespace`Direktiva:</span><span class="sxs-lookup"><span data-stu-id="f89db-249">The `@namespace` directive:</span></span>

* <span data-ttu-id="f89db-250">Nastaví obor názvů třídy vygenerované Razor stránky, zobrazení MVC nebo Razor součásti.</span><span class="sxs-lookup"><span data-stu-id="f89db-250">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="f89db-251">Nastaví kořenové obory názvů pro třídy stránky, zobrazení nebo komponenty z nejbližšího souboru importu ve stromové struktuře adresáře *_ViewImports. cshtml* (zobrazení nebo stránky) nebo *_Imports. Razor* ( Razor komponenty).</span><span class="sxs-lookup"><span data-stu-id="f89db-251">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="f89db-252">Pro Razor příklad stránek zobrazených v následující tabulce:</span><span class="sxs-lookup"><span data-stu-id="f89db-252">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="f89db-253">Každá stránka importuje *stránky/_ViewImports. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f89db-253">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="f89db-254">*Stránky/_ViewImports. cshtml* obsahuje `@namespace Hello.World` .</span><span class="sxs-lookup"><span data-stu-id="f89db-254">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="f89db-255">Každá stránka má `Hello.World` jako kořen oboru názvů IT.</span><span class="sxs-lookup"><span data-stu-id="f89db-255">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="f89db-256">Stránka</span><span class="sxs-lookup"><span data-stu-id="f89db-256">Page</span></span>                                        | <span data-ttu-id="f89db-257">Obor názvů</span><span class="sxs-lookup"><span data-stu-id="f89db-257">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="f89db-258">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f89db-258">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="f89db-259">*Pages/MorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f89db-259">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="f89db-260">*Pages/MorePages/EvenMorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f89db-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="f89db-261">Předchozí relace platí pro import souborů používaných s zobrazeními a Razor komponentami MVC.</span><span class="sxs-lookup"><span data-stu-id="f89db-261">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="f89db-262">Pokud má více souborů importu `@namespace` direktivu, soubor, který je nejblíže stránce, zobrazení nebo komponentě v adresářovém stromu, slouží k nastavení kořenového oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="f89db-262">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="f89db-263">Pokud má složka *EvenMorePages* v předchozím příkladu soubor Imports s `@namespace Another.Planet` (nebo soubor *Pages/MorePages/EvenMorePages/Page. cshtml* obsahuje `@namespace Another.Planet` ), výsledek je zobrazen v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="f89db-263">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="f89db-264">Stránka</span><span class="sxs-lookup"><span data-stu-id="f89db-264">Page</span></span>                                        | <span data-ttu-id="f89db-265">Obor názvů</span><span class="sxs-lookup"><span data-stu-id="f89db-265">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="f89db-266">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f89db-266">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="f89db-267">*Pages/MorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f89db-267">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="f89db-268">*Pages/MorePages/EvenMorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f89db-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f89db-269">`@page`Direktiva má jiné účinky v závislosti na typu souboru, ve kterém se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="f89db-269">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="f89db-270">Direktiva:</span><span class="sxs-lookup"><span data-stu-id="f89db-270">The directive:</span></span>

* <span data-ttu-id="f89db-271">V souboru *. cshtml* znamená, že soubor je Razor Stránka.</span><span class="sxs-lookup"><span data-stu-id="f89db-271">In a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="f89db-272">Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes) a <xref:razor-pages/index> .</span><span class="sxs-lookup"><span data-stu-id="f89db-272">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="f89db-273">Určuje, že Razor komponenta má zpracovávat požadavky přímo.</span><span class="sxs-lookup"><span data-stu-id="f89db-273">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="f89db-274">Další informace naleznete v tématu <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="f89db-274">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f89db-275">`@page`Direktiva na prvním řádku souboru *. cshtml* znamená, že soubor je Razor Stránka.</span><span class="sxs-lookup"><span data-stu-id="f89db-275">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="f89db-276">Další informace naleznete v tématu <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="f89db-276">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="f89db-277">*Tento scénář se vztahuje pouze na zobrazení a Razor stránky MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="f89db-277">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="f89db-278">`@section`Direktiva se používá společně s [ Razor rozloženími MVC a stránky](xref:mvc/views/layout) k tomu, aby zobrazení nebo stránky vygenerovaly obsah v různých částech stránky HTML.</span><span class="sxs-lookup"><span data-stu-id="f89db-278">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="f89db-279">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="f89db-279">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="f89db-280">`@using`Direktiva přidá `using` direktivu jazyka C# do generovaného zobrazení:</span><span class="sxs-lookup"><span data-stu-id="f89db-280">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f89db-281">V [ Razor součástech](xref:blazor/components/index) `@using` také určuje, které součásti jsou v oboru.</span><span class="sxs-lookup"><span data-stu-id="f89db-281">In [Razor components](xref:blazor/components/index), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="f89db-282">Atributy direktiv</span><span class="sxs-lookup"><span data-stu-id="f89db-282">Directive attributes</span></span>

<span data-ttu-id="f89db-283">Razor atributy direktiv jsou reprezentovány implicitními výrazy s vyhrazenými klíčovými slovy za `@` symbolem.</span><span class="sxs-lookup"><span data-stu-id="f89db-283">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="f89db-284">Atribut direktivy obvykle mění způsob, jakým je prvek analyzován, nebo umožňuje různé funkce.</span><span class="sxs-lookup"><span data-stu-id="f89db-284">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="f89db-285">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="f89db-285">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f89db-286">`@attributes` umožňuje komponentě vykreslovat nedeklarované atributy.</span><span class="sxs-lookup"><span data-stu-id="f89db-286">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="f89db-287">Další informace naleznete v tématu <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="f89db-287">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="f89db-288">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="f89db-288">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f89db-289">Datové vazby v součástech se provádí s `@bind` atributem.</span><span class="sxs-lookup"><span data-stu-id="f89db-289">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="f89db-290">Další informace naleznete v tématu <xref:blazor/components/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="f89db-290">For more information, see <xref:blazor/components/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="f89db-291">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="f89db-291">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f89db-292">Razor poskytuje funkce pro zpracování událostí pro součásti.</span><span class="sxs-lookup"><span data-stu-id="f89db-292">Razor provides event handling features for components.</span></span> <span data-ttu-id="f89db-293">Další informace naleznete v tématu <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="f89db-293">For more information, see <xref:blazor/components/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="f89db-294">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="f89db-294">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f89db-295">Zabrání výchozí akci pro událost.</span><span class="sxs-lookup"><span data-stu-id="f89db-295">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="f89db-296">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="f89db-296">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f89db-297">Zastaví šíření události pro událost.</span><span class="sxs-lookup"><span data-stu-id="f89db-297">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="f89db-298">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="f89db-298">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f89db-299">`@key`Atribut direktiva způsobí, že rozdílový algoritmus komponent garantuje zachování prvků nebo komponent na základě hodnoty klíče.</span><span class="sxs-lookup"><span data-stu-id="f89db-299">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="f89db-300">Další informace naleznete v tématu <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="f89db-300">For more information, see <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="f89db-301">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="f89db-301">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f89db-302">Odkazy na součásti ( `@ref` ) poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance.</span><span class="sxs-lookup"><span data-stu-id="f89db-302">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="f89db-303">Další informace naleznete v tématu <xref:blazor/components/index#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="f89db-303">For more information, see <xref:blazor/components/index#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="f89db-304">*Tento scénář se vztahuje pouze na Razor součásti (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="f89db-304">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f89db-305">`@typeparam`Direktiva deklaruje parametr obecného typu pro generovanou třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="f89db-305">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="f89db-306">Další informace naleznete v tématu <xref:blazor/components/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="f89db-306">For more information, see <xref:blazor/components/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a><span data-ttu-id="f89db-307">Zástupci přidaných šablon Razor</span><span class="sxs-lookup"><span data-stu-id="f89db-307">Templated Razor delegates</span></span>

<span data-ttu-id="f89db-308">Razor šablony umožňují definovat fragment uživatelského rozhraní v následujícím formátu:</span><span class="sxs-lookup"><span data-stu-id="f89db-308">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="f89db-309">Následující příklad ukazuje, jak zadat delegáta s šablonou Razor jako <xref:System.Func%602> .</span><span class="sxs-lookup"><span data-stu-id="f89db-309">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="f89db-310">[Dynamický typ](/dotnet/csharp/programming-guide/types/using-type-dynamic) je zadán pro parametr metody, kterou delegát zapouzdřuje.</span><span class="sxs-lookup"><span data-stu-id="f89db-310">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="f89db-311">[Typ objektu](/dotnet/csharp/language-reference/keywords/object) je zadán jako návratová hodnota delegáta.</span><span class="sxs-lookup"><span data-stu-id="f89db-311">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="f89db-312">Šablona se používá s <xref:System.Collections.Generic.List%601> `Pet` vlastností, která má `Name` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f89db-312">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="f89db-313">Šablona je vykreslena s `pets` dodaným `foreach` příkazem:</span><span class="sxs-lookup"><span data-stu-id="f89db-313">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="f89db-314">Vykreslený výstup:</span><span class="sxs-lookup"><span data-stu-id="f89db-314">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="f89db-315">Vloženou šablonu můžete také dodat Razor jako argument metodě.</span><span class="sxs-lookup"><span data-stu-id="f89db-315">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="f89db-316">V následujícím příkladu `Repeat` metoda obdrží Razor šablonu.</span><span class="sxs-lookup"><span data-stu-id="f89db-316">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="f89db-317">Metoda používá šablonu k vytvoření obsahu HTML s opakováním položek dodaných ze seznamu:</span><span class="sxs-lookup"><span data-stu-id="f89db-317">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="f89db-318">Pomocí seznamu domácích zvířat z předchozího příkladu `Repeat` je metoda volána s:</span><span class="sxs-lookup"><span data-stu-id="f89db-318">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="f89db-319"><xref:System.Collections.Generic.List%601> z `Pet` .</span><span class="sxs-lookup"><span data-stu-id="f89db-319"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="f89db-320">Počet opakování každé PET.</span><span class="sxs-lookup"><span data-stu-id="f89db-320">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="f89db-321">Vložená šablona, která se má použít pro položky seznamu neuspořádaného seznamu</span><span class="sxs-lookup"><span data-stu-id="f89db-321">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="f89db-322">Vykreslený výstup:</span><span class="sxs-lookup"><span data-stu-id="f89db-322">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="f89db-323">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="f89db-323">Tag Helpers</span></span>

<span data-ttu-id="f89db-324">*Tento scénář se vztahuje pouze na zobrazení a Razor stránky MVC (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="f89db-324">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="f89db-325">Existují tři direktivy, které se týkají [pomocníků značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="f89db-325">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="f89db-326">Směrnici</span><span class="sxs-lookup"><span data-stu-id="f89db-326">Directive</span></span> | <span data-ttu-id="f89db-327">Funkce</span><span class="sxs-lookup"><span data-stu-id="f89db-327">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="f89db-328">Zpřístupní pomocníkům značek zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f89db-328">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="f89db-329">Odebere pomocníky značek dříve přidané ze zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f89db-329">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="f89db-330">Určuje předponu značky pro povolení podpory značek pomocníka a k explicitnímu použití pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="f89db-330">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="no-locrazor-reserved-keywords"></a><span data-ttu-id="f89db-331">Razor vyhrazená klíčová slova</span><span class="sxs-lookup"><span data-stu-id="f89db-331">Razor reserved keywords</span></span>

### <a name="no-locrazor-keywords"></a><span data-ttu-id="f89db-332">Razor klíčov</span><span class="sxs-lookup"><span data-stu-id="f89db-332">Razor keywords</span></span>

* <span data-ttu-id="f89db-333">`page` (Vyžaduje ASP.NET Core 2,1 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="f89db-333">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="f89db-334">`helper` (Aktuálně není podporováno ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="f89db-334">`helper` (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="f89db-335">Razor Klíčová slova jsou uvozena znakem `@(Razor Keyword)` (například `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="f89db-335">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-no-locrazor-keywords"></a><span data-ttu-id="f89db-336">RazorKlíčová slova jazyka C#</span><span class="sxs-lookup"><span data-stu-id="f89db-336">C# Razor keywords</span></span>

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

<span data-ttu-id="f89db-337">RazorKlíčová slova jazyka C# musí mít dvojité uvození řídicím znakem `@(@C# Razor Keyword)` (například `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="f89db-337">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="f89db-338">První `@` řídí Razor analyzátor.</span><span class="sxs-lookup"><span data-stu-id="f89db-338">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="f89db-339">Druhý `@` řídí analyzátor jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="f89db-339">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-no-locrazor"></a><span data-ttu-id="f89db-340">Vyhrazená klíčová slova, která nepoužívá Razor</span><span class="sxs-lookup"><span data-stu-id="f89db-340">Reserved keywords not used by Razor</span></span>

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a><span data-ttu-id="f89db-341">Kontrola Razor třídy jazyka C# vygenerované pro zobrazení</span><span class="sxs-lookup"><span data-stu-id="f89db-341">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f89db-342">Pomocí .NET Core SDK 2,1 nebo novější [ Razor sada SDK](xref:razor-pages/sdk) zpracovává kompilaci Razor souborů.</span><span class="sxs-lookup"><span data-stu-id="f89db-342">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="f89db-343">Při sestavování projektu Razor generuje sada SDK objekt \*obj/<build_configuration>/<target_framework_moniker>/ Razor \* adresář v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="f89db-343">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="f89db-344">Adresářová struktura v *Razor* adresáři zrcadlí strukturu adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="f89db-344">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="f89db-345">Vezměte v úvahu následující adresářovou strukturu v projektu stránky ASP.NET Core 2,1 s Razor cílením na rozhraní .NET Core 2,1:</span><span class="sxs-lookup"><span data-stu-id="f89db-345">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

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

<span data-ttu-id="f89db-346">Sestavení projektu v konfiguraci *ladění* má za následek následující adresář *obj* :</span><span class="sxs-lookup"><span data-stu-id="f89db-346">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

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

<span data-ttu-id="f89db-347">Chcete-li zobrazit vygenerovanou třídu pro *pages/index. cshtml*, otevřete *obj/ladění/netcoreapp 2.1/ Razor /pages/index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="f89db-347">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="f89db-348">Přidejte následující třídu do projektu ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="f89db-348">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="f89db-349">V aplikaci `Startup.ConfigureServices` přepište `RazorTemplateEngine` přidanou `CustomTemplateEngine` třídou MVC pomocí třídy:</span><span class="sxs-lookup"><span data-stu-id="f89db-349">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="f89db-350">Nastavte zarážku na `return csharpDocument;` příkazu `CustomTemplateEngine` .</span><span class="sxs-lookup"><span data-stu-id="f89db-350">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="f89db-351">Když se provádění programu zastaví na zarážce, zobrazte hodnotu `generatedCode` .</span><span class="sxs-lookup"><span data-stu-id="f89db-351">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Zobrazení Vizualizér textu pro generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="f89db-353">Zobrazit hledání a rozlišovat velká a malá písmena</span><span class="sxs-lookup"><span data-stu-id="f89db-353">View lookups and case sensitivity</span></span>

<span data-ttu-id="f89db-354">RazorModul zobrazení provádí pro zobrazení vyhledávání citlivá na velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="f89db-354">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="f89db-355">Skutečné vyhledávání je však určeno základním systémem souborů:</span><span class="sxs-lookup"><span data-stu-id="f89db-355">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="f89db-356">Zdroj založený na souborech:</span><span class="sxs-lookup"><span data-stu-id="f89db-356">File based source:</span></span>
  * <span data-ttu-id="f89db-357">V operačních systémech se systémy souborů bez rozlišení velkých a malých písmen (například Windows) se u hledání fyzického zprostředkovatele souborů nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="f89db-357">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="f89db-358">Výsledkem je například `return View("Test")` shoda pro */views/Home/test.cshtml*, */views/Home/test.cshtml*a jakékoliv jiné varianty velikosti písmen.</span><span class="sxs-lookup"><span data-stu-id="f89db-358">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="f89db-359">V systémech souborů s rozlišováním velkých a malých písmen (například Linux, OSX a with `EmbeddedFileProvider` ) se při hledání rozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="f89db-359">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="f89db-360">`return View("Test")`Konkrétně například odpovídá */views/Home/test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f89db-360">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="f89db-361">Předkompilovaná zobrazení: u ASP.NET Core 2,0 a novějších, hledání předkompilovaných zobrazení rozlišuje velká a malá písmena ve všech operačních systémech.</span><span class="sxs-lookup"><span data-stu-id="f89db-361">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="f89db-362">Chování je stejné jako chování poskytovatele fyzického souboru ve Windows.</span><span class="sxs-lookup"><span data-stu-id="f89db-362">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="f89db-363">Pokud se dvě Předkompilovaná zobrazení liší pouze v případě, výsledek hledání není deterministický.</span><span class="sxs-lookup"><span data-stu-id="f89db-363">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="f89db-364">Vývojářům doporučujeme, aby se shodovaly s velkými písmeny názvů souborů a adresářů na velká a malá písmena:</span><span class="sxs-lookup"><span data-stu-id="f89db-364">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="f89db-365">Názvy oblastí, kontrolérů a akcí.</span><span class="sxs-lookup"><span data-stu-id="f89db-365">Area, controller, and action names.</span></span>
* <span data-ttu-id="f89db-366">Razor Stránky.</span><span class="sxs-lookup"><span data-stu-id="f89db-366">Razor Pages.</span></span>

<span data-ttu-id="f89db-367">V případě, že se rozlišuje velká a malá písmena, zajistíte jejich zobrazení bez ohledu na základní systém souborů.</span><span class="sxs-lookup"><span data-stu-id="f89db-367">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f89db-368">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="f89db-368">Additional resources</span></span>

<span data-ttu-id="f89db-369">[Seznámení s ASP.NET webovým programováním pomocí Razor Syntaxe](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) poskytuje mnoho ukázek programování s Razor syntaxí.</span><span class="sxs-lookup"><span data-stu-id="f89db-369">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
