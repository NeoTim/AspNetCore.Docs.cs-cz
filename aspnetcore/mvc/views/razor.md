---
title: Referenční příručka syntaxe Razor pro ASP.NET Core
author: rick-anderson
description: Další informace o syntaxi Razor kód pro vložení do webových stránek kód založený na serveru.
ms.author: riande
ms.date: 10/26/2018
uid: mvc/views/razor
ms.openlocfilehash: 7f97be651c067e94f29eef4956c10d87ec031bed
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614282"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="b40c9-103">Referenční příručka syntaxe Razor pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b40c9-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="b40c9-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylora MÜLLENA](https://twitter.com/ntaylormullen), a [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="b40c9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="b40c9-105">Razor je syntaxe značek pro vkládání do webových stránek kód založený na serveru.</span><span class="sxs-lookup"><span data-stu-id="b40c9-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="b40c9-106">Syntaxe Razor se skládá z kódu Razor C#a HTML.</span><span class="sxs-lookup"><span data-stu-id="b40c9-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="b40c9-107">Máte soubory, které obvykle obsahují Razor *.cshtml* příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="b40c9-107">Files containing Razor generally have a *.cshtml* file extension.</span></span>

## <a name="rendering-html"></a><span data-ttu-id="b40c9-108">Vykreslování protokolu HTML</span><span class="sxs-lookup"><span data-stu-id="b40c9-108">Rendering HTML</span></span>

<span data-ttu-id="b40c9-109">Výchozí jazyk Razor je ve formátu HTML.</span><span class="sxs-lookup"><span data-stu-id="b40c9-109">The default Razor language is HTML.</span></span> <span data-ttu-id="b40c9-110">Vykreslování HTML z kód Razor se nijak neliší od vykreslování protokolu HTML ze souboru HTML.</span><span class="sxs-lookup"><span data-stu-id="b40c9-110">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="b40c9-111">Značka jazyka HTML v *.cshtml* souborech Razor je vykreslen metodou serveru beze změny.</span><span class="sxs-lookup"><span data-stu-id="b40c9-111">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="b40c9-112">Syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="b40c9-112">Razor syntax</span></span>

<span data-ttu-id="b40c9-113">Podporuje Razor C# a používá `@` symbol, který má přechod z HTML na C#.</span><span class="sxs-lookup"><span data-stu-id="b40c9-113">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="b40c9-114">Vyhodnotí Razor C# výrazy a vykreslí je ve výstupu protokolu HTML.</span><span class="sxs-lookup"><span data-stu-id="b40c9-114">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="b40c9-115">Když `@` následuje symbol [Razor rezervované klíčové slovo](#razor-reserved-keywords), přechází do kódu specifické pro syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="b40c9-115">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="b40c9-116">V opačném případě bude přecházet do prostého C#.</span><span class="sxs-lookup"><span data-stu-id="b40c9-116">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="b40c9-117">Řídicí `@` symbol v kódu Razor, použijte druhý `@` symbolů:</span><span class="sxs-lookup"><span data-stu-id="b40c9-117">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="b40c9-118">Kód se vykreslí v HTML pomocí jediného `@` symbolů:</span><span class="sxs-lookup"><span data-stu-id="b40c9-118">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="b40c9-119">Atributy HTML a obsah, který obsahuje e-mailové adresy nejsou s pracovat `@` symbol jako znak přechod.</span><span class="sxs-lookup"><span data-stu-id="b40c9-119">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="b40c9-120">E-mailové adresy v následujícím příkladu jsou zůstanou podle analýzy Razor:</span><span class="sxs-lookup"><span data-stu-id="b40c9-120">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="b40c9-121">Implicitní výrazy Razor</span><span class="sxs-lookup"><span data-stu-id="b40c9-121">Implicit Razor expressions</span></span>

<span data-ttu-id="b40c9-122">Implicitní Razor výrazy začínat `@` následovaný C# kódu:</span><span class="sxs-lookup"><span data-stu-id="b40c9-122">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="b40c9-123">S výjimkou produktů C# `await` – klíčové slovo, implicitní výrazů nesmí obsahovat mezery.</span><span class="sxs-lookup"><span data-stu-id="b40c9-123">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="b40c9-124">Pokud C# příkaz má vymazat koncové, může být intermingled mezery:</span><span class="sxs-lookup"><span data-stu-id="b40c9-124">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="b40c9-125">Implicitní výrazy **nelze** obsahovat C# obecných typů, jako znaky uvnitř hranatých závorek (`<>`) jsou interpretovány jako značku jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="b40c9-125">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="b40c9-126">Následující kód je **není** platné:</span><span class="sxs-lookup"><span data-stu-id="b40c9-126">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="b40c9-127">Předcházející kód vygeneruje chybu kompilátoru podobně jako na jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="b40c9-127">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="b40c9-128">Prvek "int" není uzavřený.</span><span class="sxs-lookup"><span data-stu-id="b40c9-128">The "int" element wasn't closed.</span></span> <span data-ttu-id="b40c9-129">Všechny elementy musí být buď samouzavírací nebo koncová značka.</span><span class="sxs-lookup"><span data-stu-id="b40c9-129">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="b40c9-130">Nelze převést skupinu metod 'GenericMethod' na nedelegující typ 'object'.</span><span class="sxs-lookup"><span data-stu-id="b40c9-130">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="b40c9-131">Chtěli jste vyvolat metodu? "</span><span class="sxs-lookup"><span data-stu-id="b40c9-131">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="b40c9-132">Volání obecné metody musí být uzavřen do [explicitní výraz Razor](#explicit-razor-expressions) nebo [blok kódu Razor](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="b40c9-132">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="b40c9-133">Explicitní výrazy Razor</span><span class="sxs-lookup"><span data-stu-id="b40c9-133">Explicit Razor expressions</span></span>

<span data-ttu-id="b40c9-134">Explicitní syntaxe Razor výrazů se skládají z `@` symbol vyvážené závorky.</span><span class="sxs-lookup"><span data-stu-id="b40c9-134">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="b40c9-135">Pokud chcete zobrazit čas poslední týden, se používá následující kód Razor:</span><span class="sxs-lookup"><span data-stu-id="b40c9-135">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="b40c9-136">Veškerý obsah v rámci `@()` závorky se vyhodnotí a vykreslí do výstupu.</span><span class="sxs-lookup"><span data-stu-id="b40c9-136">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="b40c9-137">Obecně implicitní výrazů, je popsáno v předchozí části, nesmí obsahovat mezery.</span><span class="sxs-lookup"><span data-stu-id="b40c9-137">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="b40c9-138">V následujícím kódu není jeden týden odečtena od aktuální čas:</span><span class="sxs-lookup"><span data-stu-id="b40c9-138">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="b40c9-139">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="b40c9-139">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="b40c9-140">Explicitní výrazy můžete použít ke zřetězení text s výsledek výrazu:</span><span class="sxs-lookup"><span data-stu-id="b40c9-140">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="b40c9-141">Bez explicitní výraz `<p>Age@joe.Age</p>` je považován za e-mailovou adresu a `<p>Age@joe.Age</p>` vykreslením.</span><span class="sxs-lookup"><span data-stu-id="b40c9-141">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="b40c9-142">Při zápisu jako explicitní výraz `<p>Age33</p>` vykreslením.</span><span class="sxs-lookup"><span data-stu-id="b40c9-142">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="b40c9-143">Explicitní výrazy můžete použít k vykreslení výstup z obecných metod v *.cshtml* soubory.</span><span class="sxs-lookup"><span data-stu-id="b40c9-143">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="b40c9-144">Následující kód ukazuje, jak chcete-li zobrazit tato chyba dříve způsobené závorkami C# obecný.</span><span class="sxs-lookup"><span data-stu-id="b40c9-144">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="b40c9-145">Kód je zapsán jako explicitní výraz:</span><span class="sxs-lookup"><span data-stu-id="b40c9-145">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="b40c9-146">Výraz kódování</span><span class="sxs-lookup"><span data-stu-id="b40c9-146">Expression encoding</span></span>

<span data-ttu-id="b40c9-147">C#jsou výrazy, které vedou na řetězec kódovaný jazykem HTML.</span><span class="sxs-lookup"><span data-stu-id="b40c9-147">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="b40c9-148">C#výrazy, které vedou k `IHtmlContent` jsou generovány přímo pomocí `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="b40c9-148">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="b40c9-149">C#výrazy, které není vyhodnocen na `IHtmlContent` jsou převedeny na řetězec pomocí `ToString` a kódováním než se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="b40c9-149">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="b40c9-150">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="b40c9-150">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="b40c9-151">Kód HTML je zobrazená v prohlížeči jako:</span><span class="sxs-lookup"><span data-stu-id="b40c9-151">The HTML is shown in the browser as:</span></span>

```
<span>Hello World</span>
```

<span data-ttu-id="b40c9-152">`HtmlHelper.Raw` výstup není kódovaný ale se vykresluje jako značka jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="b40c9-152">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="b40c9-153">Pomocí `HtmlHelper.Raw` unsanitized uživatele vstup představuje bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="b40c9-153">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="b40c9-154">Uživatelský vstup může obsahovat další zneužití nebo škodlivý jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b40c9-154">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="b40c9-155">Sanitaci uživatelský vstup je obtížné.</span><span class="sxs-lookup"><span data-stu-id="b40c9-155">Sanitizing user input is difficult.</span></span> <span data-ttu-id="b40c9-156">Vyhněte se použití `HtmlHelper.Raw` s uživatelským vstupem.</span><span class="sxs-lookup"><span data-stu-id="b40c9-156">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="b40c9-157">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="b40c9-157">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="b40c9-158">Bloky kódu Razor</span><span class="sxs-lookup"><span data-stu-id="b40c9-158">Razor code blocks</span></span>

<span data-ttu-id="b40c9-159">Bloky kódu Razor začínat `@` a jsou uzavřeny ve `{}`.</span><span class="sxs-lookup"><span data-stu-id="b40c9-159">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="b40c9-160">Na rozdíl od výrazy C# kód uvnitř bloků kódu není vykresleno.</span><span class="sxs-lookup"><span data-stu-id="b40c9-160">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="b40c9-161">Bloky kódu a výrazy v zobrazení sdílejí stejný obor a jsou definované v pořadí:</span><span class="sxs-lookup"><span data-stu-id="b40c9-161">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="b40c9-162">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="b40c9-162">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b40c9-163">V blocích kódu, deklarujte [lokální funkce](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) pomocí značek, která bude sloužit jako ukázka metody:</span><span class="sxs-lookup"><span data-stu-id="b40c9-163">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="b40c9-164">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="b40c9-164">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="b40c9-165">Implicitní přechody</span><span class="sxs-lookup"><span data-stu-id="b40c9-165">Implicit transitions</span></span>

<span data-ttu-id="b40c9-166">Je výchozí jazyk v bloku kódu C#, ale stránka Razor můžete přejít zpět na HTML:</span><span class="sxs-lookup"><span data-stu-id="b40c9-166">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="b40c9-167">Explicitní převod s oddělovači</span><span class="sxs-lookup"><span data-stu-id="b40c9-167">Explicit delimited transition</span></span>

<span data-ttu-id="b40c9-168">Pokud chcete definovat dílčí část objektu bloku kódu, který vykreslovat kód HTML, obklopit fragmentem syntaxi Razor znaků pro vykreslování  **\<text >** značky:</span><span class="sxs-lookup"><span data-stu-id="b40c9-168">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor **\<text>** tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="b40c9-169">Tuto metodu použijte k vykreslení HTML, který není obklopený značky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="b40c9-169">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="b40c9-170">Bez značky jazyka HTML nebo Razor dojde k chybě modulu runtime Razor.</span><span class="sxs-lookup"><span data-stu-id="b40c9-170">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="b40c9-171">**\<Text >** značka je vhodné pro řízení prázdné znaky, při vykreslování obsahu:</span><span class="sxs-lookup"><span data-stu-id="b40c9-171">The **\<text>** tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="b40c9-172">Pouze obsah mezi  **\<text >** je vykreslen.</span><span class="sxs-lookup"><span data-stu-id="b40c9-172">Only the content between the **\<text>** tag is rendered.</span></span>
* <span data-ttu-id="b40c9-173">Žádné prázdné znaky před nebo po  **\<text >** značky se zobrazí ve výstupu protokolu HTML.</span><span class="sxs-lookup"><span data-stu-id="b40c9-173">No whitespace before or after the **\<text>** tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition-with-"></a><span data-ttu-id="b40c9-174">Explicitní řádek přechodu se @:</span><span class="sxs-lookup"><span data-stu-id="b40c9-174">Explicit Line Transition with @:</span></span>

<span data-ttu-id="b40c9-175">K vykreslení rest celý řádek jako kód HTML uvnitř bloku kódu, použijte `@:` syntaxi:</span><span class="sxs-lookup"><span data-stu-id="b40c9-175">To render the rest of an entire line as HTML inside a code block, use the `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="b40c9-176">Bez `@:` v kódu, je vygenerována chyba za běhu Razor.</span><span class="sxs-lookup"><span data-stu-id="b40c9-176">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="b40c9-177">Upozornění: Navíc `@` znaky v souboru Razor mohou způsobit chyby kompilátoru příkazy v bloku.</span><span class="sxs-lookup"><span data-stu-id="b40c9-177">Warning: Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="b40c9-178">Tyto chyby kompilátoru může být obtížné pochopit, protože Skutečná chyba předchází oznámenou chybu.</span><span class="sxs-lookup"><span data-stu-id="b40c9-178">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="b40c9-179">K této chybě dochází po kombinování více implicitního/explicitního výrazů v jednom bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="b40c9-179">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="b40c9-180">Řídicí struktury</span><span class="sxs-lookup"><span data-stu-id="b40c9-180">Control structures</span></span>

<span data-ttu-id="b40c9-181">Řídicí struktury jsou rozšíření bloky kódu.</span><span class="sxs-lookup"><span data-stu-id="b40c9-181">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="b40c9-182">Všechny aspekty bloky kódu (Přechod na značky vložené C#) platí také pro následující struktury:</span><span class="sxs-lookup"><span data-stu-id="b40c9-182">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="b40c9-183">Podmíněné výrazy @if, else if, else, a @switch</span><span class="sxs-lookup"><span data-stu-id="b40c9-183">Conditionals @if, else if, else, and @switch</span></span>

<span data-ttu-id="b40c9-184">`@if` ovládací prvky, pokud kód se spustí:</span><span class="sxs-lookup"><span data-stu-id="b40c9-184">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="b40c9-185">`else` a `else if` nevyžadují `@` symbolů:</span><span class="sxs-lookup"><span data-stu-id="b40c9-185">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="b40c9-186">Následující kód ukazuje použití příkazu switch:</span><span class="sxs-lookup"><span data-stu-id="b40c9-186">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="b40c9-187">Opakování ve smyčce @for, @foreach, @while, a @do při</span><span class="sxs-lookup"><span data-stu-id="b40c9-187">Looping @for, @foreach, @while, and @do while</span></span>

<span data-ttu-id="b40c9-188">Bez vizuálního vzhledu HTML lze vykreslit s opakování ve smyčce řídicí příkazy.</span><span class="sxs-lookup"><span data-stu-id="b40c9-188">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="b40c9-189">Pokud chcete zobrazit seznam lidí:</span><span class="sxs-lookup"><span data-stu-id="b40c9-189">To render a list of people:</span></span>

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

<span data-ttu-id="b40c9-190">Podporují se následující příkazy opakování:</span><span class="sxs-lookup"><span data-stu-id="b40c9-190">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="b40c9-191">Složené @using</span><span class="sxs-lookup"><span data-stu-id="b40c9-191">Compound @using</span></span>

<span data-ttu-id="b40c9-192">V C#, `using` prohlášení se používá k zajištění uvolněn objekt.</span><span class="sxs-lookup"><span data-stu-id="b40c9-192">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="b40c9-193">V prostředí Razor stejný mechanismus slouží k vytvoření pomocné rutiny HTML, které obsahují další obsah.</span><span class="sxs-lookup"><span data-stu-id="b40c9-193">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="b40c9-194">V následujícím kódu pomocných rutin HTML, vykreslení značky formuláře s `@using` – příkaz:</span><span class="sxs-lookup"><span data-stu-id="b40c9-194">In the following code, HTML Helpers render a form tag with the `@using` statement:</span></span>

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

<span data-ttu-id="b40c9-195">Akce na úrovni oboru, můžete to provést pomocí [pomocných rutin značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="b40c9-195">Scope-level actions can be performed with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

### <a name="try-catch-finally"></a><span data-ttu-id="b40c9-196">@try, catch, finally</span><span class="sxs-lookup"><span data-stu-id="b40c9-196">@try, catch, finally</span></span>

<span data-ttu-id="b40c9-197">Zpracování výjimek je podobný C#:</span><span class="sxs-lookup"><span data-stu-id="b40c9-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>@lock

<span data-ttu-id="b40c9-198">Razor má schopnost chránit kritické oddíly s příkazy uzamčení:</span><span class="sxs-lookup"><span data-stu-id="b40c9-198">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="b40c9-199">Komentáře</span><span class="sxs-lookup"><span data-stu-id="b40c9-199">Comments</span></span>

<span data-ttu-id="b40c9-200">Podporuje Razor C# a komentáře HTML:</span><span class="sxs-lookup"><span data-stu-id="b40c9-200">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="b40c9-201">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="b40c9-201">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="b40c9-202">Komentáře syntaxe Razor jsou odebrána serverem, než se zobrazí webová stránka.</span><span class="sxs-lookup"><span data-stu-id="b40c9-202">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="b40c9-203">Používá syntaxi Razor `@*  *@` pro vymezení komentáře.</span><span class="sxs-lookup"><span data-stu-id="b40c9-203">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="b40c9-204">Následující kód je zakomentovaný, tak server nevykreslí žádné značky:</span><span class="sxs-lookup"><span data-stu-id="b40c9-204">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="b40c9-205">Direktivy</span><span class="sxs-lookup"><span data-stu-id="b40c9-205">Directives</span></span>

<span data-ttu-id="b40c9-206">Direktivy Razor jsou reprezentovány implicitní výrazy s následující vyhrazená klíčová slova `@` symbol.</span><span class="sxs-lookup"><span data-stu-id="b40c9-206">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="b40c9-207">Direktivu obvykle mění způsob zobrazení je analyzován nebo jinou funkci povolí.</span><span class="sxs-lookup"><span data-stu-id="b40c9-207">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="b40c9-208">Vysvětlení způsobu, jakým Razor generuje kód pro zobrazení usnadňuje pochopení fungování direktivy.</span><span class="sxs-lookup"><span data-stu-id="b40c9-208">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="b40c9-209">Kód vygeneruje třídu podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="b40c9-209">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="b40c9-210">Dále v tomto článku v části [Zkontrolujte syntaxi Razor C# třída vygenerovaná pro zobrazení](#inspect-the-razor-c-class-generated-for-a-view) vysvětluje, jak zobrazit tento generované třídy.</span><span class="sxs-lookup"><span data-stu-id="b40c9-210">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

<a name="using"></a>

### <a name="using"></a>@using

<span data-ttu-id="b40c9-211">`@using` Direktivy přidává C# `using` direktiv generované zobrazení:</span><span class="sxs-lookup"><span data-stu-id="b40c9-211">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

### <a name="model"></a>@model

<span data-ttu-id="b40c9-212">`@model` Direktiva Určuje typ modelu předána do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="b40c9-212">The `@model` directive specifies the type of the model passed to a view:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="b40c9-213">V aplikaci ASP.NET Core MVC vytvořené pomocí jednotlivých uživatelských účtů *Views/Account/Login.cshtml* zobrazení obsahuje následující deklaraci modelu:</span><span class="sxs-lookup"><span data-stu-id="b40c9-213">In an ASP.NET Core MVC app created with individual user accounts, the *Views/Account/Login.cshtml* view contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="b40c9-214">Dědí třídu vygenerovanou z `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="b40c9-214">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="b40c9-215">Zpřístupňuje Razor `Model` předána do zobrazení, vlastnosti pro přístup k modelu:</span><span class="sxs-lookup"><span data-stu-id="b40c9-215">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="b40c9-216">`@model` Direktiva Určuje typ této vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b40c9-216">The `@model` directive specifies the type of this property.</span></span> <span data-ttu-id="b40c9-217">Direktiva Určuje, `T` v `RazorPage<T>` , že generované třídy, která zobrazení je odvozena z.</span><span class="sxs-lookup"><span data-stu-id="b40c9-217">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="b40c9-218">Pokud `@model` – direktiva není zadán, `Model` vlastnost je typu `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="b40c9-218">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="b40c9-219">Z kontroleru zobrazení je předána hodnota modelu.</span><span class="sxs-lookup"><span data-stu-id="b40c9-219">The value of the model is passed from the controller to the view.</span></span> <span data-ttu-id="b40c9-220">Další informace najdete v tématu [se silnými typy modelů a &commat;– klíčové slovo modelu](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="b40c9-220">For more information, see [Strongly typed models and the &commat;model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="inherits"></a>@inherits

<span data-ttu-id="b40c9-221">`@inherits` – Direktiva poskytuje plnou kontrolu nad třída dědí zobrazení:</span><span class="sxs-lookup"><span data-stu-id="b40c9-221">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="b40c9-222">Následující kód je vlastní typ stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="b40c9-222">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="b40c9-223">`CustomText` Se zobrazí v zobrazení:</span><span class="sxs-lookup"><span data-stu-id="b40c9-223">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="b40c9-224">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="b40c9-224">The code renders the following HTML:</span></span>

```html
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

 <span data-ttu-id="b40c9-225">`@model` a `@inherits` je možné ve stejném zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b40c9-225">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="b40c9-226">`@inherits` může být v *_ViewImports.cshtml* soubor, který importuje zobrazení:</span><span class="sxs-lookup"><span data-stu-id="b40c9-226">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="b40c9-227">Následující kód je příkladem zobrazení se silnými typy:</span><span class="sxs-lookup"><span data-stu-id="b40c9-227">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="b40c9-228">Pokud "rick@contoso.com" je předán zobrazení v modelu, generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="b40c9-228">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

### <a name="inject"></a>@inject

<span data-ttu-id="b40c9-229">`@inject` Direktiva umožňuje vložit služby z stránky Razor [kontejneru služby](xref:fundamentals/dependency-injection) do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b40c9-229">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="b40c9-230">Další informace najdete v tématu [injektáž závislostí do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b40c9-230">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

### <a name="functions"></a>@functions

<span data-ttu-id="b40c9-231">`@functions` Direktiva umožňuje stránky Razor a přidat C# blok kódu do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="b40c9-231">The `@functions` directive enables a Razor Page to add a C# code block to a view:</span></span>

```cshtml
@functions { // C# Code }
```

<span data-ttu-id="b40c9-232">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b40c9-232">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="b40c9-233">Kód generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="b40c9-233">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="b40c9-234">Následující kód je vygenerovaný Razor C# třídy:</span><span class="sxs-lookup"><span data-stu-id="b40c9-234">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b40c9-235">`@functions` metody sloužit jako ukázka metody, když mají značky:</span><span class="sxs-lookup"><span data-stu-id="b40c9-235">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="b40c9-236">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="b40c9-236">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="section"></a>@section

<span data-ttu-id="b40c9-237">`@section` – Direktiva se používá ve spojení s [rozložení](xref:mvc/views/layout) povolit zobrazení k vykreslení obsahu v různých částech stránky HTML.</span><span class="sxs-lookup"><span data-stu-id="b40c9-237">The `@section` directive is used in conjunction with the [layout](xref:mvc/views/layout) to enable views to render content in different parts of the HTML page.</span></span> <span data-ttu-id="b40c9-238">Další informace najdete v tématu [oddíly](xref:mvc/views/layout#layout-sections-label).</span><span class="sxs-lookup"><span data-stu-id="b40c9-238">For more information, see [Sections](xref:mvc/views/layout#layout-sections-label).</span></span>

## <a name="templated-razor-delegates"></a><span data-ttu-id="b40c9-239">Šablony Razor delegátů</span><span class="sxs-lookup"><span data-stu-id="b40c9-239">Templated Razor delegates</span></span>

<span data-ttu-id="b40c9-240">Šablony Razor umožňují definovat fragment uživatelského rozhraní v následujícím formátu:</span><span class="sxs-lookup"><span data-stu-id="b40c9-240">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="b40c9-241">Následující příklad ukazuje, jak zadat bez vizuálního vzhledu Razor delegáta jako <xref:System.Func%602>.</span><span class="sxs-lookup"><span data-stu-id="b40c9-241">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="b40c9-242">[Dynamického typu](/dotnet/csharp/programming-guide/types/using-type-dynamic) je zadaná pro parametr metody, která zapouzdřuje delegáta.</span><span class="sxs-lookup"><span data-stu-id="b40c9-242">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="b40c9-243">[Typ objektu](/dotnet/csharp/language-reference/keywords/object) je zadán jako návratový typ delegáta.</span><span class="sxs-lookup"><span data-stu-id="b40c9-243">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="b40c9-244">Šablona se používá s <xref:System.Collections.Generic.List%601> z `Pet` , který má `Name` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="b40c9-244">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="b40c9-245">Šablona je vykreslen pomocí `pets` poskytnutých `foreach` – příkaz:</span><span class="sxs-lookup"><span data-stu-id="b40c9-245">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="b40c9-246">Vykresleného výstupu:</span><span class="sxs-lookup"><span data-stu-id="b40c9-246">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="b40c9-247">Šablony Razor vložené lze také zadat jako argument pro metodu.</span><span class="sxs-lookup"><span data-stu-id="b40c9-247">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="b40c9-248">V následujícím příkladu `Repeat` metoda obdrží šablona Razor.</span><span class="sxs-lookup"><span data-stu-id="b40c9-248">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="b40c9-249">Metoda používá šablony k vytvoření HTML obsah s opakování zadaný ze seznamu položek:</span><span class="sxs-lookup"><span data-stu-id="b40c9-249">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="b40c9-250">Pomocí seznam mazlíčků z předchozího příkladu `Repeat` metoda je volána pomocí:</span><span class="sxs-lookup"><span data-stu-id="b40c9-250">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="b40c9-251"><xref:System.Collections.Generic.List%601> z `Pet`.</span><span class="sxs-lookup"><span data-stu-id="b40c9-251"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="b40c9-252">Počet opakování každou mazlíčků.</span><span class="sxs-lookup"><span data-stu-id="b40c9-252">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="b40c9-253">Vložená šablona použitá pro položky seznamu neuspořádaný seznam.</span><span class="sxs-lookup"><span data-stu-id="b40c9-253">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="b40c9-254">Vykresleného výstupu:</span><span class="sxs-lookup"><span data-stu-id="b40c9-254">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="b40c9-255">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="b40c9-255">Tag Helpers</span></span>

<span data-ttu-id="b40c9-256">Existují tři direktivy, které se týkají [pomocných rutin značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="b40c9-256">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="b40c9-257">– Direktiva</span><span class="sxs-lookup"><span data-stu-id="b40c9-257">Directive</span></span> | <span data-ttu-id="b40c9-258">Funkce</span><span class="sxs-lookup"><span data-stu-id="b40c9-258">Function</span></span> |
| --------- | -------- |
| [<span data-ttu-id="b40c9-259">&commat;addTagHelper</span><span class="sxs-lookup"><span data-stu-id="b40c9-259">&commat;addTagHelper</span></span>](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="b40c9-260">Zpřístupní pomocných rutin značek k zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b40c9-260">Makes Tag Helpers available to a view.</span></span> |
| [<span data-ttu-id="b40c9-261">&commat;removeTagHelper</span><span class="sxs-lookup"><span data-stu-id="b40c9-261">&commat;removeTagHelper</span></span>](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="b40c9-262">Odebere ze zobrazení přidali dříve pomocných rutin značek.</span><span class="sxs-lookup"><span data-stu-id="b40c9-262">Removes Tag Helpers previously added from a view.</span></span> |
| [<span data-ttu-id="b40c9-263">&commat;tagHelperPrefix</span><span class="sxs-lookup"><span data-stu-id="b40c9-263">&commat;tagHelperPrefix</span></span>](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="b40c9-264">Určuje předponu značky k povolení podpory pomocné rutiny značky a aby explicitní použití pomocné rutiny značky.</span><span class="sxs-lookup"><span data-stu-id="b40c9-264">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="b40c9-265">Razor vyhrazená klíčová slova</span><span class="sxs-lookup"><span data-stu-id="b40c9-265">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="b40c9-266">Klíčová slova Razor</span><span class="sxs-lookup"><span data-stu-id="b40c9-266">Razor keywords</span></span>

* <span data-ttu-id="b40c9-267">stránka (vyžaduje ASP.NET Core 2.0 a novější)</span><span class="sxs-lookup"><span data-stu-id="b40c9-267">page (Requires ASP.NET Core 2.0 and later)</span></span>
* <span data-ttu-id="b40c9-268">– obor názvů</span><span class="sxs-lookup"><span data-stu-id="b40c9-268">namespace</span></span>
* <span data-ttu-id="b40c9-269">– funkce</span><span class="sxs-lookup"><span data-stu-id="b40c9-269">functions</span></span>
* <span data-ttu-id="b40c9-270">Dědí</span><span class="sxs-lookup"><span data-stu-id="b40c9-270">inherits</span></span>
* <span data-ttu-id="b40c9-271">model</span><span class="sxs-lookup"><span data-stu-id="b40c9-271">model</span></span>
* <span data-ttu-id="b40c9-272">section</span><span class="sxs-lookup"><span data-stu-id="b40c9-272">section</span></span>
* <span data-ttu-id="b40c9-273">pomocné rutiny (aktuálně se nepodporuje ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="b40c9-273">helper (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="b40c9-274">Klíčová slova Razor jsou uvozeny řídicími znaky s `@(Razor Keyword)` (například `@(functions)`).</span><span class="sxs-lookup"><span data-stu-id="b40c9-274">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="b40c9-275">C#Klíčová slova Razor</span><span class="sxs-lookup"><span data-stu-id="b40c9-275">C# Razor keywords</span></span>

* <span data-ttu-id="b40c9-276">case</span><span class="sxs-lookup"><span data-stu-id="b40c9-276">case</span></span>
* <span data-ttu-id="b40c9-277">do</span><span class="sxs-lookup"><span data-stu-id="b40c9-277">do</span></span>
* <span data-ttu-id="b40c9-278">default</span><span class="sxs-lookup"><span data-stu-id="b40c9-278">default</span></span>
* <span data-ttu-id="b40c9-279">pro</span><span class="sxs-lookup"><span data-stu-id="b40c9-279">for</span></span>
* <span data-ttu-id="b40c9-280">foreach</span><span class="sxs-lookup"><span data-stu-id="b40c9-280">foreach</span></span>
* <span data-ttu-id="b40c9-281">if</span><span class="sxs-lookup"><span data-stu-id="b40c9-281">if</span></span>
* <span data-ttu-id="b40c9-282">else</span><span class="sxs-lookup"><span data-stu-id="b40c9-282">else</span></span>
* <span data-ttu-id="b40c9-283">lock</span><span class="sxs-lookup"><span data-stu-id="b40c9-283">lock</span></span>
* <span data-ttu-id="b40c9-284">– přepínač</span><span class="sxs-lookup"><span data-stu-id="b40c9-284">switch</span></span>
* <span data-ttu-id="b40c9-285">Zkuste</span><span class="sxs-lookup"><span data-stu-id="b40c9-285">try</span></span>
* <span data-ttu-id="b40c9-286">catch</span><span class="sxs-lookup"><span data-stu-id="b40c9-286">catch</span></span>
* <span data-ttu-id="b40c9-287">finally</span><span class="sxs-lookup"><span data-stu-id="b40c9-287">finally</span></span>
* <span data-ttu-id="b40c9-288">používání</span><span class="sxs-lookup"><span data-stu-id="b40c9-288">using</span></span>
* <span data-ttu-id="b40c9-289">while</span><span class="sxs-lookup"><span data-stu-id="b40c9-289">while</span></span>

<span data-ttu-id="b40c9-290">C#Klíčová slova Razor musí být uvozena double s `@(@C# Razor Keyword)` (například `@(@case)`).</span><span class="sxs-lookup"><span data-stu-id="b40c9-290">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="b40c9-291">První `@` řídicí sekvence analyzátor Razor.</span><span class="sxs-lookup"><span data-stu-id="b40c9-291">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="b40c9-292">Druhá `@` řídicí sekvence C# analyzátor.</span><span class="sxs-lookup"><span data-stu-id="b40c9-292">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="b40c9-293">Vyhrazená klíčová slova nepoužívá Razor</span><span class="sxs-lookup"><span data-stu-id="b40c9-293">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="b40c9-294">třída</span><span class="sxs-lookup"><span data-stu-id="b40c9-294">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="b40c9-295">Zkontrolovat syntaxi Razor C# třída vygenerovaná pro zobrazení</span><span class="sxs-lookup"><span data-stu-id="b40c9-295">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b40c9-296">S .NET Core SDK 2.1 nebo novější [Razor SDK](xref:razor-pages/sdk) zpracovává kompilace souborech Razor.</span><span class="sxs-lookup"><span data-stu-id="b40c9-296">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="b40c9-297">Při sestavování projektu, generuje Razor SDK *obj / < build_configuration > / < target_framework_moniker > / Razor* adresáře v kořenové složce projektu.</span><span class="sxs-lookup"><span data-stu-id="b40c9-297">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="b40c9-298">Struktura adresářů v rámci *Razor* directory zrcadlí adresářovou strukturu projektu.</span><span class="sxs-lookup"><span data-stu-id="b40c9-298">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="b40c9-299">Vezměte v úvahu následující adresářovou strukturu v projektu aplikace ASP.NET Core 2.1 Razor Pages cílí na .NET Core 2.1:</span><span class="sxs-lookup"><span data-stu-id="b40c9-299">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="b40c9-300">**Oblasti /**</span><span class="sxs-lookup"><span data-stu-id="b40c9-300">**Areas/**</span></span>
  * <span data-ttu-id="b40c9-301">**Správce /**</span><span class="sxs-lookup"><span data-stu-id="b40c9-301">**Admin/**</span></span>
    * <span data-ttu-id="b40c9-302">**Stránky /**</span><span class="sxs-lookup"><span data-stu-id="b40c9-302">**Pages/**</span></span>
      * <span data-ttu-id="b40c9-303">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="b40c9-303">*Index.cshtml*</span></span>
      * <span data-ttu-id="b40c9-304">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40c9-304">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="b40c9-305">**Stránky /**</span><span class="sxs-lookup"><span data-stu-id="b40c9-305">**Pages/**</span></span>
  * <span data-ttu-id="b40c9-306">**Sdílené /**</span><span class="sxs-lookup"><span data-stu-id="b40c9-306">**Shared/**</span></span>
    * <span data-ttu-id="b40c9-307">*_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="b40c9-307">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="b40c9-308">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="b40c9-308">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="b40c9-309">*_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="b40c9-309">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="b40c9-310">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="b40c9-310">*Index.cshtml*</span></span>
  * <span data-ttu-id="b40c9-311">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40c9-311">*Index.cshtml.cs*</span></span>

<span data-ttu-id="b40c9-312">Vytvoření projektu *ladění* konfigurace provede následující *obj* adresáře:</span><span class="sxs-lookup"><span data-stu-id="b40c9-312">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="b40c9-313">**obj /**</span><span class="sxs-lookup"><span data-stu-id="b40c9-313">**obj/**</span></span>
  * <span data-ttu-id="b40c9-314">**Ladění /**</span><span class="sxs-lookup"><span data-stu-id="b40c9-314">**Debug/**</span></span>
    * <span data-ttu-id="b40c9-315">**netcoreapp2.1 /**</span><span class="sxs-lookup"><span data-stu-id="b40c9-315">**netcoreapp2.1/**</span></span>
      * <span data-ttu-id="b40c9-316">**Razor /**</span><span class="sxs-lookup"><span data-stu-id="b40c9-316">**Razor/**</span></span>
        * <span data-ttu-id="b40c9-317">**Oblasti /**</span><span class="sxs-lookup"><span data-stu-id="b40c9-317">**Areas/**</span></span>
          * <span data-ttu-id="b40c9-318">**Správce /**</span><span class="sxs-lookup"><span data-stu-id="b40c9-318">**Admin/**</span></span>
            * <span data-ttu-id="b40c9-319">**Stránky /**</span><span class="sxs-lookup"><span data-stu-id="b40c9-319">**Pages/**</span></span>
              * <span data-ttu-id="b40c9-320">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40c9-320">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="b40c9-321">**Stránky /**</span><span class="sxs-lookup"><span data-stu-id="b40c9-321">**Pages/**</span></span>
          * <span data-ttu-id="b40c9-322">**Sdílené /**</span><span class="sxs-lookup"><span data-stu-id="b40c9-322">**Shared/**</span></span>
            * <span data-ttu-id="b40c9-323">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40c9-323">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="b40c9-324">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40c9-324">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="b40c9-325">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40c9-325">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="b40c9-326">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40c9-326">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="b40c9-327">Chcete-li zobrazit vygenerované třídy pro *Pages/Index.cshtml*, otevřete *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="b40c9-327">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="b40c9-328">Přidejte následující třídu do projektu ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="b40c9-328">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="b40c9-329">V `Startup.ConfigureServices`, přepsat `RazorTemplateEngine` přidal MVC s `CustomTemplateEngine` třídy:</span><span class="sxs-lookup"><span data-stu-id="b40c9-329">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="b40c9-330">Nastavit zarážku na `return csharpDocument;` příkazem `CustomTemplateEngine`.</span><span class="sxs-lookup"><span data-stu-id="b40c9-330">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="b40c9-331">Při spuštění programu se zastaví na zarážce, zobrazit hodnotu `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="b40c9-331">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Text Visualizer zobrazení generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="b40c9-333">Zobrazení vyhledávání a rozlišování velikosti písmen</span><span class="sxs-lookup"><span data-stu-id="b40c9-333">View lookups and case sensitivity</span></span>

<span data-ttu-id="b40c9-334">Zobrazovací modul Razor provádí velká a malá písmena vyhledávání pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b40c9-334">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="b40c9-335">Nicméně skutečné vyhledávání je určeno podkladový systém souborů:</span><span class="sxs-lookup"><span data-stu-id="b40c9-335">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="b40c9-336">Na základě zdrojového souboru:</span><span class="sxs-lookup"><span data-stu-id="b40c9-336">File based source:</span></span>
  * <span data-ttu-id="b40c9-337">V operačních systémech se systémy souborů malá a velká písmena (například Windows) fyzický soubor poskytovatele vyhledávání jsou malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="b40c9-337">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="b40c9-338">Například `return View("Test")` výsledkem shody */Views/Home/Test.cshtml*, */Views/home/test.cshtml*a další varianty velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="b40c9-338">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="b40c9-339">V systémech souborů s malá a velká písmena (například Linux, OSX a s `EmbeddedFileProvider`), hledání jsou malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="b40c9-339">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="b40c9-340">Například `return View("Test")` konkrétně shody */Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b40c9-340">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="b40c9-341">Předkompilované zobrazení: S ASP.NET Core 2.0 nebo novější hledání předkompilované zobrazení velká a malá písmena na všechny operační systémy.</span><span class="sxs-lookup"><span data-stu-id="b40c9-341">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="b40c9-342">Chování se shoduje s chování zprostředkovatele fyzického souboru ve Windows.</span><span class="sxs-lookup"><span data-stu-id="b40c9-342">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="b40c9-343">Pokud se zobrazeními předkompilované liší pouze v případě, výsledek vyhledávání je Nedeterministický.</span><span class="sxs-lookup"><span data-stu-id="b40c9-343">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="b40c9-344">Vývojáři nepodnikovým tak, aby odpovídaly malých a velkých písmen názvů použití malých a velkých souborů a adresářů:</span><span class="sxs-lookup"><span data-stu-id="b40c9-344">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="b40c9-345">Názvy oblastí, kontroleru a akce.</span><span class="sxs-lookup"><span data-stu-id="b40c9-345">Area, controller, and action names.</span></span>
* <span data-ttu-id="b40c9-346">Stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="b40c9-346">Razor Pages.</span></span>

<span data-ttu-id="b40c9-347">Odpovídající případ zajistí, že pro nasazení své názory, bez ohledu na podkladový systém souborů.</span><span class="sxs-lookup"><span data-stu-id="b40c9-347">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>
