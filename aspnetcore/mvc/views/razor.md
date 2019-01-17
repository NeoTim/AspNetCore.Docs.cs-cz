---
title: Referenční příručka syntaxe Razor pro ASP.NET Core
author: rick-anderson
description: Další informace o syntaxi Razor kód pro vložení do webových stránek kód založený na serveru.
ms.author: riande
ms.date: 10/26/2018
uid: mvc/views/razor
ms.openlocfilehash: 2ec86c774e0fd26c4455829680a2b1db687b8090
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "53121581"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="79a13-103">Referenční příručka syntaxe Razor pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="79a13-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="79a13-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylora MÜLLENA](https://twitter.com/ntaylormullen), a [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="79a13-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="79a13-105">Razor je syntaxe značek pro vkládání do webových stránek kód založený na serveru.</span><span class="sxs-lookup"><span data-stu-id="79a13-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="79a13-106">Syntaxe Razor se skládá z kódu Razor C#a HTML.</span><span class="sxs-lookup"><span data-stu-id="79a13-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="79a13-107">Máte soubory, které obvykle obsahují Razor *.cshtml* příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="79a13-107">Files containing Razor generally have a *.cshtml* file extension.</span></span>

## <a name="rendering-html"></a><span data-ttu-id="79a13-108">Vykreslování protokolu HTML</span><span class="sxs-lookup"><span data-stu-id="79a13-108">Rendering HTML</span></span>

<span data-ttu-id="79a13-109">Výchozí jazyk Razor je ve formátu HTML.</span><span class="sxs-lookup"><span data-stu-id="79a13-109">The default Razor language is HTML.</span></span> <span data-ttu-id="79a13-110">Vykreslování HTML z kód Razor se nijak neliší od vykreslování protokolu HTML ze souboru HTML.</span><span class="sxs-lookup"><span data-stu-id="79a13-110">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="79a13-111">Značka jazyka HTML v *.cshtml* souborech Razor je vykreslen metodou serveru beze změny.</span><span class="sxs-lookup"><span data-stu-id="79a13-111">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="79a13-112">Syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="79a13-112">Razor syntax</span></span>

<span data-ttu-id="79a13-113">Podporuje Razor C# a používá `@` symbol, který má přechod z HTML na C#.</span><span class="sxs-lookup"><span data-stu-id="79a13-113">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="79a13-114">Vyhodnotí Razor C# výrazy a vykreslí je ve výstupu protokolu HTML.</span><span class="sxs-lookup"><span data-stu-id="79a13-114">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="79a13-115">Když `@` následuje symbol [Razor rezervované klíčové slovo](#razor-reserved-keywords), přechází do kódu specifické pro syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="79a13-115">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="79a13-116">V opačném případě bude přecházet do prostého C#.</span><span class="sxs-lookup"><span data-stu-id="79a13-116">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="79a13-117">Řídicí `@` symbol v kódu Razor, použijte druhý `@` symbolů:</span><span class="sxs-lookup"><span data-stu-id="79a13-117">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="79a13-118">Kód se vykreslí v HTML pomocí jediného `@` symbolů:</span><span class="sxs-lookup"><span data-stu-id="79a13-118">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="79a13-119">Atributy HTML a obsah, který obsahuje e-mailové adresy nejsou s pracovat `@` symbol jako znak přechod.</span><span class="sxs-lookup"><span data-stu-id="79a13-119">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="79a13-120">E-mailové adresy v následujícím příkladu jsou zůstanou podle analýzy Razor:</span><span class="sxs-lookup"><span data-stu-id="79a13-120">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="79a13-121">Implicitní výrazy Razor</span><span class="sxs-lookup"><span data-stu-id="79a13-121">Implicit Razor expressions</span></span>

<span data-ttu-id="79a13-122">Implicitní Razor výrazy začínat `@` následovaný C# kódu:</span><span class="sxs-lookup"><span data-stu-id="79a13-122">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="79a13-123">S výjimkou produktů C# `await` – klíčové slovo, implicitní výrazů nesmí obsahovat mezery.</span><span class="sxs-lookup"><span data-stu-id="79a13-123">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="79a13-124">Pokud C# příkaz má vymazat koncové, může být intermingled mezery:</span><span class="sxs-lookup"><span data-stu-id="79a13-124">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="79a13-125">Implicitní výrazy **nelze** obsahovat C# obecných typů, jako znaky uvnitř hranatých závorek (`<>`) jsou interpretovány jako značku jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="79a13-125">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="79a13-126">Následující kód je **není** platné:</span><span class="sxs-lookup"><span data-stu-id="79a13-126">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="79a13-127">Předcházející kód vygeneruje chybu kompilátoru podobně jako na jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="79a13-127">The preceding code generates a compiler error similar to one of the following:</span></span>

 * <span data-ttu-id="79a13-128">Prvek "int" není uzavřený.</span><span class="sxs-lookup"><span data-stu-id="79a13-128">The "int" element wasn't closed.</span></span> <span data-ttu-id="79a13-129">Všechny elementy musí být buď samouzavírací nebo koncová značka.</span><span class="sxs-lookup"><span data-stu-id="79a13-129">All elements must be either self-closing or have a matching end tag.</span></span>
 *  <span data-ttu-id="79a13-130">Nelze převést skupinu metod 'GenericMethod' na nedelegující typ 'object'.</span><span class="sxs-lookup"><span data-stu-id="79a13-130">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="79a13-131">Chtěli jste vyvolat metodu? "</span><span class="sxs-lookup"><span data-stu-id="79a13-131">Did you intend to invoke the method?\`</span></span> 
 
<span data-ttu-id="79a13-132">Volání obecné metody musí být uzavřen do [explicitní výraz Razor](#explicit-razor-expressions) nebo [blok kódu Razor](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="79a13-132">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="79a13-133">Explicitní výrazy Razor</span><span class="sxs-lookup"><span data-stu-id="79a13-133">Explicit Razor expressions</span></span>

<span data-ttu-id="79a13-134">Explicitní syntaxe Razor výrazů se skládají z `@` symbol vyvážené závorky.</span><span class="sxs-lookup"><span data-stu-id="79a13-134">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="79a13-135">Pokud chcete zobrazit čas poslední týden, se používá následující kód Razor:</span><span class="sxs-lookup"><span data-stu-id="79a13-135">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="79a13-136">Veškerý obsah v rámci `@()` závorky se vyhodnotí a vykreslí do výstupu.</span><span class="sxs-lookup"><span data-stu-id="79a13-136">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="79a13-137">Obecně implicitní výrazů, je popsáno v předchozí části, nesmí obsahovat mezery.</span><span class="sxs-lookup"><span data-stu-id="79a13-137">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="79a13-138">V následujícím kódu není jeden týden odečtena od aktuální čas:</span><span class="sxs-lookup"><span data-stu-id="79a13-138">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="79a13-139">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="79a13-139">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="79a13-140">Explicitní výrazy můžete použít ke zřetězení text s výsledek výrazu:</span><span class="sxs-lookup"><span data-stu-id="79a13-140">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="79a13-141">Bez explicitní výraz `<p>Age@joe.Age</p>` je považován za e-mailovou adresu a `<p>Age@joe.Age</p>` vykreslením.</span><span class="sxs-lookup"><span data-stu-id="79a13-141">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="79a13-142">Při zápisu jako explicitní výraz `<p>Age33</p>` vykreslením.</span><span class="sxs-lookup"><span data-stu-id="79a13-142">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="79a13-143">Explicitní výrazy můžete použít k vykreslení výstup z obecných metod v *.cshtml* soubory.</span><span class="sxs-lookup"><span data-stu-id="79a13-143">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="79a13-144">Následující kód ukazuje, jak chcete-li zobrazit tato chyba dříve způsobené závorkami C# obecný.</span><span class="sxs-lookup"><span data-stu-id="79a13-144">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="79a13-145">Kód je zapsán jako explicitní výraz:</span><span class="sxs-lookup"><span data-stu-id="79a13-145">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="79a13-146">Výraz kódování</span><span class="sxs-lookup"><span data-stu-id="79a13-146">Expression encoding</span></span>

<span data-ttu-id="79a13-147">C#jsou výrazy, které vedou na řetězec kódovaný jazykem HTML.</span><span class="sxs-lookup"><span data-stu-id="79a13-147">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="79a13-148">C#výrazy, které vedou k `IHtmlContent` jsou generovány přímo pomocí `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="79a13-148">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="79a13-149">C#výrazy, které není vyhodnocen na `IHtmlContent` jsou převedeny na řetězec pomocí `ToString` a kódováním než se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="79a13-149">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="79a13-150">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="79a13-150">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="79a13-151">Kód HTML je zobrazená v prohlížeči jako:</span><span class="sxs-lookup"><span data-stu-id="79a13-151">The HTML is shown in the browser as:</span></span>

```
<span>Hello World</span>
```

<span data-ttu-id="79a13-152">`HtmlHelper.Raw` výstup není kódovaný ale se vykresluje jako značka jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="79a13-152">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="79a13-153">Pomocí `HtmlHelper.Raw` unsanitized uživatele vstup představuje bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="79a13-153">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="79a13-154">Uživatelský vstup může obsahovat další zneužití nebo škodlivý jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="79a13-154">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="79a13-155">Sanitaci uživatelský vstup je obtížné.</span><span class="sxs-lookup"><span data-stu-id="79a13-155">Sanitizing user input is difficult.</span></span> <span data-ttu-id="79a13-156">Vyhněte se použití `HtmlHelper.Raw` s uživatelským vstupem.</span><span class="sxs-lookup"><span data-stu-id="79a13-156">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="79a13-157">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="79a13-157">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="79a13-158">Bloky kódu Razor</span><span class="sxs-lookup"><span data-stu-id="79a13-158">Razor code blocks</span></span>

<span data-ttu-id="79a13-159">Bloky kódu Razor začínat `@` a jsou uzavřeny ve `{}`.</span><span class="sxs-lookup"><span data-stu-id="79a13-159">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="79a13-160">Na rozdíl od výrazy C# kód uvnitř bloků kódu není vykresleno.</span><span class="sxs-lookup"><span data-stu-id="79a13-160">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="79a13-161">Bloky kódu a výrazy v zobrazení sdílejí stejný obor a jsou definované v pořadí:</span><span class="sxs-lookup"><span data-stu-id="79a13-161">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="79a13-162">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="79a13-162">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

### <a name="implicit-transitions"></a><span data-ttu-id="79a13-163">Implicitní přechody</span><span class="sxs-lookup"><span data-stu-id="79a13-163">Implicit transitions</span></span>

<span data-ttu-id="79a13-164">Je výchozí jazyk v bloku kódu C#, ale stránka Razor můžete přejít zpět na HTML:</span><span class="sxs-lookup"><span data-stu-id="79a13-164">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="79a13-165">Explicitní převod s oddělovači</span><span class="sxs-lookup"><span data-stu-id="79a13-165">Explicit delimited transition</span></span>

<span data-ttu-id="79a13-166">Pokud chcete definovat dílčí část objektu bloku kódu, který vykreslovat kód HTML, obklopit fragmentem syntaxi Razor znaků pro vykreslování  **\<text >** značky:</span><span class="sxs-lookup"><span data-stu-id="79a13-166">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor **\<text>** tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="79a13-167">Tuto metodu použijte k vykreslení HTML, který není obklopený značky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="79a13-167">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="79a13-168">Bez značky jazyka HTML nebo Razor dojde k chybě modulu runtime Razor.</span><span class="sxs-lookup"><span data-stu-id="79a13-168">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="79a13-169">**\<Text >** značka je vhodné pro řízení prázdné znaky, při vykreslování obsahu:</span><span class="sxs-lookup"><span data-stu-id="79a13-169">The **\<text>** tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="79a13-170">Pouze obsah mezi  **\<text >** je vykreslen.</span><span class="sxs-lookup"><span data-stu-id="79a13-170">Only the content between the **\<text>** tag is rendered.</span></span> 
* <span data-ttu-id="79a13-171">Žádné prázdné znaky před nebo po  **\<text >** značky se zobrazí ve výstupu protokolu HTML.</span><span class="sxs-lookup"><span data-stu-id="79a13-171">No whitespace before or after the **\<text>** tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition-with-"></a><span data-ttu-id="79a13-172">Explicitní řádek přechodu se @:</span><span class="sxs-lookup"><span data-stu-id="79a13-172">Explicit Line Transition with @:</span></span>

<span data-ttu-id="79a13-173">K vykreslení rest celý řádek jako kód HTML uvnitř bloku kódu, použijte `@:` syntaxi:</span><span class="sxs-lookup"><span data-stu-id="79a13-173">To render the rest of an entire line as HTML inside a code block, use the `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="79a13-174">Bez `@:` v kódu, je vygenerována chyba za běhu Razor.</span><span class="sxs-lookup"><span data-stu-id="79a13-174">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="79a13-175">Upozornění: Navíc `@` znaky v souboru Razor mohou způsobit chyby kompilátoru příkazy v bloku.</span><span class="sxs-lookup"><span data-stu-id="79a13-175">Warning: Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="79a13-176">Tyto chyby kompilátoru může být obtížné pochopit, protože Skutečná chyba předchází oznámenou chybu.</span><span class="sxs-lookup"><span data-stu-id="79a13-176">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="79a13-177">K této chybě dochází po kombinování více implicitního/explicitního výrazů v jednom bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="79a13-177">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="79a13-178">Řídicí struktury</span><span class="sxs-lookup"><span data-stu-id="79a13-178">Control structures</span></span>

<span data-ttu-id="79a13-179">Řídicí struktury jsou rozšíření bloky kódu.</span><span class="sxs-lookup"><span data-stu-id="79a13-179">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="79a13-180">Všechny aspekty bloky kódu (Přechod na značky vložené C#) platí také pro následující struktury:</span><span class="sxs-lookup"><span data-stu-id="79a13-180">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="79a13-181">Podmíněné výrazy @if, else if, else, a @switch</span><span class="sxs-lookup"><span data-stu-id="79a13-181">Conditionals @if, else if, else, and @switch</span></span>

<span data-ttu-id="79a13-182">`@if` ovládací prvky, pokud kód se spustí:</span><span class="sxs-lookup"><span data-stu-id="79a13-182">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="79a13-183">`else` a `else if` nevyžadují `@` symbolů:</span><span class="sxs-lookup"><span data-stu-id="79a13-183">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="79a13-184">Následující kód ukazuje použití příkazu switch:</span><span class="sxs-lookup"><span data-stu-id="79a13-184">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="79a13-185">Opakování ve smyčce @for, @foreach, @while, a @do při</span><span class="sxs-lookup"><span data-stu-id="79a13-185">Looping @for, @foreach, @while, and @do while</span></span>

<span data-ttu-id="79a13-186">Bez vizuálního vzhledu HTML lze vykreslit s opakování ve smyčce řídicí příkazy.</span><span class="sxs-lookup"><span data-stu-id="79a13-186">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="79a13-187">Pokud chcete zobrazit seznam lidí:</span><span class="sxs-lookup"><span data-stu-id="79a13-187">To render a list of people:</span></span>

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

<span data-ttu-id="79a13-188">Podporují se následující příkazy opakování:</span><span class="sxs-lookup"><span data-stu-id="79a13-188">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="79a13-189">Složené @using</span><span class="sxs-lookup"><span data-stu-id="79a13-189">Compound @using</span></span>

<span data-ttu-id="79a13-190">V C#, `using` prohlášení se používá k zajištění uvolněn objekt.</span><span class="sxs-lookup"><span data-stu-id="79a13-190">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="79a13-191">V prostředí Razor stejný mechanismus slouží k vytvoření pomocné rutiny HTML, které obsahují další obsah.</span><span class="sxs-lookup"><span data-stu-id="79a13-191">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="79a13-192">V následujícím kódu pomocných rutin HTML, vykreslení značky formuláře s `@using` – příkaz:</span><span class="sxs-lookup"><span data-stu-id="79a13-192">In the following code, HTML Helpers render a form tag with the `@using` statement:</span></span>


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

<span data-ttu-id="79a13-193">Akce na úrovni oboru, můžete to provést pomocí [pomocných rutin značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="79a13-193">Scope-level actions can be performed with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

### <a name="try-catch-finally"></a><span data-ttu-id="79a13-194">@try, catch, finally</span><span class="sxs-lookup"><span data-stu-id="79a13-194">@try, catch, finally</span></span>

<span data-ttu-id="79a13-195">Zpracování výjimek je podobný C#:</span><span class="sxs-lookup"><span data-stu-id="79a13-195">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>@lock

<span data-ttu-id="79a13-196">Razor má schopnost chránit kritické oddíly s příkazy uzamčení:</span><span class="sxs-lookup"><span data-stu-id="79a13-196">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="79a13-197">Komentáře</span><span class="sxs-lookup"><span data-stu-id="79a13-197">Comments</span></span>

<span data-ttu-id="79a13-198">Podporuje Razor C# a komentáře HTML:</span><span class="sxs-lookup"><span data-stu-id="79a13-198">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="79a13-199">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="79a13-199">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="79a13-200">Komentáře syntaxe Razor jsou odebrána serverem, než se zobrazí webová stránka.</span><span class="sxs-lookup"><span data-stu-id="79a13-200">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="79a13-201">Používá syntaxi Razor `@*  *@` pro vymezení komentáře.</span><span class="sxs-lookup"><span data-stu-id="79a13-201">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="79a13-202">Následující kód je zakomentovaný, tak server nevykreslí žádné značky:</span><span class="sxs-lookup"><span data-stu-id="79a13-202">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="79a13-203">Direktivy</span><span class="sxs-lookup"><span data-stu-id="79a13-203">Directives</span></span>

<span data-ttu-id="79a13-204">Direktivy Razor jsou reprezentovány implicitní výrazy s následující vyhrazená klíčová slova `@` symbol.</span><span class="sxs-lookup"><span data-stu-id="79a13-204">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="79a13-205">Direktivu obvykle mění způsob zobrazení je analyzován nebo jinou funkci povolí.</span><span class="sxs-lookup"><span data-stu-id="79a13-205">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="79a13-206">Vysvětlení způsobu, jakým Razor generuje kód pro zobrazení usnadňuje pochopení fungování direktivy.</span><span class="sxs-lookup"><span data-stu-id="79a13-206">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="79a13-207">Kód vygeneruje třídu podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="79a13-207">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="79a13-208">Dále v tomto článku v části [Zkontrolujte syntaxi Razor C# třída vygenerovaná pro zobrazení](#inspect-the-razor-c-class-generated-for-a-view) vysvětluje, jak zobrazit tento generované třídy.</span><span class="sxs-lookup"><span data-stu-id="79a13-208">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

<a name="using"></a>
### <a name="using"></a>@using

<span data-ttu-id="79a13-209">`@using` Direktivy přidává C# `using` direktiv generované zobrazení:</span><span class="sxs-lookup"><span data-stu-id="79a13-209">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

### <a name="model"></a>@model

<span data-ttu-id="79a13-210">`@model` Direktiva Určuje typ modelu předána do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="79a13-210">The `@model` directive specifies the type of the model passed to a view:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="79a13-211">V aplikaci ASP.NET Core MVC vytvořené pomocí jednotlivých uživatelských účtů *Views/Account/Login.cshtml* zobrazení obsahuje následující deklaraci modelu:</span><span class="sxs-lookup"><span data-stu-id="79a13-211">In an ASP.NET Core MVC app created with individual user accounts, the *Views/Account/Login.cshtml* view contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="79a13-212">Dědí třídu vygenerovanou z `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="79a13-212">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="79a13-213">Zpřístupňuje Razor `Model` předána do zobrazení, vlastnosti pro přístup k modelu:</span><span class="sxs-lookup"><span data-stu-id="79a13-213">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="79a13-214">`@model` Direktiva Určuje typ této vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="79a13-214">The `@model` directive specifies the type of this property.</span></span> <span data-ttu-id="79a13-215">Direktiva Určuje, `T` v `RazorPage<T>` , že generované třídy, která zobrazení je odvozena z.</span><span class="sxs-lookup"><span data-stu-id="79a13-215">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="79a13-216">Pokud `@model` – direktiva není zadán, `Model` vlastnost je typu `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="79a13-216">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="79a13-217">Z kontroleru zobrazení je předána hodnota modelu.</span><span class="sxs-lookup"><span data-stu-id="79a13-217">The value of the model is passed from the controller to the view.</span></span> <span data-ttu-id="79a13-218">Další informace najdete v tématu [se silnými typy modelů a &commat;– klíčové slovo modelu](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="79a13-218">For more information, see [Strongly typed models and the &commat;model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="inherits"></a>@inherits

<span data-ttu-id="79a13-219">`@inherits` – Direktiva poskytuje plnou kontrolu nad třída dědí zobrazení:</span><span class="sxs-lookup"><span data-stu-id="79a13-219">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="79a13-220">Následující kód je vlastní typ stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="79a13-220">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="79a13-221">`CustomText` Se zobrazí v zobrazení:</span><span class="sxs-lookup"><span data-stu-id="79a13-221">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="79a13-222">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="79a13-222">The code renders the following HTML:</span></span>

```html
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

 <span data-ttu-id="79a13-223">`@model` a `@inherits` je možné ve stejném zobrazení.</span><span class="sxs-lookup"><span data-stu-id="79a13-223">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="79a13-224">`@inherits` může být v *_ViewImports.cshtml* soubor, který importuje zobrazení:</span><span class="sxs-lookup"><span data-stu-id="79a13-224">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="79a13-225">Následující kód je příkladem zobrazení se silnými typy:</span><span class="sxs-lookup"><span data-stu-id="79a13-225">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="79a13-226">Pokud "rick@contoso.com" je předán zobrazení v modelu, generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="79a13-226">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

### <a name="inject"></a>@inject

<span data-ttu-id="79a13-227">`@inject` Direktiva umožňuje vložit služby z stránky Razor [kontejneru služby](xref:fundamentals/dependency-injection) do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="79a13-227">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="79a13-228">Další informace najdete v tématu [injektáž závislostí do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="79a13-228">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

### <a name="functions"></a>@functions

<span data-ttu-id="79a13-229">`@functions` Direktiva umožňuje stránky Razor a přidat C# blok kódu do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="79a13-229">The `@functions` directive enables a Razor Page to add a C# code block to a view:</span></span>

```cshtml
@functions { // C# Code }
```

<span data-ttu-id="79a13-230">Příklad:</span><span class="sxs-lookup"><span data-stu-id="79a13-230">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="79a13-231">Kód generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="79a13-231">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="79a13-232">Následující kód je vygenerovaný Razor C# třídy:</span><span class="sxs-lookup"><span data-stu-id="79a13-232">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### <a name="section"></a>@section

<span data-ttu-id="79a13-233">`@section` – Direktiva se používá ve spojení s [rozložení](xref:mvc/views/layout) povolit zobrazení k vykreslení obsahu v různých částech stránky HTML.</span><span class="sxs-lookup"><span data-stu-id="79a13-233">The `@section` directive is used in conjunction with the [layout](xref:mvc/views/layout) to enable views to render content in different parts of the HTML page.</span></span> <span data-ttu-id="79a13-234">Další informace najdete v tématu [oddíly](xref:mvc/views/layout#layout-sections-label).</span><span class="sxs-lookup"><span data-stu-id="79a13-234">For more information, see [Sections](xref:mvc/views/layout#layout-sections-label).</span></span>

## <a name="templated-razor-delegates"></a><span data-ttu-id="79a13-235">Šablony Razor delegátů</span><span class="sxs-lookup"><span data-stu-id="79a13-235">Templated Razor delegates</span></span>

<span data-ttu-id="79a13-236">Šablony Razor umožňují definovat fragment uživatelského rozhraní v následujícím formátu:</span><span class="sxs-lookup"><span data-stu-id="79a13-236">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="79a13-237">Následující příklad ukazuje, jak zadat bez vizuálního vzhledu Razor delegáta jako <xref:System.Func`2>.</span><span class="sxs-lookup"><span data-stu-id="79a13-237">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func`2>.</span></span> <span data-ttu-id="79a13-238">[Dynamického typu](/dotnet/csharp/programming-guide/types/using-type-dynamic) je zadaná pro parametr metody, která zapouzdřuje delegáta.</span><span class="sxs-lookup"><span data-stu-id="79a13-238">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="79a13-239">[Typ objektu](/dotnet/csharp/language-reference/keywords/object) je zadán jako návratový typ delegáta.</span><span class="sxs-lookup"><span data-stu-id="79a13-239">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="79a13-240">Šablona se používá s <xref:System.Collections.Generic.List`1> z `Pet` , který má `Name` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="79a13-240">The template is used with a <xref:System.Collections.Generic.List`1> of `Pet` that has a `Name` property.</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named @item.Name.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

<span data-ttu-id="79a13-241">Šablona je vykreslen pomocí `pets` poskytnutých `foreach` – příkaz:</span><span class="sxs-lookup"><span data-stu-id="79a13-241">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate2(pet)
}
```

<span data-ttu-id="79a13-242">Vykresleného výstupu:</span><span class="sxs-lookup"><span data-stu-id="79a13-242">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="79a13-243">Šablony Razor vložené lze také zadat jako argument pro metodu.</span><span class="sxs-lookup"><span data-stu-id="79a13-243">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="79a13-244">V následujícím příkladu `Repeat` metoda obdrží šablona Razor.</span><span class="sxs-lookup"><span data-stu-id="79a13-244">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="79a13-245">Metoda používá šablony k vytvoření HTML obsah s opakování zadaný ze seznamu položek:</span><span class="sxs-lookup"><span data-stu-id="79a13-245">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="79a13-246">Pomocí seznam mazlíčků z předchozího příkladu `Repeat` metoda je volána pomocí:</span><span class="sxs-lookup"><span data-stu-id="79a13-246">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="79a13-247"><xref:System.Collections.Generic.List`1> z `Pet`.</span><span class="sxs-lookup"><span data-stu-id="79a13-247"><xref:System.Collections.Generic.List`1> of `Pet`.</span></span>
* <span data-ttu-id="79a13-248">Počet opakování každou mazlíčků.</span><span class="sxs-lookup"><span data-stu-id="79a13-248">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="79a13-249">Vložená šablona použitá pro položky seznamu neuspořádaný seznam.</span><span class="sxs-lookup"><span data-stu-id="79a13-249">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="79a13-250">Vykresleného výstupu:</span><span class="sxs-lookup"><span data-stu-id="79a13-250">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="79a13-251">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="79a13-251">Tag Helpers</span></span>

<span data-ttu-id="79a13-252">Existují tři direktivy, které se týkají [pomocných rutin značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="79a13-252">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="79a13-253">– Direktiva</span><span class="sxs-lookup"><span data-stu-id="79a13-253">Directive</span></span> | <span data-ttu-id="79a13-254">Funkce</span><span class="sxs-lookup"><span data-stu-id="79a13-254">Function</span></span> |
| --------- | -------- |
| [<span data-ttu-id="79a13-255">&commat;addTagHelper</span><span class="sxs-lookup"><span data-stu-id="79a13-255">&commat;addTagHelper</span></span>](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="79a13-256">Zpřístupní pomocných rutin značek k zobrazení.</span><span class="sxs-lookup"><span data-stu-id="79a13-256">Makes Tag Helpers available to a view.</span></span> |
| [<span data-ttu-id="79a13-257">&commat;removeTagHelper</span><span class="sxs-lookup"><span data-stu-id="79a13-257">&commat;removeTagHelper</span></span>](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="79a13-258">Odebere ze zobrazení přidali dříve pomocných rutin značek.</span><span class="sxs-lookup"><span data-stu-id="79a13-258">Removes Tag Helpers previously added from a view.</span></span> |
| [<span data-ttu-id="79a13-259">&commat;tagHelperPrefix</span><span class="sxs-lookup"><span data-stu-id="79a13-259">&commat;tagHelperPrefix</span></span>](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="79a13-260">Určuje předponu značky k povolení podpory pomocné rutiny značky a aby explicitní použití pomocné rutiny značky.</span><span class="sxs-lookup"><span data-stu-id="79a13-260">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="79a13-261">Razor vyhrazená klíčová slova</span><span class="sxs-lookup"><span data-stu-id="79a13-261">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="79a13-262">Klíčová slova Razor</span><span class="sxs-lookup"><span data-stu-id="79a13-262">Razor keywords</span></span>

* <span data-ttu-id="79a13-263">stránka (vyžaduje ASP.NET Core 2.0 a novější)</span><span class="sxs-lookup"><span data-stu-id="79a13-263">page (Requires ASP.NET Core 2.0 and later)</span></span>
* <span data-ttu-id="79a13-264">– obor názvů</span><span class="sxs-lookup"><span data-stu-id="79a13-264">namespace</span></span>
* <span data-ttu-id="79a13-265">– funkce</span><span class="sxs-lookup"><span data-stu-id="79a13-265">functions</span></span>
* <span data-ttu-id="79a13-266">Dědí</span><span class="sxs-lookup"><span data-stu-id="79a13-266">inherits</span></span>
* <span data-ttu-id="79a13-267">model</span><span class="sxs-lookup"><span data-stu-id="79a13-267">model</span></span>
* <span data-ttu-id="79a13-268">section</span><span class="sxs-lookup"><span data-stu-id="79a13-268">section</span></span>
* <span data-ttu-id="79a13-269">pomocné rutiny (aktuálně se nepodporuje ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="79a13-269">helper (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="79a13-270">Klíčová slova Razor jsou uvozeny řídicími znaky s `@(Razor Keyword)` (například `@(functions)`).</span><span class="sxs-lookup"><span data-stu-id="79a13-270">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="79a13-271">C#Klíčová slova Razor</span><span class="sxs-lookup"><span data-stu-id="79a13-271">C# Razor keywords</span></span>

* <span data-ttu-id="79a13-272">case</span><span class="sxs-lookup"><span data-stu-id="79a13-272">case</span></span>
* <span data-ttu-id="79a13-273">do</span><span class="sxs-lookup"><span data-stu-id="79a13-273">do</span></span>
* <span data-ttu-id="79a13-274">default</span><span class="sxs-lookup"><span data-stu-id="79a13-274">default</span></span>
* <span data-ttu-id="79a13-275">pro</span><span class="sxs-lookup"><span data-stu-id="79a13-275">for</span></span>
* <span data-ttu-id="79a13-276">foreach</span><span class="sxs-lookup"><span data-stu-id="79a13-276">foreach</span></span>
* <span data-ttu-id="79a13-277">if</span><span class="sxs-lookup"><span data-stu-id="79a13-277">if</span></span>
* <span data-ttu-id="79a13-278">else</span><span class="sxs-lookup"><span data-stu-id="79a13-278">else</span></span>
* <span data-ttu-id="79a13-279">lock</span><span class="sxs-lookup"><span data-stu-id="79a13-279">lock</span></span>
* <span data-ttu-id="79a13-280">– přepínač</span><span class="sxs-lookup"><span data-stu-id="79a13-280">switch</span></span>
* <span data-ttu-id="79a13-281">Zkuste</span><span class="sxs-lookup"><span data-stu-id="79a13-281">try</span></span>
* <span data-ttu-id="79a13-282">catch</span><span class="sxs-lookup"><span data-stu-id="79a13-282">catch</span></span>
* <span data-ttu-id="79a13-283">finally</span><span class="sxs-lookup"><span data-stu-id="79a13-283">finally</span></span>
* <span data-ttu-id="79a13-284">používání</span><span class="sxs-lookup"><span data-stu-id="79a13-284">using</span></span>
* <span data-ttu-id="79a13-285">while</span><span class="sxs-lookup"><span data-stu-id="79a13-285">while</span></span>

<span data-ttu-id="79a13-286">C#Klíčová slova Razor musí být uvozena double s `@(@C# Razor Keyword)` (například `@(@case)`).</span><span class="sxs-lookup"><span data-stu-id="79a13-286">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="79a13-287">První `@` řídicí sekvence analyzátor Razor.</span><span class="sxs-lookup"><span data-stu-id="79a13-287">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="79a13-288">Druhá `@` řídicí sekvence C# analyzátor.</span><span class="sxs-lookup"><span data-stu-id="79a13-288">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="79a13-289">Vyhrazená klíčová slova nepoužívá Razor</span><span class="sxs-lookup"><span data-stu-id="79a13-289">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="79a13-290">třída</span><span class="sxs-lookup"><span data-stu-id="79a13-290">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="79a13-291">Zkontrolovat syntaxi Razor C# třída vygenerovaná pro zobrazení</span><span class="sxs-lookup"><span data-stu-id="79a13-291">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="79a13-292">S .NET Core SDK 2.1 nebo novější [Razor SDK](xref:razor-pages/sdk) zpracovává kompilace souborech Razor.</span><span class="sxs-lookup"><span data-stu-id="79a13-292">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="79a13-293">Při sestavování projektu, generuje Razor SDK *obj / < build_configuration > / < target_framework_moniker > / Razor* adresáře v kořenové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="79a13-293">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="79a13-294">Struktura adresářů v rámci *Razor* directory zrcadlí adresářovou strukturu projektu.</span><span class="sxs-lookup"><span data-stu-id="79a13-294">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="79a13-295">Vezměte v úvahu následující adresářovou strukturu v projektu aplikace ASP.NET Core 2.1 Razor Pages cílí na .NET Core 2.1:</span><span class="sxs-lookup"><span data-stu-id="79a13-295">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="79a13-296">**Oblasti /**</span><span class="sxs-lookup"><span data-stu-id="79a13-296">**Areas/**</span></span>
  * <span data-ttu-id="79a13-297">**Správce /**</span><span class="sxs-lookup"><span data-stu-id="79a13-297">**Admin/**</span></span>
    * <span data-ttu-id="79a13-298">**Stránky /**</span><span class="sxs-lookup"><span data-stu-id="79a13-298">**Pages/**</span></span>
      * <span data-ttu-id="79a13-299">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="79a13-299">*Index.cshtml*</span></span>
      * <span data-ttu-id="79a13-300">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="79a13-300">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="79a13-301">**Stránky /**</span><span class="sxs-lookup"><span data-stu-id="79a13-301">**Pages/**</span></span>
  * <span data-ttu-id="79a13-302">**Sdílené /**</span><span class="sxs-lookup"><span data-stu-id="79a13-302">**Shared/**</span></span>
    * <span data-ttu-id="79a13-303">*_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="79a13-303">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="79a13-304">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="79a13-304">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="79a13-305">*_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="79a13-305">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="79a13-306">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="79a13-306">*Index.cshtml*</span></span>
  * <span data-ttu-id="79a13-307">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="79a13-307">*Index.cshtml.cs*</span></span>

<span data-ttu-id="79a13-308">Vytvoření projektu *ladění* konfigurace provede následující *obj* adresáře:</span><span class="sxs-lookup"><span data-stu-id="79a13-308">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="79a13-309">**obj /**</span><span class="sxs-lookup"><span data-stu-id="79a13-309">**obj/**</span></span>
  * <span data-ttu-id="79a13-310">**Ladění /**</span><span class="sxs-lookup"><span data-stu-id="79a13-310">**Debug/**</span></span>
    * <span data-ttu-id="79a13-311">**netcoreapp2.1 /**</span><span class="sxs-lookup"><span data-stu-id="79a13-311">**netcoreapp2.1/**</span></span>
      * <span data-ttu-id="79a13-312">**Razor /**</span><span class="sxs-lookup"><span data-stu-id="79a13-312">**Razor/**</span></span>
        * <span data-ttu-id="79a13-313">**Oblasti /**</span><span class="sxs-lookup"><span data-stu-id="79a13-313">**Areas/**</span></span>
          * <span data-ttu-id="79a13-314">**Správce /**</span><span class="sxs-lookup"><span data-stu-id="79a13-314">**Admin/**</span></span>
            * <span data-ttu-id="79a13-315">**Stránky /**</span><span class="sxs-lookup"><span data-stu-id="79a13-315">**Pages/**</span></span>
              * <span data-ttu-id="79a13-316">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="79a13-316">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="79a13-317">**Stránky /**</span><span class="sxs-lookup"><span data-stu-id="79a13-317">**Pages/**</span></span>
          * <span data-ttu-id="79a13-318">**Sdílené /**</span><span class="sxs-lookup"><span data-stu-id="79a13-318">**Shared/**</span></span>
            * <span data-ttu-id="79a13-319">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="79a13-319">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="79a13-320">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="79a13-320">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="79a13-321">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="79a13-321">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="79a13-322">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="79a13-322">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="79a13-323">Chcete-li zobrazit vygenerované třídy pro *Pages/Index.cshtml*, otevřete *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="79a13-323">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="79a13-324">Přidejte následující třídu do projektu ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="79a13-324">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="79a13-325">V `Startup.ConfigureServices`, přepsat `RazorTemplateEngine` přidal MVC s `CustomTemplateEngine` třídy:</span><span class="sxs-lookup"><span data-stu-id="79a13-325">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="79a13-326">Nastavit zarážku na `return csharpDocument;` příkazem `CustomTemplateEngine`.</span><span class="sxs-lookup"><span data-stu-id="79a13-326">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="79a13-327">Při spuštění programu se zastaví na zarážce, zobrazit hodnotu `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="79a13-327">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Text Visualizer zobrazení generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="79a13-329">Zobrazení vyhledávání a rozlišování velikosti písmen</span><span class="sxs-lookup"><span data-stu-id="79a13-329">View lookups and case sensitivity</span></span>

<span data-ttu-id="79a13-330">Zobrazovací modul Razor provádí velká a malá písmena vyhledávání pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="79a13-330">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="79a13-331">Nicméně skutečné vyhledávání je určeno podkladový systém souborů:</span><span class="sxs-lookup"><span data-stu-id="79a13-331">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="79a13-332">Na základě zdrojového souboru:</span><span class="sxs-lookup"><span data-stu-id="79a13-332">File based source:</span></span>
  * <span data-ttu-id="79a13-333">V operačních systémech se systémy souborů malá a velká písmena (například Windows) fyzický soubor poskytovatele vyhledávání jsou malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="79a13-333">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="79a13-334">Například `return View("Test")` výsledkem shody */Views/Home/Test.cshtml*, */Views/home/test.cshtml*a další varianty velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="79a13-334">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="79a13-335">V systémech souborů s malá a velká písmena (například Linux, OSX a s `EmbeddedFileProvider`), hledání jsou malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="79a13-335">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="79a13-336">Například `return View("Test")` konkrétně shody */Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="79a13-336">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="79a13-337">Předkompilované zobrazení: S ASP.NET Core 2.0 nebo novější hledání předkompilované zobrazení velká a malá písmena na všechny operační systémy.</span><span class="sxs-lookup"><span data-stu-id="79a13-337">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="79a13-338">Chování se shoduje s chování zprostředkovatele fyzického souboru ve Windows.</span><span class="sxs-lookup"><span data-stu-id="79a13-338">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="79a13-339">Pokud se zobrazeními předkompilované liší pouze v případě, výsledek vyhledávání je Nedeterministický.</span><span class="sxs-lookup"><span data-stu-id="79a13-339">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="79a13-340">Vývojáři nepodnikovým tak, aby odpovídaly malých a velkých písmen názvů použití malých a velkých souborů a adresářů:</span><span class="sxs-lookup"><span data-stu-id="79a13-340">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="79a13-341">Názvy oblastí, kontroleru a akce.</span><span class="sxs-lookup"><span data-stu-id="79a13-341">Area, controller, and action names.</span></span>
* <span data-ttu-id="79a13-342">Stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="79a13-342">Razor Pages.</span></span>

<span data-ttu-id="79a13-343">Odpovídající případ zajistí, že pro nasazení své názory, bez ohledu na podkladový systém souborů.</span><span class="sxs-lookup"><span data-stu-id="79a13-343">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>
