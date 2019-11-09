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
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="3e01b-103">Razor syntax reference for ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3e01b-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="3e01b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="3e01b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="3e01b-105">Razor is a markup syntax for embedding server-based code into webpages.</span><span class="sxs-lookup"><span data-stu-id="3e01b-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="3e01b-106">The Razor syntax consists of Razor markup, C#, and HTML.</span><span class="sxs-lookup"><span data-stu-id="3e01b-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="3e01b-107">Files containing Razor generally have a *.cshtml* file extension.</span><span class="sxs-lookup"><span data-stu-id="3e01b-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="3e01b-108">Razor is also found in [Razor components](xref:blazor/components) files ( *.razor*).</span><span class="sxs-lookup"><span data-stu-id="3e01b-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="3e01b-109">Rendering HTML</span><span class="sxs-lookup"><span data-stu-id="3e01b-109">Rendering HTML</span></span>

<span data-ttu-id="3e01b-110">The default Razor language is HTML.</span><span class="sxs-lookup"><span data-stu-id="3e01b-110">The default Razor language is HTML.</span></span> <span data-ttu-id="3e01b-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span><span class="sxs-lookup"><span data-stu-id="3e01b-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="3e01b-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span><span class="sxs-lookup"><span data-stu-id="3e01b-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="3e01b-113">Syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="3e01b-113">Razor syntax</span></span>

<span data-ttu-id="3e01b-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span><span class="sxs-lookup"><span data-stu-id="3e01b-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="3e01b-115">Razor evaluates C# expressions and renders them in the HTML output.</span><span class="sxs-lookup"><span data-stu-id="3e01b-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="3e01b-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span><span class="sxs-lookup"><span data-stu-id="3e01b-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="3e01b-117">Otherwise, it transitions into plain C#.</span><span class="sxs-lookup"><span data-stu-id="3e01b-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="3e01b-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span><span class="sxs-lookup"><span data-stu-id="3e01b-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="3e01b-119">The code is rendered in HTML with a single `@` symbol:</span><span class="sxs-lookup"><span data-stu-id="3e01b-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="3e01b-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span><span class="sxs-lookup"><span data-stu-id="3e01b-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="3e01b-121">The email addresses in the following example are untouched by Razor parsing:</span><span class="sxs-lookup"><span data-stu-id="3e01b-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="3e01b-122">Implicit Razor expressions</span><span class="sxs-lookup"><span data-stu-id="3e01b-122">Implicit Razor expressions</span></span>

<span data-ttu-id="3e01b-123">Implicit Razor expressions start with `@` followed by C# code:</span><span class="sxs-lookup"><span data-stu-id="3e01b-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="3e01b-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span><span class="sxs-lookup"><span data-stu-id="3e01b-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="3e01b-125">If the C# statement has a clear ending, spaces can be intermingled:</span><span class="sxs-lookup"><span data-stu-id="3e01b-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="3e01b-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span><span class="sxs-lookup"><span data-stu-id="3e01b-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="3e01b-127">The following code is **not** valid:</span><span class="sxs-lookup"><span data-stu-id="3e01b-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="3e01b-128">The preceding code generates a compiler error similar to one of the following:</span><span class="sxs-lookup"><span data-stu-id="3e01b-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="3e01b-129">The "int" element wasn't closed.</span><span class="sxs-lookup"><span data-stu-id="3e01b-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="3e01b-130">All elements must be either self-closing or have a matching end tag.</span><span class="sxs-lookup"><span data-stu-id="3e01b-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="3e01b-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span><span class="sxs-lookup"><span data-stu-id="3e01b-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="3e01b-132">Did you intend to invoke the method?\`</span><span class="sxs-lookup"><span data-stu-id="3e01b-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="3e01b-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="3e01b-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="3e01b-134">Explicit Razor expressions</span><span class="sxs-lookup"><span data-stu-id="3e01b-134">Explicit Razor expressions</span></span>

<span data-ttu-id="3e01b-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span><span class="sxs-lookup"><span data-stu-id="3e01b-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="3e01b-136">To render last week's time, the following Razor markup is used:</span><span class="sxs-lookup"><span data-stu-id="3e01b-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="3e01b-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span><span class="sxs-lookup"><span data-stu-id="3e01b-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="3e01b-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span><span class="sxs-lookup"><span data-stu-id="3e01b-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="3e01b-139">In the following code, one week isn't subtracted from the current time:</span><span class="sxs-lookup"><span data-stu-id="3e01b-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="3e01b-140">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="3e01b-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="3e01b-141">Explicit expressions can be used to concatenate text with an expression result:</span><span class="sxs-lookup"><span data-stu-id="3e01b-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="3e01b-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span><span class="sxs-lookup"><span data-stu-id="3e01b-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="3e01b-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span><span class="sxs-lookup"><span data-stu-id="3e01b-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="3e01b-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span><span class="sxs-lookup"><span data-stu-id="3e01b-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="3e01b-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span><span class="sxs-lookup"><span data-stu-id="3e01b-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="3e01b-146">The code is written as an explicit expression:</span><span class="sxs-lookup"><span data-stu-id="3e01b-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="3e01b-147">Expression encoding</span><span class="sxs-lookup"><span data-stu-id="3e01b-147">Expression encoding</span></span>

