---
title: Referenční příručka syntaxe Razor pro ASP.NET Core
author: rick-anderson
description: Další informace o syntaxi Razor kód pro vložení do webových stránek kód založený na serveru.
ms.author: riande
ms.date: 02/12/2020
uid: mvc/views/razor
ms.openlocfilehash: e9d2e42ba3c36bc1661739f3b105ec8efe03de48
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658716"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="38a46-103">Referenční příručka syntaxe Razor pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="38a46-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="38a46-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)a [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="38a46-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="38a46-105">Razor je syntaxe značek pro vkládání do webových stránek kód založený na serveru.</span><span class="sxs-lookup"><span data-stu-id="38a46-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="38a46-106">Syntaxe Razor se skládá z kódu Razor C#a HTML.</span><span class="sxs-lookup"><span data-stu-id="38a46-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="38a46-107">Soubory obsahující Razor mají obecně příponu *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="38a46-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="38a46-108">Razor se nachází také v souborech [součástí Razor](xref:blazor/components) ( *. Razor*).</span><span class="sxs-lookup"><span data-stu-id="38a46-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="38a46-109">Vykreslování protokolu HTML</span><span class="sxs-lookup"><span data-stu-id="38a46-109">Rendering HTML</span></span>

<span data-ttu-id="38a46-110">Výchozí jazyk Razor je ve formátu HTML.</span><span class="sxs-lookup"><span data-stu-id="38a46-110">The default Razor language is HTML.</span></span> <span data-ttu-id="38a46-111">Vykreslování HTML z kód Razor se nijak neliší od vykreslování protokolu HTML ze souboru HTML.</span><span class="sxs-lookup"><span data-stu-id="38a46-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="38a46-112">Kód HTML v souboru *. cshtml* Razor je vykreslen serverem beze změny.</span><span class="sxs-lookup"><span data-stu-id="38a46-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="38a46-113">Syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="38a46-113">Razor syntax</span></span>

<span data-ttu-id="38a46-114">Razor podporuje C# a používá symbol `@` k přechodu z formátu HTML na C#.</span><span class="sxs-lookup"><span data-stu-id="38a46-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="38a46-115">Vyhodnotí Razor C# výrazy a vykreslí je ve výstupu protokolu HTML.</span><span class="sxs-lookup"><span data-stu-id="38a46-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="38a46-116">Je-li symbol `@` následovaný [rezervovaným klíčovým slovem Razor](#razor-reserved-keywords), přejde do kódu specifického pro Razor.</span><span class="sxs-lookup"><span data-stu-id="38a46-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="38a46-117">V opačném případě bude přecházet do prostého C#.</span><span class="sxs-lookup"><span data-stu-id="38a46-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="38a46-118">Chcete-li v kódu Razor použít řídicí znak `@`, použijte druhý symbol `@`:</span><span class="sxs-lookup"><span data-stu-id="38a46-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="38a46-119">Kód je vykreslen ve formátu HTML s jedním symbolem `@`:</span><span class="sxs-lookup"><span data-stu-id="38a46-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="38a46-120">Atributy HTML a obsah obsahující e-mailové adresy nepovažují symbol `@` jako znak přechodu.</span><span class="sxs-lookup"><span data-stu-id="38a46-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="38a46-121">E-mailové adresy v následujícím příkladu jsou zůstanou podle analýzy Razor:</span><span class="sxs-lookup"><span data-stu-id="38a46-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="38a46-122">Implicitní výrazy Razor</span><span class="sxs-lookup"><span data-stu-id="38a46-122">Implicit Razor expressions</span></span>

<span data-ttu-id="38a46-123">Implicitní výrazy Razor začínají na C# `@` následovaný kódem:</span><span class="sxs-lookup"><span data-stu-id="38a46-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="38a46-124">S výjimkou klíčového C# slova `await` nesmí implicitní výrazy obsahovat mezery.</span><span class="sxs-lookup"><span data-stu-id="38a46-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="38a46-125">Pokud C# příkaz má vymazat koncové, může být intermingled mezery:</span><span class="sxs-lookup"><span data-stu-id="38a46-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="38a46-126">Implicitní výrazy **nemůžou** obsahovat C# obecné typy, protože znaky uvnitř závorek (`<>`) se interpretují jako značky HTML.</span><span class="sxs-lookup"><span data-stu-id="38a46-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="38a46-127">Následující kód **není platný:**</span><span class="sxs-lookup"><span data-stu-id="38a46-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="38a46-128">Předcházející kód vygeneruje chybu kompilátoru podobně jako na jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="38a46-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="38a46-129">Prvek "int" není uzavřený.</span><span class="sxs-lookup"><span data-stu-id="38a46-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="38a46-130">Všechny elementy musí být buď samouzavírací nebo koncová značka.</span><span class="sxs-lookup"><span data-stu-id="38a46-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="38a46-131">Nelze převést skupinu metod 'GenericMethod' na nedelegující typ 'object'.</span><span class="sxs-lookup"><span data-stu-id="38a46-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="38a46-132">Chtěli jste vyvolat metodu? "</span><span class="sxs-lookup"><span data-stu-id="38a46-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="38a46-133">Volání obecných metod musí být zabalena do [explicitního výrazu Razor](#explicit-razor-expressions) nebo [bloku kódu Razor](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="38a46-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="38a46-134">Explicitní výrazy Razor</span><span class="sxs-lookup"><span data-stu-id="38a46-134">Explicit Razor expressions</span></span>