<span data-ttu-id="3e01b-148">C# expressions that evaluate to a string are HTML encoded.</span><span class="sxs-lookup"><span data-stu-id="3e01b-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="3e01b-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="3e01b-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="3e01b-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span><span class="sxs-lookup"><span data-stu-id="3e01b-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="3e01b-151">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="3e01b-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="3e01b-152">The HTML is shown in the browser as:</span><span class="sxs-lookup"><span data-stu-id="3e01b-152">The HTML is shown in the browser as:</span></span>

```
<span>Hello World</span>
```

<span data-ttu-id="3e01b-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span><span class="sxs-lookup"><span data-stu-id="3e01b-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="3e01b-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span><span class="sxs-lookup"><span data-stu-id="3e01b-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="3e01b-155">User input might contain malicious JavaScript or other exploits.</span><span class="sxs-lookup"><span data-stu-id="3e01b-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="3e01b-156">Sanitizing user input is difficult.</span><span class="sxs-lookup"><span data-stu-id="3e01b-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="3e01b-157">Avoid using `HtmlHelper.Raw` with user input.</span><span class="sxs-lookup"><span data-stu-id="3e01b-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="3e01b-158">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="3e01b-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="3e01b-159">Razor code blocks</span><span class="sxs-lookup"><span data-stu-id="3e01b-159">Razor code blocks</span></span>

<span data-ttu-id="3e01b-160">Razor code blocks start with `@` and are enclosed by `{}`.</span><span class="sxs-lookup"><span data-stu-id="3e01b-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="3e01b-161">Unlike expressions, C# code inside code blocks isn't rendered.</span><span class="sxs-lookup"><span data-stu-id="3e01b-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="3e01b-162">Code blocks and expressions in a view share the same scope and are defined in order:</span><span class="sxs-lookup"><span data-stu-id="3e01b-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="3e01b-163">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="3e01b-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3e01b-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span><span class="sxs-lookup"><span data-stu-id="3e01b-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="3e01b-165">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="3e01b-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="3e01b-166">Implicit transitions</span><span class="sxs-lookup"><span data-stu-id="3e01b-166">Implicit transitions</span></span>

<span data-ttu-id="3e01b-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span><span class="sxs-lookup"><span data-stu-id="3e01b-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="3e01b-168">Explicit delimited transition</span><span class="sxs-lookup"><span data-stu-id="3e01b-168">Explicit delimited transition</span></span>

<span data-ttu-id="3e01b-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span><span class="sxs-lookup"><span data-stu-id="3e01b-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="3e01b-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span><span class="sxs-lookup"><span data-stu-id="3e01b-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="3e01b-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span><span class="sxs-lookup"><span data-stu-id="3e01b-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="3e01b-172">The `<text>` tag is useful to control whitespace when rendering content:</span><span class="sxs-lookup"><span data-stu-id="3e01b-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="3e01b-173">Only the content between the `<text>` tag is rendered.</span><span class="sxs-lookup"><span data-stu-id="3e01b-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="3e01b-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span><span class="sxs-lookup"><span data-stu-id="3e01b-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="3e01b-175">Explicit line transition</span><span class="sxs-lookup"><span data-stu-id="3e01b-175">Explicit line transition</span></span>

<span data-ttu-id="3e01b-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span><span class="sxs-lookup"><span data-stu-id="3e01b-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="3e01b-177">Bez `@:` v kódu je vygenerována chyba modulu runtime Razor.</span><span class="sxs-lookup"><span data-stu-id="3e01b-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="3e01b-178">Nadbytečné `@` znaky v souboru Razor mohou způsobit chyby kompilátoru v příkazech později v bloku.</span><span class="sxs-lookup"><span data-stu-id="3e01b-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="3e01b-179">Tyto chyby kompilátoru mohou být obtížné pochopit, protože před nahlášenou chybou dojde k aktuální chybě.</span><span class="sxs-lookup"><span data-stu-id="3e01b-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="3e01b-180">Tato chyba je společná po Zkombinování více implicitních nebo explicitních výrazů do jednoho bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="3e01b-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="3e01b-181">Řídicí struktury</span><span class="sxs-lookup"><span data-stu-id="3e01b-181">Control structures</span></span>

<span data-ttu-id="3e01b-182">Řídicí struktury jsou rozšíření bloků kódu.</span><span class="sxs-lookup"><span data-stu-id="3e01b-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="3e01b-183">Všechny aspekty bloků kódu (přechod na označení, vloženo C#) platí také pro následující struktury:</span><span class="sxs-lookup"><span data-stu-id="3e01b-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="3e01b-184">Podmíněné \@if, else if, else a \@Switch</span><span class="sxs-lookup"><span data-stu-id="3e01b-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="3e01b-185">`@if` ovládací prvky při spuštění kódu:</span><span class="sxs-lookup"><span data-stu-id="3e01b-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="3e01b-186">`else` a `else if` nevyžadují symbol `@`:</span><span class="sxs-lookup"><span data-stu-id="3e01b-186">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="3e01b-187">Následující kód ukazuje, jak použít příkaz switch:</span><span class="sxs-lookup"><span data-stu-id="3e01b-187">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="3e01b-188">Opakování \@pro, \@foreach, \@while a \@</span><span class="sxs-lookup"><span data-stu-id="3e01b-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="3e01b-189">HTML s šablonou lze vykreslit pomocí příkazů pro opakování.</span><span class="sxs-lookup"><span data-stu-id="3e01b-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="3e01b-190">Vykreslit seznam osob:</span><span class="sxs-lookup"><span data-stu-id="3e01b-190">To render a list of people:</span></span>

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

<span data-ttu-id="3e01b-191">Podporovány jsou následující příkazy smyček:</span><span class="sxs-lookup"><span data-stu-id="3e01b-191">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="3e01b-192">Složené \@using</span><span class="sxs-lookup"><span data-stu-id="3e01b-192">Compound \@using</span></span>

<span data-ttu-id="3e01b-193">V C#nástroji se k zajištění uvolnění objektu používá příkaz `using`.</span><span class="sxs-lookup"><span data-stu-id="3e01b-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="3e01b-194">V Razor je stejný mechanismus použit k vytváření pomocníků HTML, které obsahují další obsah.</span><span class="sxs-lookup"><span data-stu-id="3e01b-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="3e01b-195">V následujícím kódu pomocník HTML vykresluje značku `<form>` pomocí příkazu `@using`:</span><span class="sxs-lookup"><span data-stu-id="3e01b-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="3e01b-196">\@try, catch, finally</span><span class="sxs-lookup"><span data-stu-id="3e01b-196">\@try, catch, finally</span></span>

<span data-ttu-id="3e01b-197">Zpracování výjimek je podobné jako C#:</span><span class="sxs-lookup"><span data-stu-id="3e01b-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="3e01b-198">\@zámek</span><span class="sxs-lookup"><span data-stu-id="3e01b-198">\@lock</span></span>

<span data-ttu-id="3e01b-199">Razor má možnost chránit kritické oddíly pomocí příkazů Lock:</span><span class="sxs-lookup"><span data-stu-id="3e01b-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="3e01b-200">Komentáře</span><span class="sxs-lookup"><span data-stu-id="3e01b-200">Comments</span></span>

<span data-ttu-id="3e01b-201">Razor podporuje C# a komentáře HTML:</span><span class="sxs-lookup"><span data-stu-id="3e01b-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="3e01b-202">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="3e01b-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="3e01b-203">Před vykreslením webové stránky jsou komentáře Razor odebrány serverem.</span><span class="sxs-lookup"><span data-stu-id="3e01b-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="3e01b-204">Razor používá `@*  *@` k vymezení komentářů.</span><span class="sxs-lookup"><span data-stu-id="3e01b-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="3e01b-205">Následující kód je zakomentován, takže server nevykresluje žádné značky:</span><span class="sxs-lookup"><span data-stu-id="3e01b-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="3e01b-206">Direktivy</span><span class="sxs-lookup"><span data-stu-id="3e01b-206">Directives</span></span>

<span data-ttu-id="3e01b-207">Direktivy Razor jsou reprezentovány implicitními výrazy s vyhrazenými klíčovými slovy za symbolem `@`.</span><span class="sxs-lookup"><span data-stu-id="3e01b-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="3e01b-208">Direktiva obvykle mění způsob, jakým se analyzuje zobrazení, nebo umožňuje různé funkce.</span><span class="sxs-lookup"><span data-stu-id="3e01b-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="3e01b-209">Pochopení, jak Razor generuje kód pro zobrazení, usnadňuje pochopení, jak direktivy fungují.</span><span class="sxs-lookup"><span data-stu-id="3e01b-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="3e01b-210">Kód generuje třídu podobnou této:</span><span class="sxs-lookup"><span data-stu-id="3e01b-210">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="3e01b-211">Později v tomto článku oddíl [kontroluje třídu Razor C# generovanou pro zobrazení](#inspect-the-razor-c-class-generated-for-a-view) vysvětluje, jak zobrazit tuto generovanou třídu.</span><span class="sxs-lookup"><span data-stu-id="3e01b-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="3e01b-212">atribut \@</span><span class="sxs-lookup"><span data-stu-id="3e01b-212">\@attribute</span></span>