<span data-ttu-id="38a46-135">Explicitní výrazy Razor se skládají z `@` symbolu se vyváženými závorkami.</span><span class="sxs-lookup"><span data-stu-id="38a46-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="38a46-136">Pokud chcete zobrazit čas poslední týden, se používá následující kód Razor:</span><span class="sxs-lookup"><span data-stu-id="38a46-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="38a46-137">Veškerý obsah v `@()` závorkách se vyhodnotí a vykreslí do výstupu.</span><span class="sxs-lookup"><span data-stu-id="38a46-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="38a46-138">Obecně implicitní výrazů, je popsáno v předchozí části, nesmí obsahovat mezery.</span><span class="sxs-lookup"><span data-stu-id="38a46-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="38a46-139">V následujícím kódu není jeden týden odečtena od aktuální čas:</span><span class="sxs-lookup"><span data-stu-id="38a46-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="38a46-140">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="38a46-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="38a46-141">Explicitní výrazy můžete použít ke zřetězení text s výsledek výrazu:</span><span class="sxs-lookup"><span data-stu-id="38a46-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="38a46-142">Bez explicitního výrazu se `<p>Age@joe.Age</p>` považuje za e-mailovou adresu a vykreslí se `<p>Age@joe.Age</p>`.</span><span class="sxs-lookup"><span data-stu-id="38a46-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="38a46-143">Když je zapsán jako explicitní výraz, `<p>Age33</p>` je vykreslen.</span><span class="sxs-lookup"><span data-stu-id="38a46-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="38a46-144">Explicitní výrazy lze použít k vykreslení výstupu z obecných metod v souborech *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="38a46-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="38a46-145">Následující kód ukazuje, jak chcete-li zobrazit tato chyba dříve způsobené závorkami C# obecný.</span><span class="sxs-lookup"><span data-stu-id="38a46-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="38a46-146">Kód je zapsán jako explicitní výraz:</span><span class="sxs-lookup"><span data-stu-id="38a46-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="38a46-147">Výraz kódování</span><span class="sxs-lookup"><span data-stu-id="38a46-147">Expression encoding</span></span>

<span data-ttu-id="38a46-148">C#jsou výrazy, které vedou na řetězec kódovaný jazykem HTML.</span><span class="sxs-lookup"><span data-stu-id="38a46-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="38a46-149">C#výrazy, které se vyhodnotí jako `IHtmlContent`, se vykreslují přímo prostřednictvím `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="38a46-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="38a46-150">C#výrazy, které nejsou vyhodnoceny jako `IHtmlContent` jsou převedeny na řetězec `ToString` a kódovány před jejich vykreslením.</span><span class="sxs-lookup"><span data-stu-id="38a46-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="38a46-151">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="38a46-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="38a46-152">Kód HTML je zobrazená v prohlížeči jako:</span><span class="sxs-lookup"><span data-stu-id="38a46-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="38a46-153">`HtmlHelper.Raw` výstup není kódovaný, ale vykresluje se jako kód HTML.</span><span class="sxs-lookup"><span data-stu-id="38a46-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="38a46-154">Používání `HtmlHelper.Raw` v neupraveném uživatelském vstupu je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="38a46-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="38a46-155">Uživatelský vstup může obsahovat další zneužití nebo škodlivý jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="38a46-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="38a46-156">Sanitaci uživatelský vstup je obtížné.</span><span class="sxs-lookup"><span data-stu-id="38a46-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="38a46-157">Vyhněte se použití `HtmlHelper.Raw` s uživatelským vstupem.</span><span class="sxs-lookup"><span data-stu-id="38a46-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="38a46-158">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="38a46-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="38a46-159">Bloky kódu Razor</span><span class="sxs-lookup"><span data-stu-id="38a46-159">Razor code blocks</span></span>

<span data-ttu-id="38a46-160">Bloky kódu Razor začínají na `@` a jsou uzavřeny pomocí `{}`.</span><span class="sxs-lookup"><span data-stu-id="38a46-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="38a46-161">Na rozdíl od výrazy C# kód uvnitř bloků kódu není vykresleno.</span><span class="sxs-lookup"><span data-stu-id="38a46-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="38a46-162">Bloky kódu a výrazy v zobrazení sdílejí stejný obor a jsou definované v pořadí:</span><span class="sxs-lookup"><span data-stu-id="38a46-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="38a46-163">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="38a46-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38a46-164">V blocích kódu deklarujte [místní funkce](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) pomocí značek, které budou sloužit jako metody šablonování:</span><span class="sxs-lookup"><span data-stu-id="38a46-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="38a46-165">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="38a46-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="38a46-166">Implicitní přechody</span><span class="sxs-lookup"><span data-stu-id="38a46-166">Implicit transitions</span></span>

<span data-ttu-id="38a46-167">Je výchozí jazyk v bloku kódu C#, ale stránka Razor můžete přejít zpět na HTML:</span><span class="sxs-lookup"><span data-stu-id="38a46-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="38a46-168">Explicitní převod s oddělovači</span><span class="sxs-lookup"><span data-stu-id="38a46-168">Explicit delimited transition</span></span>

<span data-ttu-id="38a46-169">Chcete-li definovat dílčí oddíl bloku kódu, který by měl vykreslovat kód HTML, uzavřete znaky pro vykreslování pomocí značky `<text>` Razor:</span><span class="sxs-lookup"><span data-stu-id="38a46-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="38a46-170">Tuto metodu použijte k vykreslení HTML, který není obklopený značky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="38a46-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="38a46-171">Bez značky jazyka HTML nebo Razor dojde k chybě modulu runtime Razor.</span><span class="sxs-lookup"><span data-stu-id="38a46-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="38a46-172">Značka `<text>` je užitečná pro řízení prázdných znaků při vykreslování obsahu:</span><span class="sxs-lookup"><span data-stu-id="38a46-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="38a46-173">Vykreslí se jenom obsah mezi `<text>` značkou.</span><span class="sxs-lookup"><span data-stu-id="38a46-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="38a46-174">Žádný prázdný znak před nebo za `<text>` značka se zobrazí ve výstupu HTML.</span><span class="sxs-lookup"><span data-stu-id="38a46-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="38a46-175">Explicitní přechod řádku</span><span class="sxs-lookup"><span data-stu-id="38a46-175">Explicit line transition</span></span>

<span data-ttu-id="38a46-176">Chcete-li vykreslit zbytek celého řádku jako HTML v bloku kódu, použijte `@:` syntaxi:</span><span class="sxs-lookup"><span data-stu-id="38a46-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="38a46-177">Bez `@:` v kódu je vygenerována chyba modulu runtime Razor.</span><span class="sxs-lookup"><span data-stu-id="38a46-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="38a46-178">Nadbytečné `@` znaky v souboru Razor mohou způsobit chyby kompilátoru v příkazech později v bloku.</span><span class="sxs-lookup"><span data-stu-id="38a46-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="38a46-179">Tyto chyby kompilátoru může být obtížné pochopit, protože Skutečná chyba předchází oznámenou chybu.</span><span class="sxs-lookup"><span data-stu-id="38a46-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="38a46-180">K této chybě dochází po kombinování více implicitního/explicitního výrazů v jednom bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="38a46-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="38a46-181">Řídicí struktury</span><span class="sxs-lookup"><span data-stu-id="38a46-181">Control structures</span></span>