<span data-ttu-id="3e01b-213">Direktiva `@attribute` přidá daný atribut třídě vygenerované stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3e01b-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="3e01b-214">Následující příklad přidá atribut `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="3e01b-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="3e01b-215">kód \@</span><span class="sxs-lookup"><span data-stu-id="3e01b-215">\@code</span></span>

<span data-ttu-id="3e01b-216">*This scenario only applies to Razor components (.razor).*</span><span class="sxs-lookup"><span data-stu-id="3e01b-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="3e01b-217">`@code` blok umožňuje [komponentě Razor](xref:blazor/components) přidat C# členy (pole, vlastnosti a metody) do komponenty:</span><span class="sxs-lookup"><span data-stu-id="3e01b-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```cshtml
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="3e01b-218">Pro součásti Razor je `@code` aliasem [@functions](#functions) a doporučuje se nad `@functions`.</span><span class="sxs-lookup"><span data-stu-id="3e01b-218">For Razor components, `@code` is an alias of [@functions](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="3e01b-219">Je přípustný více než jeden blok `@code`.</span><span class="sxs-lookup"><span data-stu-id="3e01b-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="3e01b-220">funkce \@</span><span class="sxs-lookup"><span data-stu-id="3e01b-220">\@functions</span></span>

<span data-ttu-id="3e01b-221">Direktiva `@functions` umožňuje přidat C# členy (pole, vlastnosti a metody) do generované třídy:</span><span class="sxs-lookup"><span data-stu-id="3e01b-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3e01b-222">V [součástech Razor](xref:blazor/components)použijte `@code` přes `@functions` k přidání C# členů.</span><span class="sxs-lookup"><span data-stu-id="3e01b-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="3e01b-223">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3e01b-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="3e01b-224">Kód generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="3e01b-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="3e01b-225">Následující kód je vygenerovaná třída Razor C# :</span><span class="sxs-lookup"><span data-stu-id="3e01b-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3e01b-226">`@functions` metody slouží jako metody šablonování, pokud mají značky:</span><span class="sxs-lookup"><span data-stu-id="3e01b-226">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="3e01b-227">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="3e01b-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="3e01b-228">\@implementuje</span><span class="sxs-lookup"><span data-stu-id="3e01b-228">\@implements</span></span>

<span data-ttu-id="3e01b-229">Direktiva `@implements` implementuje rozhraní pro generovanou třídu.</span><span class="sxs-lookup"><span data-stu-id="3e01b-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="3e01b-230">Následující příklad implementuje <xref:System.IDisposable?displayProperty=fullName> tak, aby mohla být volána metoda <xref:System.IDisposable.Dispose*>:</span><span class="sxs-lookup"><span data-stu-id="3e01b-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

### <a name="inherits"></a><span data-ttu-id="3e01b-231">\@dědí</span><span class="sxs-lookup"><span data-stu-id="3e01b-231">\@inherits</span></span>

<span data-ttu-id="3e01b-232">Direktiva `@inherits` poskytuje úplnou kontrolu nad třídou, kterou zobrazení dědí:</span><span class="sxs-lookup"><span data-stu-id="3e01b-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="3e01b-233">Následující kód je vlastní typ stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="3e01b-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="3e01b-234">`CustomText` se zobrazí v zobrazení:</span><span class="sxs-lookup"><span data-stu-id="3e01b-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="3e01b-235">Kód vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="3e01b-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="3e01b-236">`@model` a `@inherits` lze použít ve stejném zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3e01b-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="3e01b-237">`@inherits` může být v souboru *_ViewImports. cshtml* , který zobrazení importuje:</span><span class="sxs-lookup"><span data-stu-id="3e01b-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="3e01b-238">Následující kód je příkladem zobrazení silného typu:</span><span class="sxs-lookup"><span data-stu-id="3e01b-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="3e01b-239">Pokud je v modelu předána možnostrick@contoso.com, zobrazení generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="3e01b-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="3e01b-240">\@vkládání</span><span class="sxs-lookup"><span data-stu-id="3e01b-240">\@inject</span></span>

<span data-ttu-id="3e01b-241">Direktiva `@inject` umožňuje, aby stránka Razor mohla vložit službu z [kontejneru služby](xref:fundamentals/dependency-injection) do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3e01b-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="3e01b-242">Další informace najdete v tématu [vkládání závislostí do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3e01b-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="3e01b-243">rozložení \@</span><span class="sxs-lookup"><span data-stu-id="3e01b-243">\@layout</span></span>

<span data-ttu-id="3e01b-244">*This scenario only applies to Razor components (.razor).*</span><span class="sxs-lookup"><span data-stu-id="3e01b-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="3e01b-245">Direktiva `@layout` určuje rozložení součásti Razor.</span><span class="sxs-lookup"><span data-stu-id="3e01b-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="3e01b-246">Komponenty rozložení se používají k zamezení Duplikace kódu a nekonzistenci.</span><span class="sxs-lookup"><span data-stu-id="3e01b-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="3e01b-247">Další informace najdete v tématu <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="3e01b-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="3e01b-248">model \@</span><span class="sxs-lookup"><span data-stu-id="3e01b-248">\@model</span></span>

<span data-ttu-id="3e01b-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="3e01b-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="3e01b-250">Direktiva `@model` určuje typ modelu předaného zobrazení nebo stránce:</span><span class="sxs-lookup"><span data-stu-id="3e01b-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="3e01b-251">V ASP.NET Core MVC nebo aplikace Razor Pages vytvořené pomocí individuálních uživatelských účtů, *zobrazení/účet/přihlášení. cshtml* obsahuje následující deklaraci modelu:</span><span class="sxs-lookup"><span data-stu-id="3e01b-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="3e01b-252">Třída vygenerovaná z `RazorPage<dynamic>`dědí:</span><span class="sxs-lookup"><span data-stu-id="3e01b-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="3e01b-253">Razor zpřístupňuje vlastnost `Model` pro přístup k modelu předanému zobrazení:</span><span class="sxs-lookup"><span data-stu-id="3e01b-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="3e01b-254">Direktiva `@model` určuje typ vlastnosti `Model`.</span><span class="sxs-lookup"><span data-stu-id="3e01b-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="3e01b-255">Direktiva určuje `T` v `RazorPage<T>`, ze které byla vygenerována třída, ze které je odvozeno zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3e01b-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="3e01b-256">Pokud není zadána direktiva `@model`, je vlastnost `Model` typu `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="3e01b-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="3e01b-257">Další informace naleznete v tématu [modely silného typu a klíčové slovo @model](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="3e01b-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="3e01b-258">obor názvů \@</span><span class="sxs-lookup"><span data-stu-id="3e01b-258">\@namespace</span></span>

<span data-ttu-id="3e01b-259">Direktiva `@namespace`:</span><span class="sxs-lookup"><span data-stu-id="3e01b-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="3e01b-260">Nastaví obor názvů třídy vygenerované stránky Razor, zobrazení MVC nebo komponenty Razor.</span><span class="sxs-lookup"><span data-stu-id="3e01b-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="3e01b-261">Nastaví kořenové obory názvů pro třídy stránky, zobrazení nebo komponenty z nejbližšího souboru importu ve stromové struktuře, *_ViewImports. cshtml* (zobrazení nebo stránky) nebo *_Imports. Razor* (komponenty Razor).</span><span class="sxs-lookup"><span data-stu-id="3e01b-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="3e01b-262">Pro Razor Pages příklad zobrazený v následující tabulce:</span><span class="sxs-lookup"><span data-stu-id="3e01b-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="3e01b-263">Každá stránka importuje *stránky/_ViewImports. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3e01b-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="3e01b-264">*Pages/_ViewImports. cshtml* obsahuje `@namespace Hello.World`.</span><span class="sxs-lookup"><span data-stu-id="3e01b-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="3e01b-265">Každá stránka má `Hello.World` jako kořen oboru názvů IT.</span><span class="sxs-lookup"><span data-stu-id="3e01b-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="3e01b-266">Page</span><span class="sxs-lookup"><span data-stu-id="3e01b-266">Page</span></span>                                        | <span data-ttu-id="3e01b-267">Obor názvů</span><span class="sxs-lookup"><span data-stu-id="3e01b-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="3e01b-268">*Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e01b-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="3e01b-269">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e01b-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="3e01b-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e01b-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="3e01b-271">Předchozí relace platí pro soubory importu používané s zobrazeními MVC a komponentami Razor.</span><span class="sxs-lookup"><span data-stu-id="3e01b-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="3e01b-272">Pokud má několik importových souborů direktivu `@namespace`, soubor, který je nejblíže stránce, zobrazení nebo komponentě v adresářovém stromu, slouží k nastavení kořenového oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="3e01b-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="3e01b-273">Pokud má složka *EvenMorePages* v předchozím příkladu soubor Imports s `@namespace Another.Planet` (nebo soubor *Pages/MorePages/EvenMorePages/Page. cshtml* obsahuje `@namespace Another.Planet`), výsledek je zobrazen v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="3e01b-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="3e01b-274">Page</span><span class="sxs-lookup"><span data-stu-id="3e01b-274">Page</span></span>                                        | <span data-ttu-id="3e01b-275">Obor názvů</span><span class="sxs-lookup"><span data-stu-id="3e01b-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="3e01b-276">*Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e01b-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="3e01b-277">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e01b-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="3e01b-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e01b-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="3e01b-279">\@page</span><span class="sxs-lookup"><span data-stu-id="3e01b-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3e01b-280">The `@page` directive has different effects depending on the type of the file where it appears.</span><span class="sxs-lookup"><span data-stu-id="3e01b-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="3e01b-281">The directive:</span><span class="sxs-lookup"><span data-stu-id="3e01b-281">The directive:</span></span>

* <span data-ttu-id="3e01b-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span><span class="sxs-lookup"><span data-stu-id="3e01b-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="3e01b-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="3e01b-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="3e01b-284">Specifies that a Razor component should handle requests directly.</span><span class="sxs-lookup"><span data-stu-id="3e01b-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="3e01b-285">Další informace najdete v tématu <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="3e01b-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3e01b-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span><span class="sxs-lookup"><span data-stu-id="3e01b-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="3e01b-287">Další informace najdete v tématu <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="3e01b-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="3e01b-288">\@section</span><span class="sxs-lookup"><span data-stu-id="3e01b-288">\@section</span></span>

<span data-ttu-id="3e01b-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="3e01b-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="3e01b-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span><span class="sxs-lookup"><span data-stu-id="3e01b-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="3e01b-291">Další informace najdete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="3e01b-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="3e01b-292">\@using</span><span class="sxs-lookup"><span data-stu-id="3e01b-292">\@using</span></span>

<span data-ttu-id="3e01b-293">The `@using` directive adds the C# `using` directive to the generated view:</span><span class="sxs-lookup"><span data-stu-id="3e01b-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3e01b-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span><span class="sxs-lookup"><span data-stu-id="3e01b-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="3e01b-295">Directive attributes</span><span class="sxs-lookup"><span data-stu-id="3e01b-295">Directive attributes</span></span>

### <a name="attributes"></a><span data-ttu-id="3e01b-296">\@attributes</span><span class="sxs-lookup"><span data-stu-id="3e01b-296">\@attributes</span></span>

<span data-ttu-id="3e01b-297">*This scenario only applies to Razor components (.razor).*</span><span class="sxs-lookup"><span data-stu-id="3e01b-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="3e01b-298">`@attributes` allows a component to render non-declared attributes.</span><span class="sxs-lookup"><span data-stu-id="3e01b-298">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="3e01b-299">Další informace najdete v tématu <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="3e01b-299">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="3e01b-300">\@bind</span><span class="sxs-lookup"><span data-stu-id="3e01b-300">\@bind</span></span>

<span data-ttu-id="3e01b-301">*This scenario only applies to Razor components (.razor).*</span><span class="sxs-lookup"><span data-stu-id="3e01b-301">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="3e01b-302">Data binding in components is accomplished with the `@bind` attribute.</span><span class="sxs-lookup"><span data-stu-id="3e01b-302">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="3e01b-303">Další informace najdete v tématu <xref:blazor/components#data-binding>.</span><span class="sxs-lookup"><span data-stu-id="3e01b-303">For more information, see <xref:blazor/components#data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="3e01b-304">\@on{event}</span><span class="sxs-lookup"><span data-stu-id="3e01b-304">\@on{event}</span></span>

<span data-ttu-id="3e01b-305">*This scenario only applies to Razor components (.razor).*</span><span class="sxs-lookup"><span data-stu-id="3e01b-305">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="3e01b-306">Razor provides event handling features for components.</span><span class="sxs-lookup"><span data-stu-id="3e01b-306">Razor provides event handling features for components.</span></span> <span data-ttu-id="3e01b-307">Další informace najdete v tématu <xref:blazor/components#event-handling>.</span><span class="sxs-lookup"><span data-stu-id="3e01b-307">For more information, see <xref:blazor/components#event-handling>.</span></span>

### <a name="key"></a><span data-ttu-id="3e01b-308">\@key</span><span class="sxs-lookup"><span data-stu-id="3e01b-308">\@key</span></span>

<span data-ttu-id="3e01b-309">*This scenario only applies to Razor components (.razor).*</span><span class="sxs-lookup"><span data-stu-id="3e01b-309">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="3e01b-310">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span><span class="sxs-lookup"><span data-stu-id="3e01b-310">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="3e01b-311">Další informace najdete v tématu <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="3e01b-311">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="3e01b-312">\@ref</span><span class="sxs-lookup"><span data-stu-id="3e01b-312">\@ref</span></span>

<span data-ttu-id="3e01b-313">*This scenario only applies to Razor components (.razor).*</span><span class="sxs-lookup"><span data-stu-id="3e01b-313">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="3e01b-314">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span><span class="sxs-lookup"><span data-stu-id="3e01b-314">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="3e01b-315">Další informace najdete v tématu <xref:blazor/components#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="3e01b-315">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="3e01b-316">\@typeparam</span><span class="sxs-lookup"><span data-stu-id="3e01b-316">\@typeparam</span></span>

<span data-ttu-id="3e01b-317">*This scenario only applies to Razor components (.razor).*</span><span class="sxs-lookup"><span data-stu-id="3e01b-317">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="3e01b-318">The `@typeparam` directive declares a generic type parameter for the generated component class.</span><span class="sxs-lookup"><span data-stu-id="3e01b-318">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="3e01b-319">Další informace najdete v tématu <xref:blazor/components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="3e01b-319">For more information, see <xref:blazor/components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="3e01b-320">Templated Razor delegates</span><span class="sxs-lookup"><span data-stu-id="3e01b-320">Templated Razor delegates</span></span>

<span data-ttu-id="3e01b-321">Razor templates allow you to define a UI snippet with the following format:</span><span class="sxs-lookup"><span data-stu-id="3e01b-321">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="3e01b-322">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span><span class="sxs-lookup"><span data-stu-id="3e01b-322">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="3e01b-323">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span><span class="sxs-lookup"><span data-stu-id="3e01b-323">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="3e01b-324">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span><span class="sxs-lookup"><span data-stu-id="3e01b-324">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="3e01b-325">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span><span class="sxs-lookup"><span data-stu-id="3e01b-325">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="3e01b-326">The template is rendered with `pets` supplied by a `foreach` statement:</span><span class="sxs-lookup"><span data-stu-id="3e01b-326">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="3e01b-327">Rendered output:</span><span class="sxs-lookup"><span data-stu-id="3e01b-327">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="3e01b-328">You can also supply an inline Razor template as an argument to a method.</span><span class="sxs-lookup"><span data-stu-id="3e01b-328">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="3e01b-329">In the following example, the `Repeat` method receives a Razor template.</span><span class="sxs-lookup"><span data-stu-id="3e01b-329">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="3e01b-330">The method uses the template to produce HTML content with repeats of items supplied from a list:</span><span class="sxs-lookup"><span data-stu-id="3e01b-330">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="3e01b-331">Using the list of pets from the prior example, the `Repeat` method is called with:</span><span class="sxs-lookup"><span data-stu-id="3e01b-331">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="3e01b-332"><xref:System.Collections.Generic.List%601> of `Pet`.</span><span class="sxs-lookup"><span data-stu-id="3e01b-332"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="3e01b-333">Number of times to repeat each pet.</span><span class="sxs-lookup"><span data-stu-id="3e01b-333">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="3e01b-334">Inline template to use for the list items of an unordered list.</span><span class="sxs-lookup"><span data-stu-id="3e01b-334">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="3e01b-335">Rendered output:</span><span class="sxs-lookup"><span data-stu-id="3e01b-335">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="3e01b-336">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="3e01b-336">Tag Helpers</span></span>

<span data-ttu-id="3e01b-337">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="3e01b-337">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="3e01b-338">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="3e01b-338">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="3e01b-339">Directive</span><span class="sxs-lookup"><span data-stu-id="3e01b-339">Directive</span></span> | <span data-ttu-id="3e01b-340">Funkce</span><span class="sxs-lookup"><span data-stu-id="3e01b-340">Function</span></span> |
| --------- | -------- |
| [@addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="3e01b-341">Makes Tag Helpers available to a view.</span><span class="sxs-lookup"><span data-stu-id="3e01b-341">Makes Tag Helpers available to a view.</span></span> |
| [@removeTagHelper](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="3e01b-342">Removes Tag Helpers previously added from a view.</span><span class="sxs-lookup"><span data-stu-id="3e01b-342">Removes Tag Helpers previously added from a view.</span></span> |
| [@tagHelperPrefix](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="3e01b-343">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span><span class="sxs-lookup"><span data-stu-id="3e01b-343">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="3e01b-344">Razor reserved keywords</span><span class="sxs-lookup"><span data-stu-id="3e01b-344">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="3e01b-345">Razor keywords</span><span class="sxs-lookup"><span data-stu-id="3e01b-345">Razor keywords</span></span>

* <span data-ttu-id="3e01b-346">page (Requires ASP.NET Core 2.1 or later)</span><span class="sxs-lookup"><span data-stu-id="3e01b-346">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="3e01b-347">– obor názvů</span><span class="sxs-lookup"><span data-stu-id="3e01b-347">namespace</span></span>
* <span data-ttu-id="3e01b-348">– funkce</span><span class="sxs-lookup"><span data-stu-id="3e01b-348">functions</span></span>
* <span data-ttu-id="3e01b-349">inherits</span><span class="sxs-lookup"><span data-stu-id="3e01b-349">inherits</span></span>
* <span data-ttu-id="3e01b-350">model</span><span class="sxs-lookup"><span data-stu-id="3e01b-350">model</span></span>
* <span data-ttu-id="3e01b-351">section</span><span class="sxs-lookup"><span data-stu-id="3e01b-351">section</span></span>
* <span data-ttu-id="3e01b-352">helper (Not currently supported by ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="3e01b-352">helper (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="3e01b-353">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span><span class="sxs-lookup"><span data-stu-id="3e01b-353">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="3e01b-354">C# Razor keywords</span><span class="sxs-lookup"><span data-stu-id="3e01b-354">C# Razor keywords</span></span>

* <span data-ttu-id="3e01b-355">case</span><span class="sxs-lookup"><span data-stu-id="3e01b-355">case</span></span>
* <span data-ttu-id="3e01b-356">do</span><span class="sxs-lookup"><span data-stu-id="3e01b-356">do</span></span>
* <span data-ttu-id="3e01b-357">default</span><span class="sxs-lookup"><span data-stu-id="3e01b-357">default</span></span>
* <span data-ttu-id="3e01b-358">pro</span><span class="sxs-lookup"><span data-stu-id="3e01b-358">for</span></span>
* <span data-ttu-id="3e01b-359">foreach</span><span class="sxs-lookup"><span data-stu-id="3e01b-359">foreach</span></span>
* <span data-ttu-id="3e01b-360">if</span><span class="sxs-lookup"><span data-stu-id="3e01b-360">if</span></span>
* <span data-ttu-id="3e01b-361">else</span><span class="sxs-lookup"><span data-stu-id="3e01b-361">else</span></span>
* <span data-ttu-id="3e01b-362">lock</span><span class="sxs-lookup"><span data-stu-id="3e01b-362">lock</span></span>
* <span data-ttu-id="3e01b-363">– přepínač</span><span class="sxs-lookup"><span data-stu-id="3e01b-363">switch</span></span>
* <span data-ttu-id="3e01b-364">try</span><span class="sxs-lookup"><span data-stu-id="3e01b-364">try</span></span>
* <span data-ttu-id="3e01b-365">catch</span><span class="sxs-lookup"><span data-stu-id="3e01b-365">catch</span></span>
* <span data-ttu-id="3e01b-366">finally</span><span class="sxs-lookup"><span data-stu-id="3e01b-366">finally</span></span>
* <span data-ttu-id="3e01b-367">používání</span><span class="sxs-lookup"><span data-stu-id="3e01b-367">using</span></span>
* <span data-ttu-id="3e01b-368">while</span><span class="sxs-lookup"><span data-stu-id="3e01b-368">while</span></span>

<span data-ttu-id="3e01b-369">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span><span class="sxs-lookup"><span data-stu-id="3e01b-369">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="3e01b-370">The first `@` escapes the Razor parser.</span><span class="sxs-lookup"><span data-stu-id="3e01b-370">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="3e01b-371">The second `@` escapes the C# parser.</span><span class="sxs-lookup"><span data-stu-id="3e01b-371">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="3e01b-372">Reserved keywords not used by Razor</span><span class="sxs-lookup"><span data-stu-id="3e01b-372">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="3e01b-373">třída</span><span class="sxs-lookup"><span data-stu-id="3e01b-373">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="3e01b-374">Inspect the Razor C# class generated for a view</span><span class="sxs-lookup"><span data-stu-id="3e01b-374">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="3e01b-375">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span><span class="sxs-lookup"><span data-stu-id="3e01b-375">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="3e01b-376">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span><span class="sxs-lookup"><span data-stu-id="3e01b-376">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="3e01b-377">The directory structure within the *Razor* directory mirrors the project's directory structure.</span><span class="sxs-lookup"><span data-stu-id="3e01b-377">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="3e01b-378">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span><span class="sxs-lookup"><span data-stu-id="3e01b-378">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="3e01b-379">**Areas/**</span><span class="sxs-lookup"><span data-stu-id="3e01b-379">**Areas/**</span></span>
  * <span data-ttu-id="3e01b-380">**Admin/**</span><span class="sxs-lookup"><span data-stu-id="3e01b-380">**Admin/**</span></span>
    * <span data-ttu-id="3e01b-381">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="3e01b-381">**Pages/**</span></span>
      * <span data-ttu-id="3e01b-382">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e01b-382">*Index.cshtml*</span></span>
      * <span data-ttu-id="3e01b-383">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3e01b-383">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="3e01b-384">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="3e01b-384">**Pages/**</span></span>
  * <span data-ttu-id="3e01b-385">**Shared/**</span><span class="sxs-lookup"><span data-stu-id="3e01b-385">**Shared/**</span></span>
    * <span data-ttu-id="3e01b-386">*_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e01b-386">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="3e01b-387">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e01b-387">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="3e01b-388">*_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e01b-388">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="3e01b-389">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e01b-389">*Index.cshtml*</span></span>
  * <span data-ttu-id="3e01b-390">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3e01b-390">*Index.cshtml.cs*</span></span>

<span data-ttu-id="3e01b-391">Building the project in *Debug* configuration yields the following *obj* directory:</span><span class="sxs-lookup"><span data-stu-id="3e01b-391">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="3e01b-392">**obj/**</span><span class="sxs-lookup"><span data-stu-id="3e01b-392">**obj/**</span></span>
  * <span data-ttu-id="3e01b-393">**Debug/**</span><span class="sxs-lookup"><span data-stu-id="3e01b-393">**Debug/**</span></span>
    * <span data-ttu-id="3e01b-394">**netcoreapp2.1/**</span><span class="sxs-lookup"><span data-stu-id="3e01b-394">**netcoreapp2.1/**</span></span>
      * <span data-ttu-id="3e01b-395">**Razor/**</span><span class="sxs-lookup"><span data-stu-id="3e01b-395">**Razor/**</span></span>
        * <span data-ttu-id="3e01b-396">**Areas/**</span><span class="sxs-lookup"><span data-stu-id="3e01b-396">**Areas/**</span></span>
          * <span data-ttu-id="3e01b-397">**Admin/**</span><span class="sxs-lookup"><span data-stu-id="3e01b-397">**Admin/**</span></span>
            * <span data-ttu-id="3e01b-398">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="3e01b-398">**Pages/**</span></span>
              * <span data-ttu-id="3e01b-399">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3e01b-399">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="3e01b-400">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="3e01b-400">**Pages/**</span></span>
          * <span data-ttu-id="3e01b-401">**Shared/**</span><span class="sxs-lookup"><span data-stu-id="3e01b-401">**Shared/**</span></span>
            * <span data-ttu-id="3e01b-402">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3e01b-402">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="3e01b-403">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3e01b-403">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="3e01b-404">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3e01b-404">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="3e01b-405">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3e01b-405">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="3e01b-406">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="3e01b-406">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="3e01b-407">Add the following class to the ASP.NET Core MVC project:</span><span class="sxs-lookup"><span data-stu-id="3e01b-407">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="3e01b-408">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span><span class="sxs-lookup"><span data-stu-id="3e01b-408">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="3e01b-409">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span><span class="sxs-lookup"><span data-stu-id="3e01b-409">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="3e01b-410">When program execution stops at the breakpoint, view the value of `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="3e01b-410">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Text Visualizer view of generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="3e01b-412">View lookups and case sensitivity</span><span class="sxs-lookup"><span data-stu-id="3e01b-412">View lookups and case sensitivity</span></span>

<span data-ttu-id="3e01b-413">The Razor view engine performs case-sensitive lookups for views.</span><span class="sxs-lookup"><span data-stu-id="3e01b-413">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="3e01b-414">However, the actual lookup is determined by the underlying file system:</span><span class="sxs-lookup"><span data-stu-id="3e01b-414">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="3e01b-415">File based source:</span><span class="sxs-lookup"><span data-stu-id="3e01b-415">File based source:</span></span>
  * <span data-ttu-id="3e01b-416">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span><span class="sxs-lookup"><span data-stu-id="3e01b-416">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="3e01b-417">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span><span class="sxs-lookup"><span data-stu-id="3e01b-417">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="3e01b-418">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span><span class="sxs-lookup"><span data-stu-id="3e01b-418">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="3e01b-419">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3e01b-419">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="3e01b-420">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span><span class="sxs-lookup"><span data-stu-id="3e01b-420">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="3e01b-421">The behavior is identical to physical file provider's behavior on Windows.</span><span class="sxs-lookup"><span data-stu-id="3e01b-421">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="3e01b-422">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span><span class="sxs-lookup"><span data-stu-id="3e01b-422">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="3e01b-423">Developers are encouraged to match the casing of file and directory names to the casing of:</span><span class="sxs-lookup"><span data-stu-id="3e01b-423">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="3e01b-424">Area, controller, and action names.</span><span class="sxs-lookup"><span data-stu-id="3e01b-424">Area, controller, and action names.</span></span>
* <span data-ttu-id="3e01b-425">Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3e01b-425">Razor Pages.</span></span>

<span data-ttu-id="3e01b-426">Matching case ensures the deployments find their views regardless of the underlying file system.</span><span class="sxs-lookup"><span data-stu-id="3e01b-426">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>