<span data-ttu-id="38a46-182">Řídicí struktury jsou rozšíření bloky kódu.</span><span class="sxs-lookup"><span data-stu-id="38a46-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="38a46-183">Všechny aspekty bloky kódu (Přechod na značky vložené C#) platí také pro následující struktury:</span><span class="sxs-lookup"><span data-stu-id="38a46-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="38a46-184">Podmíněné \@if, else if, else a \@Switch</span><span class="sxs-lookup"><span data-stu-id="38a46-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="38a46-185">`@if` ovládací prvky při spuštění kódu:</span><span class="sxs-lookup"><span data-stu-id="38a46-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="38a46-186">`else` a `else if` nevyžadují symbol `@`:</span><span class="sxs-lookup"><span data-stu-id="38a46-186">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="38a46-187">Následující kód ukazuje použití příkazu switch:</span><span class="sxs-lookup"><span data-stu-id="38a46-187">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="38a46-188">Opakování \@pro, \@foreach, \@while a \@</span><span class="sxs-lookup"><span data-stu-id="38a46-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="38a46-189">Bez vizuálního vzhledu HTML lze vykreslit s opakování ve smyčce řídicí příkazy.</span><span class="sxs-lookup"><span data-stu-id="38a46-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="38a46-190">Pokud chcete zobrazit seznam lidí:</span><span class="sxs-lookup"><span data-stu-id="38a46-190">To render a list of people:</span></span>

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

<span data-ttu-id="38a46-191">Podporují se následující příkazy opakování:</span><span class="sxs-lookup"><span data-stu-id="38a46-191">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="38a46-192">Složené \@using</span><span class="sxs-lookup"><span data-stu-id="38a46-192">Compound \@using</span></span>

<span data-ttu-id="38a46-193">V C#nástroji se k zajištění uvolnění objektu používá příkaz `using`.</span><span class="sxs-lookup"><span data-stu-id="38a46-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="38a46-194">V prostředí Razor stejný mechanismus slouží k vytvoření pomocné rutiny HTML, které obsahují další obsah.</span><span class="sxs-lookup"><span data-stu-id="38a46-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="38a46-195">V následujícím kódu pomocník HTML vykresluje značku `<form>` pomocí příkazu `@using`:</span><span class="sxs-lookup"><span data-stu-id="38a46-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="38a46-196">\@try, catch, finally</span><span class="sxs-lookup"><span data-stu-id="38a46-196">\@try, catch, finally</span></span>

<span data-ttu-id="38a46-197">Zpracování výjimek je podobný C#:</span><span class="sxs-lookup"><span data-stu-id="38a46-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="38a46-198">\@zámek</span><span class="sxs-lookup"><span data-stu-id="38a46-198">\@lock</span></span>

<span data-ttu-id="38a46-199">Razor má schopnost chránit kritické oddíly s příkazy uzamčení:</span><span class="sxs-lookup"><span data-stu-id="38a46-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="38a46-200">Komentáře</span><span class="sxs-lookup"><span data-stu-id="38a46-200">Comments</span></span>

<span data-ttu-id="38a46-201">Podporuje Razor C# a komentáře HTML:</span><span class="sxs-lookup"><span data-stu-id="38a46-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="38a46-202">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="38a46-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="38a46-203">Komentáře syntaxe Razor jsou odebrána serverem, než se zobrazí webová stránka.</span><span class="sxs-lookup"><span data-stu-id="38a46-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="38a46-204">Razor používá `@*  *@` k vymezení komentářů.</span><span class="sxs-lookup"><span data-stu-id="38a46-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="38a46-205">Následující kód je zakomentovaný, tak server nevykreslí žádné značky:</span><span class="sxs-lookup"><span data-stu-id="38a46-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="38a46-206">Direktivy</span><span class="sxs-lookup"><span data-stu-id="38a46-206">Directives</span></span>

<span data-ttu-id="38a46-207">Direktivy Razor jsou reprezentovány implicitními výrazy s vyhrazenými klíčovými slovy za symbolem `@`.</span><span class="sxs-lookup"><span data-stu-id="38a46-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="38a46-208">Direktivu obvykle mění způsob zobrazení je analyzován nebo jinou funkci povolí.</span><span class="sxs-lookup"><span data-stu-id="38a46-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="38a46-209">Vysvětlení způsobu, jakým Razor generuje kód pro zobrazení usnadňuje pochopení fungování direktivy.</span><span class="sxs-lookup"><span data-stu-id="38a46-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="38a46-210">Kód vygeneruje třídu podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="38a46-210">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="38a46-211">Později v tomto článku oddíl [kontroluje třídu Razor C# generovanou pro zobrazení](#inspect-the-razor-c-class-generated-for-a-view) vysvětluje, jak zobrazit tuto generovanou třídu.</span><span class="sxs-lookup"><span data-stu-id="38a46-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="38a46-212">atribut \@</span><span class="sxs-lookup"><span data-stu-id="38a46-212">\@attribute</span></span>

<span data-ttu-id="38a46-213">Direktiva `@attribute` přidá daný atribut třídě vygenerované stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="38a46-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="38a46-214">Následující příklad přidá atribut `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="38a46-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="38a46-215">kód \@</span><span class="sxs-lookup"><span data-stu-id="38a46-215">\@code</span></span>

<span data-ttu-id="38a46-216">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="38a46-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="38a46-217">`@code` blok umožňuje [komponentě Razor](xref:blazor/components) přidat C# členy (pole, vlastnosti a metody) do komponenty:</span><span class="sxs-lookup"><span data-stu-id="38a46-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="38a46-218">Pro součásti Razor je `@code` aliasem [`@functions`](#functions) a doporučuje se nad `@functions`.</span><span class="sxs-lookup"><span data-stu-id="38a46-218">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="38a46-219">Je přípustný více než jeden blok `@code`.</span><span class="sxs-lookup"><span data-stu-id="38a46-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="38a46-220">funkce \@</span><span class="sxs-lookup"><span data-stu-id="38a46-220">\@functions</span></span>

<span data-ttu-id="38a46-221">Direktiva `@functions` umožňuje přidat C# členy (pole, vlastnosti a metody) do generované třídy:</span><span class="sxs-lookup"><span data-stu-id="38a46-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38a46-222">V [součástech Razor](xref:blazor/components)použijte `@code` přes `@functions` k přidání C# členů.</span><span class="sxs-lookup"><span data-stu-id="38a46-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="38a46-223">Příklad:</span><span class="sxs-lookup"><span data-stu-id="38a46-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="38a46-224">Kód generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="38a46-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="38a46-225">Následující kód je vygenerovaný Razor C# třídy:</span><span class="sxs-lookup"><span data-stu-id="38a46-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38a46-226">`@functions` metody slouží jako metody šablonování, pokud mají značky:</span><span class="sxs-lookup"><span data-stu-id="38a46-226">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="38a46-227">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="38a46-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="38a46-228">\@implementuje</span><span class="sxs-lookup"><span data-stu-id="38a46-228">\@implements</span></span>

<span data-ttu-id="38a46-229">Direktiva `@implements` implementuje rozhraní pro generovanou třídu.</span><span class="sxs-lookup"><span data-stu-id="38a46-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="38a46-230">Následující příklad implementuje <xref:System.IDisposable?displayProperty=fullName> tak, aby mohla být volána metoda <xref:System.IDisposable.Dispose*>:</span><span class="sxs-lookup"><span data-stu-id="38a46-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

### <a name="inherits"></a><span data-ttu-id="38a46-231">\@dědí</span><span class="sxs-lookup"><span data-stu-id="38a46-231">\@inherits</span></span>

<span data-ttu-id="38a46-232">Direktiva `@inherits` poskytuje úplnou kontrolu nad třídou, kterou zobrazení dědí:</span><span class="sxs-lookup"><span data-stu-id="38a46-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="38a46-233">Následující kód je vlastní typ stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="38a46-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="38a46-234">`CustomText` se zobrazí v zobrazení:</span><span class="sxs-lookup"><span data-stu-id="38a46-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="38a46-235">Kód vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="38a46-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="38a46-236">`@model` a `@inherits` lze použít ve stejném zobrazení.</span><span class="sxs-lookup"><span data-stu-id="38a46-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="38a46-237">`@inherits` může být v souboru *_ViewImports. cshtml* , který zobrazení importuje:</span><span class="sxs-lookup"><span data-stu-id="38a46-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="38a46-238">Následující kód je příkladem zobrazení se silnými typy:</span><span class="sxs-lookup"><span data-stu-id="38a46-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="38a46-239">Pokud je v modelu předána možnostrick@contoso.com, zobrazení generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="38a46-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="38a46-240">\@vkládání</span><span class="sxs-lookup"><span data-stu-id="38a46-240">\@inject</span></span>

<span data-ttu-id="38a46-241">Direktiva `@inject` umožňuje, aby stránka Razor mohla vložit službu z [kontejneru služby](xref:fundamentals/dependency-injection) do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="38a46-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="38a46-242">Další informace najdete v tématu [vkládání závislostí do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="38a46-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="38a46-243">rozložení \@</span><span class="sxs-lookup"><span data-stu-id="38a46-243">\@layout</span></span>

<span data-ttu-id="38a46-244">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="38a46-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="38a46-245">Direktiva `@layout` určuje rozložení součásti Razor.</span><span class="sxs-lookup"><span data-stu-id="38a46-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="38a46-246">Komponenty rozložení se používají k zamezení Duplikace kódu a nekonzistenci.</span><span class="sxs-lookup"><span data-stu-id="38a46-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="38a46-247">Další informace naleznete v tématu <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="38a46-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="38a46-248">model \@</span><span class="sxs-lookup"><span data-stu-id="38a46-248">\@model</span></span>

<span data-ttu-id="38a46-249">*Tento scénář se vztahuje pouze na zobrazení MVC a Razor Pages (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="38a46-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="38a46-250">Direktiva `@model` určuje typ modelu předaného zobrazení nebo stránce:</span><span class="sxs-lookup"><span data-stu-id="38a46-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="38a46-251">V ASP.NET Core MVC nebo aplikace Razor Pages vytvořené pomocí individuálních uživatelských účtů, *zobrazení/účet/přihlášení. cshtml* obsahuje následující deklaraci modelu:</span><span class="sxs-lookup"><span data-stu-id="38a46-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="38a46-252">Třída vygenerovaná z `RazorPage<dynamic>`dědí:</span><span class="sxs-lookup"><span data-stu-id="38a46-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="38a46-253">Razor zpřístupňuje vlastnost `Model` pro přístup k modelu předanému zobrazení:</span><span class="sxs-lookup"><span data-stu-id="38a46-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="38a46-254">Direktiva `@model` určuje typ vlastnosti `Model`.</span><span class="sxs-lookup"><span data-stu-id="38a46-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="38a46-255">Direktiva určuje `T` v `RazorPage<T>`, ze které byla vygenerována třída, ze které je odvozeno zobrazení.</span><span class="sxs-lookup"><span data-stu-id="38a46-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="38a46-256">Pokud není zadána direktiva `@model`, je vlastnost `Model` typu `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="38a46-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="38a46-257">Další informace naleznete v tématu [modely silného typu a klíčové slovo @model](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="38a46-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="38a46-258">obor názvů \@</span><span class="sxs-lookup"><span data-stu-id="38a46-258">\@namespace</span></span>

<span data-ttu-id="38a46-259">Direktiva `@namespace`:</span><span class="sxs-lookup"><span data-stu-id="38a46-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="38a46-260">Nastaví obor názvů třídy vygenerované stránky Razor, zobrazení MVC nebo komponenty Razor.</span><span class="sxs-lookup"><span data-stu-id="38a46-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="38a46-261">Nastaví kořenové obory názvů pro třídy stránky, zobrazení nebo komponenty z nejbližšího souboru importu ve stromové struktuře, *_ViewImports. cshtml* (zobrazení nebo stránky) nebo *_Imports. Razor* (komponenty Razor).</span><span class="sxs-lookup"><span data-stu-id="38a46-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="38a46-262">Pro Razor Pages příklad zobrazený v následující tabulce:</span><span class="sxs-lookup"><span data-stu-id="38a46-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="38a46-263">Každá stránka importuje *stránky/_ViewImports. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="38a46-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="38a46-264">*Pages/_ViewImports. cshtml* obsahuje `@namespace Hello.World`.</span><span class="sxs-lookup"><span data-stu-id="38a46-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="38a46-265">Každá stránka má `Hello.World` jako kořen oboru názvů IT.</span><span class="sxs-lookup"><span data-stu-id="38a46-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="38a46-266">Stránka</span><span class="sxs-lookup"><span data-stu-id="38a46-266">Page</span></span>                                        | <span data-ttu-id="38a46-267">Obor názvů</span><span class="sxs-lookup"><span data-stu-id="38a46-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="38a46-268">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="38a46-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="38a46-269">*Pages/MorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="38a46-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="38a46-270">*Pages/MorePages/EvenMorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="38a46-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="38a46-271">Předchozí relace platí pro soubory importu používané s zobrazeními MVC a komponentami Razor.</span><span class="sxs-lookup"><span data-stu-id="38a46-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="38a46-272">Pokud má několik importových souborů direktivu `@namespace`, soubor, který je nejblíže stránce, zobrazení nebo komponentě v adresářovém stromu, slouží k nastavení kořenového oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="38a46-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="38a46-273">Pokud má složka *EvenMorePages* v předchozím příkladu soubor Imports s `@namespace Another.Planet` (nebo soubor *Pages/MorePages/EvenMorePages/Page. cshtml* obsahuje `@namespace Another.Planet`), výsledek je zobrazen v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="38a46-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="38a46-274">Stránka</span><span class="sxs-lookup"><span data-stu-id="38a46-274">Page</span></span>                                        | <span data-ttu-id="38a46-275">Obor názvů</span><span class="sxs-lookup"><span data-stu-id="38a46-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="38a46-276">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="38a46-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="38a46-277">*Pages/MorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="38a46-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="38a46-278">*Pages/MorePages/EvenMorePages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="38a46-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="38a46-279">Stránka \@</span><span class="sxs-lookup"><span data-stu-id="38a46-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38a46-280">Direktiva `@page` má různé účinky v závislosti na typu souboru, ve kterém se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="38a46-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="38a46-281">Direktiva:</span><span class="sxs-lookup"><span data-stu-id="38a46-281">The directive:</span></span>

* <span data-ttu-id="38a46-282">V v souboru *. cshtml* znamená, že soubor je stránka Razor.</span><span class="sxs-lookup"><span data-stu-id="38a46-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="38a46-283">Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes) a <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="38a46-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="38a46-284">Určuje, že komponenta Razor by měla zpracovávat požadavky přímo.</span><span class="sxs-lookup"><span data-stu-id="38a46-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="38a46-285">Další informace naleznete v tématu <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="38a46-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="38a46-286">Direktiva `@page` na prvním řádku souboru *. cshtml* znamená, že soubor je stránka Razor.</span><span class="sxs-lookup"><span data-stu-id="38a46-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="38a46-287">Další informace naleznete v tématu <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="38a46-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="38a46-288">\@oddíl</span><span class="sxs-lookup"><span data-stu-id="38a46-288">\@section</span></span>

<span data-ttu-id="38a46-289">*Tento scénář se vztahuje pouze na zobrazení MVC a Razor Pages (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="38a46-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="38a46-290">Direktiva `@section` se používá ve spojení s [MVC a Razor Pages rozloženími](xref:mvc/views/layout) k tomu, aby zobrazení nebo stránky vygenerovaly obsah v různých částech stránky HTML.</span><span class="sxs-lookup"><span data-stu-id="38a46-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="38a46-291">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="38a46-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="38a46-292">\@pomocí</span><span class="sxs-lookup"><span data-stu-id="38a46-292">\@using</span></span>

<span data-ttu-id="38a46-293">Direktiva `@using` přidá do C# generovaného zobrazení direktivu `using`:</span><span class="sxs-lookup"><span data-stu-id="38a46-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38a46-294">V [součástech Razor](xref:blazor/components)`@using` také určuje, které součásti jsou v oboru.</span><span class="sxs-lookup"><span data-stu-id="38a46-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="38a46-295">Atributy direktiv</span><span class="sxs-lookup"><span data-stu-id="38a46-295">Directive attributes</span></span>

### <a name="attributes"></a><span data-ttu-id="38a46-296">atributy \@</span><span class="sxs-lookup"><span data-stu-id="38a46-296">\@attributes</span></span>

<span data-ttu-id="38a46-297">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="38a46-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="38a46-298">`@attributes` umožňuje komponentě vykreslovat nedeklarované atributy.</span><span class="sxs-lookup"><span data-stu-id="38a46-298">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="38a46-299">Další informace naleznete v tématu <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="38a46-299">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="38a46-300">vazba \@</span><span class="sxs-lookup"><span data-stu-id="38a46-300">\@bind</span></span>

<span data-ttu-id="38a46-301">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="38a46-301">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="38a46-302">Datovou vazbu v součástech lze provést pomocí atributu `@bind`.</span><span class="sxs-lookup"><span data-stu-id="38a46-302">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="38a46-303">Další informace naleznete v tématu <xref:blazor/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="38a46-303">For more information, see <xref:blazor/data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="38a46-304">\@v události {EVENT}</span><span class="sxs-lookup"><span data-stu-id="38a46-304">\@on{EVENT}</span></span>

<span data-ttu-id="38a46-305">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="38a46-305">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="38a46-306">Razor poskytuje funkce pro zpracování událostí pro součásti.</span><span class="sxs-lookup"><span data-stu-id="38a46-306">Razor provides event handling features for components.</span></span> <span data-ttu-id="38a46-307">Další informace naleznete v tématu <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="38a46-307">For more information, see <xref:blazor/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a><span data-ttu-id="38a46-308">\@události {EVENT}:p reventDefault</span><span class="sxs-lookup"><span data-stu-id="38a46-308">\@on{EVENT}:preventDefault</span></span>

<span data-ttu-id="38a46-309">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="38a46-309">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="38a46-310">Zabrání výchozí akci pro událost.</span><span class="sxs-lookup"><span data-stu-id="38a46-310">Prevents the default action for the event.</span></span>

### <a name="oneventstoppropagation"></a><span data-ttu-id="38a46-311">\@v události {EVENT}: stopPropagation</span><span class="sxs-lookup"><span data-stu-id="38a46-311">\@on{EVENT}:stopPropagation</span></span>

<span data-ttu-id="38a46-312">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="38a46-312">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="38a46-313">Zastaví šíření události pro událost.</span><span class="sxs-lookup"><span data-stu-id="38a46-313">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a><span data-ttu-id="38a46-314">\@klíč</span><span class="sxs-lookup"><span data-stu-id="38a46-314">\@key</span></span>

<span data-ttu-id="38a46-315">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="38a46-315">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="38a46-316">Atribut direktivy `@key` způsobuje rozdíl mezi komponentami, které zajišťují uchovávání prvků nebo komponent na základě hodnoty klíče.</span><span class="sxs-lookup"><span data-stu-id="38a46-316">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="38a46-317">Další informace naleznete v tématu <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="38a46-317">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="38a46-318">\@odkaz</span><span class="sxs-lookup"><span data-stu-id="38a46-318">\@ref</span></span>

<span data-ttu-id="38a46-319">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="38a46-319">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="38a46-320">Odkazy na součásti (`@ref`) poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance.</span><span class="sxs-lookup"><span data-stu-id="38a46-320">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="38a46-321">Další informace naleznete v tématu <xref:blazor/components#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="38a46-321">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="38a46-322">\@typeparam</span><span class="sxs-lookup"><span data-stu-id="38a46-322">\@typeparam</span></span>

<span data-ttu-id="38a46-323">*Tento scénář platí pouze pro součásti Razor (. Razor).*</span><span class="sxs-lookup"><span data-stu-id="38a46-323">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="38a46-324">Direktiva `@typeparam` deklaruje parametr obecného typu pro generovanou třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="38a46-324">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="38a46-325">Další informace naleznete v tématu <xref:blazor/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="38a46-325">For more information, see <xref:blazor/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="38a46-326">Šablony Razor delegátů</span><span class="sxs-lookup"><span data-stu-id="38a46-326">Templated Razor delegates</span></span>

<span data-ttu-id="38a46-327">Šablony Razor umožňují definovat fragment uživatelského rozhraní v následujícím formátu:</span><span class="sxs-lookup"><span data-stu-id="38a46-327">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="38a46-328">Následující příklad ukazuje, jak zadat delegáta Razor s šablonou jako <xref:System.Func%602>.</span><span class="sxs-lookup"><span data-stu-id="38a46-328">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="38a46-329">[Dynamický typ](/dotnet/csharp/programming-guide/types/using-type-dynamic) je zadán pro parametr metody, kterou delegát zapouzdřuje.</span><span class="sxs-lookup"><span data-stu-id="38a46-329">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="38a46-330">[Typ objektu](/dotnet/csharp/language-reference/keywords/object) je zadán jako návratová hodnota delegáta.</span><span class="sxs-lookup"><span data-stu-id="38a46-330">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="38a46-331">Šablona se používá s <xref:System.Collections.Generic.List%601> `Pet`, která má vlastnost `Name`.</span><span class="sxs-lookup"><span data-stu-id="38a46-331">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="38a46-332">Šablona je vykreslena pomocí `pets` dodaného příkazem `foreach`:</span><span class="sxs-lookup"><span data-stu-id="38a46-332">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="38a46-333">Vykresleného výstupu:</span><span class="sxs-lookup"><span data-stu-id="38a46-333">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="38a46-334">Šablony Razor vložené lze také zadat jako argument pro metodu.</span><span class="sxs-lookup"><span data-stu-id="38a46-334">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="38a46-335">V následujícím příkladu metoda `Repeat` obdrží šablonu Razor.</span><span class="sxs-lookup"><span data-stu-id="38a46-335">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="38a46-336">Metoda používá šablony k vytvoření HTML obsah s opakování zadaný ze seznamu položek:</span><span class="sxs-lookup"><span data-stu-id="38a46-336">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="38a46-337">Pomocí seznamu domácích zvířat z předchozího příkladu je metoda `Repeat` volána s:</span><span class="sxs-lookup"><span data-stu-id="38a46-337">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="38a46-338"><xref:System.Collections.Generic.List%601> `Pet`.</span><span class="sxs-lookup"><span data-stu-id="38a46-338"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="38a46-339">Počet opakování každou mazlíčků.</span><span class="sxs-lookup"><span data-stu-id="38a46-339">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="38a46-340">Vložená šablona použitá pro položky seznamu neuspořádaný seznam.</span><span class="sxs-lookup"><span data-stu-id="38a46-340">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="38a46-341">Vykresleného výstupu:</span><span class="sxs-lookup"><span data-stu-id="38a46-341">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="38a46-342">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="38a46-342">Tag Helpers</span></span>

<span data-ttu-id="38a46-343">*Tento scénář se vztahuje pouze na zobrazení MVC a Razor Pages (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="38a46-343">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="38a46-344">Existují tři direktivy, které se týkají [pomocníků značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="38a46-344">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="38a46-345">– Direktiva</span><span class="sxs-lookup"><span data-stu-id="38a46-345">Directive</span></span> | <span data-ttu-id="38a46-346">Funkce</span><span class="sxs-lookup"><span data-stu-id="38a46-346">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="38a46-347">Zpřístupní pomocných rutin značek k zobrazení.</span><span class="sxs-lookup"><span data-stu-id="38a46-347">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="38a46-348">Odebere ze zobrazení přidali dříve pomocných rutin značek.</span><span class="sxs-lookup"><span data-stu-id="38a46-348">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="38a46-349">Určuje předponu značky k povolení podpory pomocné rutiny značky a aby explicitní použití pomocné rutiny značky.</span><span class="sxs-lookup"><span data-stu-id="38a46-349">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="38a46-350">Razor vyhrazená klíčová slova</span><span class="sxs-lookup"><span data-stu-id="38a46-350">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="38a46-351">Klíčová slova Razor</span><span class="sxs-lookup"><span data-stu-id="38a46-351">Razor keywords</span></span>

* <span data-ttu-id="38a46-352">Page (vyžaduje ASP.NET Core 2,1 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="38a46-352">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="38a46-353">Obor názvů</span><span class="sxs-lookup"><span data-stu-id="38a46-353">namespace</span></span>
* <span data-ttu-id="38a46-354">funkce</span><span class="sxs-lookup"><span data-stu-id="38a46-354">functions</span></span>
* <span data-ttu-id="38a46-355">Dědí</span><span class="sxs-lookup"><span data-stu-id="38a46-355">inherits</span></span>
* <span data-ttu-id="38a46-356">model</span><span class="sxs-lookup"><span data-stu-id="38a46-356">model</span></span>
* <span data-ttu-id="38a46-357">section</span><span class="sxs-lookup"><span data-stu-id="38a46-357">section</span></span>
* <span data-ttu-id="38a46-358">pomocné rutiny (aktuálně se nepodporuje ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="38a46-358">helper (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="38a46-359">Klíčová slova Razor jsou uvozená pomocí `@(Razor Keyword)` (například `@(functions)`).</span><span class="sxs-lookup"><span data-stu-id="38a46-359">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="38a46-360">C#Klíčová slova Razor</span><span class="sxs-lookup"><span data-stu-id="38a46-360">C# Razor keywords</span></span>

* <span data-ttu-id="38a46-361">case</span><span class="sxs-lookup"><span data-stu-id="38a46-361">case</span></span>
* <span data-ttu-id="38a46-362">do</span><span class="sxs-lookup"><span data-stu-id="38a46-362">do</span></span>
* <span data-ttu-id="38a46-363">default</span><span class="sxs-lookup"><span data-stu-id="38a46-363">default</span></span>
* <span data-ttu-id="38a46-364">pro</span><span class="sxs-lookup"><span data-stu-id="38a46-364">for</span></span>
* <span data-ttu-id="38a46-365">foreach</span><span class="sxs-lookup"><span data-stu-id="38a46-365">foreach</span></span>
* <span data-ttu-id="38a46-366">if</span><span class="sxs-lookup"><span data-stu-id="38a46-366">if</span></span>
* <span data-ttu-id="38a46-367">else</span><span class="sxs-lookup"><span data-stu-id="38a46-367">else</span></span>
* <span data-ttu-id="38a46-368">Zámek</span><span class="sxs-lookup"><span data-stu-id="38a46-368">lock</span></span>
* <span data-ttu-id="38a46-369">– přepínač</span><span class="sxs-lookup"><span data-stu-id="38a46-369">switch</span></span>
* <span data-ttu-id="38a46-370">vyzkoušení</span><span class="sxs-lookup"><span data-stu-id="38a46-370">try</span></span>
* <span data-ttu-id="38a46-371">catch</span><span class="sxs-lookup"><span data-stu-id="38a46-371">catch</span></span>
* <span data-ttu-id="38a46-372">finally</span><span class="sxs-lookup"><span data-stu-id="38a46-372">finally</span></span>
* <span data-ttu-id="38a46-373">používání</span><span class="sxs-lookup"><span data-stu-id="38a46-373">using</span></span>
* <span data-ttu-id="38a46-374">while</span><span class="sxs-lookup"><span data-stu-id="38a46-374">while</span></span>

<span data-ttu-id="38a46-375">C#Klíčová slova Razor musí mít dvakrát uvození řídicím znakem `@(@C# Razor Keyword)` (například `@(@case)`).</span><span class="sxs-lookup"><span data-stu-id="38a46-375">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="38a46-376">První `@` řídí analyzátor Razor.</span><span class="sxs-lookup"><span data-stu-id="38a46-376">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="38a46-377">Druhý `@` řídí C# analyzátor.</span><span class="sxs-lookup"><span data-stu-id="38a46-377">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="38a46-378">Vyhrazená klíčová slova nepoužívá Razor</span><span class="sxs-lookup"><span data-stu-id="38a46-378">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="38a46-379">Třída</span><span class="sxs-lookup"><span data-stu-id="38a46-379">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="38a46-380">Zkontrolovat syntaxi Razor C# třída vygenerovaná pro zobrazení</span><span class="sxs-lookup"><span data-stu-id="38a46-380">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="38a46-381">V .NET Core SDK 2,1 nebo novější sadě [SDK Razor](xref:razor-pages/sdk) zpracovává kompilaci souborů Razor.</span><span class="sxs-lookup"><span data-stu-id="38a46-381">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="38a46-382">Při sestavování projektu sada Razor SDK generuje v kořenu projektu *build_configuration > </< target_framework_moniker >* adresář.</span><span class="sxs-lookup"><span data-stu-id="38a46-382">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="38a46-383">Adresářová struktura v adresáři *Razor* zrcadlí strukturu adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="38a46-383">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="38a46-384">Vezměte v úvahu následující adresářovou strukturu v projektu aplikace ASP.NET Core 2.1 Razor Pages cílí na .NET Core 2.1:</span><span class="sxs-lookup"><span data-stu-id="38a46-384">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="38a46-385">**Místa**</span><span class="sxs-lookup"><span data-stu-id="38a46-385">**Areas/**</span></span>
  * <span data-ttu-id="38a46-386">**Správ**</span><span class="sxs-lookup"><span data-stu-id="38a46-386">**Admin/**</span></span>
    * <span data-ttu-id="38a46-387">**Stránky**</span><span class="sxs-lookup"><span data-stu-id="38a46-387">**Pages/**</span></span>
      * <span data-ttu-id="38a46-388">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="38a46-388">*Index.cshtml*</span></span>
      * <span data-ttu-id="38a46-389">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="38a46-389">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="38a46-390">**Stránky**</span><span class="sxs-lookup"><span data-stu-id="38a46-390">**Pages/**</span></span>
  * <span data-ttu-id="38a46-391">**Sdíleného**</span><span class="sxs-lookup"><span data-stu-id="38a46-391">**Shared/**</span></span>
    * <span data-ttu-id="38a46-392">*_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="38a46-392">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="38a46-393">*_ViewImports. cshtml*</span><span class="sxs-lookup"><span data-stu-id="38a46-393">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="38a46-394">*_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="38a46-394">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="38a46-395">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="38a46-395">*Index.cshtml*</span></span>
  * <span data-ttu-id="38a46-396">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="38a46-396">*Index.cshtml.cs*</span></span>

<span data-ttu-id="38a46-397">Sestavení projektu v konfiguraci *ladění* má za následek následující adresář *obj* :</span><span class="sxs-lookup"><span data-stu-id="38a46-397">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="38a46-398">**objektu**</span><span class="sxs-lookup"><span data-stu-id="38a46-398">**obj/**</span></span>
  * <span data-ttu-id="38a46-399">**Ladí**</span><span class="sxs-lookup"><span data-stu-id="38a46-399">**Debug/**</span></span>
    * <span data-ttu-id="38a46-400">**netcoreapp 2.1/**</span><span class="sxs-lookup"><span data-stu-id="38a46-400">**netcoreapp2.1/**</span></span>
      * <span data-ttu-id="38a46-401">**Syntaxi**</span><span class="sxs-lookup"><span data-stu-id="38a46-401">**Razor/**</span></span>
        * <span data-ttu-id="38a46-402">**Místa**</span><span class="sxs-lookup"><span data-stu-id="38a46-402">**Areas/**</span></span>
          * <span data-ttu-id="38a46-403">**Správ**</span><span class="sxs-lookup"><span data-stu-id="38a46-403">**Admin/**</span></span>
            * <span data-ttu-id="38a46-404">**Stránky**</span><span class="sxs-lookup"><span data-stu-id="38a46-404">**Pages/**</span></span>
              * <span data-ttu-id="38a46-405">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="38a46-405">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="38a46-406">**Stránky**</span><span class="sxs-lookup"><span data-stu-id="38a46-406">**Pages/**</span></span>
          * <span data-ttu-id="38a46-407">**Sdíleného**</span><span class="sxs-lookup"><span data-stu-id="38a46-407">**Shared/**</span></span>
            * <span data-ttu-id="38a46-408">*_Layout. g. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="38a46-408">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="38a46-409">*_ViewImports. g. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="38a46-409">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="38a46-410">*_ViewStart. g. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="38a46-410">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="38a46-411">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="38a46-411">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="38a46-412">Chcete-li zobrazit vygenerovanou třídu pro *pages/index. cshtml*, otevřete *obj/Debug/Netcoreapp 2.1/Razor/pages/index. g. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="38a46-412">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="38a46-413">Přidejte následující třídu do projektu ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="38a46-413">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="38a46-414">V `Startup.ConfigureServices`přepište `RazorTemplateEngine` přidaných pomocí MVC pomocí třídy `CustomTemplateEngine`:</span><span class="sxs-lookup"><span data-stu-id="38a46-414">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="38a46-415">Nastavte zarážku na `return csharpDocument;` příkazu `CustomTemplateEngine`.</span><span class="sxs-lookup"><span data-stu-id="38a46-415">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="38a46-416">Když se provádění programu zastaví na zarážce, zobrazí se hodnota `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="38a46-416">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Text Visualizer zobrazení generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="38a46-418">Zobrazení vyhledávání a rozlišování velikosti písmen</span><span class="sxs-lookup"><span data-stu-id="38a46-418">View lookups and case sensitivity</span></span>

<span data-ttu-id="38a46-419">Zobrazovací modul Razor provádí velká a malá písmena vyhledávání pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="38a46-419">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="38a46-420">Nicméně skutečné vyhledávání je určeno podkladový systém souborů:</span><span class="sxs-lookup"><span data-stu-id="38a46-420">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="38a46-421">Na základě zdrojového souboru:</span><span class="sxs-lookup"><span data-stu-id="38a46-421">File based source:</span></span>
  * <span data-ttu-id="38a46-422">V operačních systémech se systémy souborů malá a velká písmena (například Windows) fyzický soubor poskytovatele vyhledávání jsou malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="38a46-422">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="38a46-423">Například `return View("Test")` výsledky odpovídají pro */views/Home/test.cshtml*, */views/Home/test.cshtml*a jakékoliv jiné varianty velikosti písmen.</span><span class="sxs-lookup"><span data-stu-id="38a46-423">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="38a46-424">V systémech souborů s rozlišováním velkých a malých písmen (například Linux, OSX a s `EmbeddedFileProvider`) se při hledání rozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="38a46-424">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="38a46-425">Například `return View("Test")` konkrétně odpovídá */views/Home/test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="38a46-425">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="38a46-426">Předkompilované zobrazení: pomocí ASP.NET Core 2.0 a vyšší, hledání předkompilované zobrazení velká a malá písmena na všechny operační systémy.</span><span class="sxs-lookup"><span data-stu-id="38a46-426">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="38a46-427">Chování se shoduje s chování zprostředkovatele fyzického souboru ve Windows.</span><span class="sxs-lookup"><span data-stu-id="38a46-427">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="38a46-428">Pokud se zobrazeními předkompilované liší pouze v případě, výsledek vyhledávání je Nedeterministický.</span><span class="sxs-lookup"><span data-stu-id="38a46-428">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="38a46-429">Vývojáři nepodnikovým tak, aby odpovídaly malých a velkých písmen názvů použití malých a velkých souborů a adresářů:</span><span class="sxs-lookup"><span data-stu-id="38a46-429">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="38a46-430">Názvy oblastí, kontroleru a akce.</span><span class="sxs-lookup"><span data-stu-id="38a46-430">Area, controller, and action names.</span></span>
* <span data-ttu-id="38a46-431">Stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="38a46-431">Razor Pages.</span></span>

<span data-ttu-id="38a46-432">Odpovídající případ zajistí, že pro nasazení své názory, bez ohledu na podkladový systém souborů.</span><span class="sxs-lookup"><span data-stu-id="38a46-432">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>
