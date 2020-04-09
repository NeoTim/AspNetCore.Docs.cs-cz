---
title: Odkaz na syntaxi holicího strojku pro ASP.NET Core
author: rick-anderson
description: Přečtěte si o syntaxi značek Razor pro vkládání kódu založeného na serveru do webových stránek.
ms.author: riande
ms.date: 02/12/2020
uid: mvc/views/razor
ms.openlocfilehash: dd5c73be56ed0dafb759df2f5ff2eac1a3b5b09e
ms.sourcegitcommit: d03905aadf5ceac39fff17706481af7f6c130411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80381770"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="89d59-103">Odkaz na syntaxi holicího strojku pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="89d59-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="89d59-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), a Dan [Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="89d59-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="89d59-105">Razor je syntaxe značek pro vkládání kódu založeného na serveru do webových stránek.</span><span class="sxs-lookup"><span data-stu-id="89d59-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="89d59-106">Syntaxe Razor se skládá z značek Razor, C# a HTML.</span><span class="sxs-lookup"><span data-stu-id="89d59-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="89d59-107">Soubory obsahující Razor mají obecně příponu *.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="89d59-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="89d59-108">Břitva se také nachází v [souborech komponent Razor](xref:blazor/components) (*.razor*).</span><span class="sxs-lookup"><span data-stu-id="89d59-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="89d59-109">Vykreslování HTML</span><span class="sxs-lookup"><span data-stu-id="89d59-109">Rendering HTML</span></span>

<span data-ttu-id="89d59-110">Výchozím jazykem pro Razor je HTML.</span><span class="sxs-lookup"><span data-stu-id="89d59-110">The default Razor language is HTML.</span></span> <span data-ttu-id="89d59-111">Vykreslování HTML z kódu Razor se nijak neliší od vykreslování HTML ze souboru HTML.</span><span class="sxs-lookup"><span data-stu-id="89d59-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="89d59-112">Html značky v *.cshtml* Razor soubory je vykreslen serverem beze změny.</span><span class="sxs-lookup"><span data-stu-id="89d59-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="89d59-113">Syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="89d59-113">Razor syntax</span></span>

<span data-ttu-id="89d59-114">Razor podporuje C# `@` a používá symbol pro přechod z HTML na C#.</span><span class="sxs-lookup"><span data-stu-id="89d59-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="89d59-115">Razor vyhodnotí výrazy Jazyka C# a vykreslí je ve výstupu HTML.</span><span class="sxs-lookup"><span data-stu-id="89d59-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="89d59-116">Když `@` symbol následuje [razor vyhrazené klíčové slovo](#razor-reserved-keywords), přechody do razor-specifické značky.</span><span class="sxs-lookup"><span data-stu-id="89d59-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="89d59-117">V opačném případě přejde do prostého jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="89d59-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="89d59-118">Chcete-li `@` uniknout symbolu ve značkách Razor, použijte druhý `@` symbol:</span><span class="sxs-lookup"><span data-stu-id="89d59-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="89d59-119">Kód je vykreslen v HTML `@` s jedním symbolem:</span><span class="sxs-lookup"><span data-stu-id="89d59-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="89d59-120">Atributy HTML a obsah obsahující e-mailové `@` adresy nepovažují symbol za přechodový znak.</span><span class="sxs-lookup"><span data-stu-id="89d59-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="89d59-121">E-mailové adresy v následujícím příkladu jsou nedotčeny analýzou Razor:</span><span class="sxs-lookup"><span data-stu-id="89d59-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="89d59-122">Implicitní výrazy Razor</span><span class="sxs-lookup"><span data-stu-id="89d59-122">Implicit Razor expressions</span></span>

<span data-ttu-id="89d59-123">Implicitní výrazy `@` Razor začínají následovaným kódem Jazyka C#:</span><span class="sxs-lookup"><span data-stu-id="89d59-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="89d59-124">S výjimkou klíčového `await` slova C# nesmí implicitní výrazy obsahovat mezery.</span><span class="sxs-lookup"><span data-stu-id="89d59-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="89d59-125">Pokud c# prohlášení má jasný konec, mezery mohou být prolíná:</span><span class="sxs-lookup"><span data-stu-id="89d59-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="89d59-126">Implicitní výrazy **nemohou** obsahovat obecné znaky jazyka C#, protože znaky uvnitř závorek (`<>`) jsou interpretovány jako značka HTML.</span><span class="sxs-lookup"><span data-stu-id="89d59-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="89d59-127">Následující kód **není** platný:</span><span class="sxs-lookup"><span data-stu-id="89d59-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="89d59-128">Předchozí kód generuje chybu kompilátoru podobnou jedné z následujících:</span><span class="sxs-lookup"><span data-stu-id="89d59-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="89d59-129">Prvek "int" nebyl uzavřen.</span><span class="sxs-lookup"><span data-stu-id="89d59-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="89d59-130">Všechny prvky musí být buď samouzavírací nebo musí mít odpovídající koncovou značku.</span><span class="sxs-lookup"><span data-stu-id="89d59-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="89d59-131">Skupinu metod GenericMethod nelze převést na nedelegovaného typu "object".</span><span class="sxs-lookup"><span data-stu-id="89d59-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="89d59-132">Měl jste v úmyslu vyvolat metodu?</span><span class="sxs-lookup"><span data-stu-id="89d59-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="89d59-133">Volání obecné metody musí být zabaleno v [explicitním výrazu Razor](#explicit-razor-expressions) nebo [bloku kódu Razor](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="89d59-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="89d59-134">Explicitní razor výrazy</span><span class="sxs-lookup"><span data-stu-id="89d59-134">Explicit Razor expressions</span></span>

<span data-ttu-id="89d59-135">Explicitní razor výrazy `@` se skládají ze symbolu s vyváženou závorkou.</span><span class="sxs-lookup"><span data-stu-id="89d59-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="89d59-136">Chcete-li vykreslit čas z minulého týdne, použije se následující značky Razor:</span><span class="sxs-lookup"><span data-stu-id="89d59-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="89d59-137">Veškerý obsah `@()` v závorce je vyhodnocen a vykreslen do výstupu.</span><span class="sxs-lookup"><span data-stu-id="89d59-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="89d59-138">Implicitní výrazy popsané v předchozí části obecně nesmí obsahovat mezery.</span><span class="sxs-lookup"><span data-stu-id="89d59-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="89d59-139">V následujícím kódu se jeden týden neodečítá od aktuálního času:</span><span class="sxs-lookup"><span data-stu-id="89d59-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="89d59-140">Kód vykreslí následující HTML:</span><span class="sxs-lookup"><span data-stu-id="89d59-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="89d59-141">Explicitní výrazy lze použít ke zřetězení textu s výsledkem výrazu:</span><span class="sxs-lookup"><span data-stu-id="89d59-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="89d59-142">Bez explicitní výraz, `<p>Age@joe.Age</p>` je považován za e-mailovou adresu a `<p>Age@joe.Age</p>` je vykreslen.</span><span class="sxs-lookup"><span data-stu-id="89d59-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="89d59-143">Při zápisu jako `<p>Age33</p>` explicitní výraz, je vykreslen.</span><span class="sxs-lookup"><span data-stu-id="89d59-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="89d59-144">Explicitní výrazy lze použít k vykreslení výstupu z obecných metod v souborech *.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="89d59-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="89d59-145">Následující značky ukazují, jak opravit chybu zobrazenou dříve způsobenou závorkami obecného jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="89d59-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="89d59-146">Kód je napsán jako explicitní výraz:</span><span class="sxs-lookup"><span data-stu-id="89d59-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="89d59-147">Kódování výrazů</span><span class="sxs-lookup"><span data-stu-id="89d59-147">Expression encoding</span></span>

<span data-ttu-id="89d59-148">C# výrazy, které vyhodnocují řetězec jsou kódovány HTML.</span><span class="sxs-lookup"><span data-stu-id="89d59-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="89d59-149">C# výrazy, `IHtmlContent` které vyhodnocují jsou vykresleny přímo prostřednictvím `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="89d59-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="89d59-150">C# výrazy, které nejsou `IHtmlContent` vyhodnoceny na `ToString` jsou převedeny na řetězec a zakódovány před jejich vykreslení.</span><span class="sxs-lookup"><span data-stu-id="89d59-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="89d59-151">Kód vykreslí následující HTML:</span><span class="sxs-lookup"><span data-stu-id="89d59-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="89d59-152">KÓD HTML se v prohlížeči zobrazuje jako:</span><span class="sxs-lookup"><span data-stu-id="89d59-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="89d59-153">`HtmlHelper.Raw`výstup není kódován, ale vykreslen jako html značky.</span><span class="sxs-lookup"><span data-stu-id="89d59-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="89d59-154">Použití `HtmlHelper.Raw` na nedezinfikované masívní uživatelský vstup je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="89d59-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="89d59-155">Uživatelský vstup může obsahovat škodlivý JavaScript nebo jiné zneužití.</span><span class="sxs-lookup"><span data-stu-id="89d59-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="89d59-156">Dezinfekce vstupu uživatele je obtížná.</span><span class="sxs-lookup"><span data-stu-id="89d59-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="89d59-157">Vyhněte se použití `HtmlHelper.Raw` s uživatelským vstupem.</span><span class="sxs-lookup"><span data-stu-id="89d59-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="89d59-158">Kód vykreslí následující HTML:</span><span class="sxs-lookup"><span data-stu-id="89d59-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="89d59-159">Bloky kódů holicí strojek</span><span class="sxs-lookup"><span data-stu-id="89d59-159">Razor code blocks</span></span>

<span data-ttu-id="89d59-160">Bloky kódu holicí strojek začínají `@` a jsou uzavřeny . `{}`</span><span class="sxs-lookup"><span data-stu-id="89d59-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="89d59-161">Na rozdíl od výrazů kód Jazyka C# uvnitř bloků kódu není vykreslen.</span><span class="sxs-lookup"><span data-stu-id="89d59-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="89d59-162">Bloky kódu a výrazy v zobrazení sdílejí stejný obor a jsou definovány v pořadí:</span><span class="sxs-lookup"><span data-stu-id="89d59-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="89d59-163">Kód vykreslí následující HTML:</span><span class="sxs-lookup"><span data-stu-id="89d59-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="89d59-164">V blocích kódu deklarujte [místní funkce](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) se značkami, které slouží jako metody šablonování:</span><span class="sxs-lookup"><span data-stu-id="89d59-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="89d59-165">Kód vykreslí následující HTML:</span><span class="sxs-lookup"><span data-stu-id="89d59-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="89d59-166">Implicitní přechody</span><span class="sxs-lookup"><span data-stu-id="89d59-166">Implicit transitions</span></span>

<span data-ttu-id="89d59-167">Výchozí jazyk v bloku kódu je C#, ale stránka Razor může přejít zpět na HTML:</span><span class="sxs-lookup"><span data-stu-id="89d59-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="89d59-168">Explicitní oddělený přechod</span><span class="sxs-lookup"><span data-stu-id="89d59-168">Explicit delimited transition</span></span>

<span data-ttu-id="89d59-169">Chcete-li definovat podsekci bloku kódu, který by měl vykreslit HTML, obklopte znaky pro vykreslování značkou Razor: `<text>`</span><span class="sxs-lookup"><span data-stu-id="89d59-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="89d59-170">Tento přístup slouží k vykreslení html, který není obklopen značkou HTML.</span><span class="sxs-lookup"><span data-stu-id="89d59-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="89d59-171">Bez značky HTML nebo Razor dojde k chybě razor runtime.</span><span class="sxs-lookup"><span data-stu-id="89d59-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="89d59-172">Značka `<text>` je užitečná pro řízení mezer při vykreslování obsahu:</span><span class="sxs-lookup"><span data-stu-id="89d59-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="89d59-173">Vykreslí se `<text>` pouze obsah mezi značkou.</span><span class="sxs-lookup"><span data-stu-id="89d59-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="89d59-174">Před nebo za výstupem `<text>` HTML se nezobrazí žádné mezery.</span><span class="sxs-lookup"><span data-stu-id="89d59-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="89d59-175">Explicitní přechod řádku</span><span class="sxs-lookup"><span data-stu-id="89d59-175">Explicit line transition</span></span>

<span data-ttu-id="89d59-176">Chcete-li vykreslit zbytek celého řádku `@:` jako HTML uvnitř bloku kódu, použijte syntaxi:</span><span class="sxs-lookup"><span data-stu-id="89d59-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="89d59-177">Bez `@:` v kódu je generována chyba razor runtime.</span><span class="sxs-lookup"><span data-stu-id="89d59-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="89d59-178">Další `@` znaky v souboru Razor může způsobit chyby kompilátoru na příkazy později v bloku.</span><span class="sxs-lookup"><span data-stu-id="89d59-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="89d59-179">Tyto chyby kompilátoru může být obtížné pochopit, protože skutečné chyby dochází před ohlášenou chybu.</span><span class="sxs-lookup"><span data-stu-id="89d59-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="89d59-180">Tato chyba je běžné po kombinaci více implicitní/explicitní výrazy do jednoho bloku kódu.</span><span class="sxs-lookup"><span data-stu-id="89d59-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="89d59-181">Řídicí struktury</span><span class="sxs-lookup"><span data-stu-id="89d59-181">Control structures</span></span>

<span data-ttu-id="89d59-182">Řídicí struktury jsou rozšíření matných bloků kódu.</span><span class="sxs-lookup"><span data-stu-id="89d59-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="89d59-183">Všechny aspekty bloků kódu (přechod na značky, vposlední řadě C#) platí také pro následující struktury:</span><span class="sxs-lookup"><span data-stu-id="89d59-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="89d59-184">\@Podmínky, pokud, jinak pokud, \@jinak, a přepnout</span><span class="sxs-lookup"><span data-stu-id="89d59-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="89d59-185">`@if`při spuštění kódu:</span><span class="sxs-lookup"><span data-stu-id="89d59-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="89d59-186">`else`a `else if` nevyžadují `@` symbol:</span><span class="sxs-lookup"><span data-stu-id="89d59-186">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="89d59-187">Následující značky ukazují, jak používat příkaz switch:</span><span class="sxs-lookup"><span data-stu-id="89d59-187">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="89d59-188">Opakování \@pro, \@foreach, \@zatímco, a \@dělat, zatímco</span><span class="sxs-lookup"><span data-stu-id="89d59-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="89d59-189">Šablonované HTML lze vykreslit pomocí příkazů řízení opakování.</span><span class="sxs-lookup"><span data-stu-id="89d59-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="89d59-190">Vykreslení seznamu osob:</span><span class="sxs-lookup"><span data-stu-id="89d59-190">To render a list of people:</span></span>

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

<span data-ttu-id="89d59-191">Jsou podporovány následující příkazy opakování:</span><span class="sxs-lookup"><span data-stu-id="89d59-191">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="89d59-192">Složené \@pomocí</span><span class="sxs-lookup"><span data-stu-id="89d59-192">Compound \@using</span></span>

<span data-ttu-id="89d59-193">V C# `using` příkaz se používá k zajištění objektu je uvolněn.</span><span class="sxs-lookup"><span data-stu-id="89d59-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="89d59-194">V Razor, stejný mechanismus se používá k vytvoření HTML pomocníků, které obsahují další obsah.</span><span class="sxs-lookup"><span data-stu-id="89d59-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="89d59-195">V následujícím kódu pomocníci HTML `<form>` vykreslují značku s příkazem: `@using`</span><span class="sxs-lookup"><span data-stu-id="89d59-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="89d59-196">\@zkuste, chytit, konečně</span><span class="sxs-lookup"><span data-stu-id="89d59-196">\@try, catch, finally</span></span>

<span data-ttu-id="89d59-197">Zpracování výjimek je podobné c#:</span><span class="sxs-lookup"><span data-stu-id="89d59-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="89d59-198">\@Zámek</span><span class="sxs-lookup"><span data-stu-id="89d59-198">\@lock</span></span>

<span data-ttu-id="89d59-199">Holicí strojek má schopnost chránit kritické části pomocí příkazů zámku:</span><span class="sxs-lookup"><span data-stu-id="89d59-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="89d59-200">Komentáře</span><span class="sxs-lookup"><span data-stu-id="89d59-200">Comments</span></span>

<span data-ttu-id="89d59-201">Razor podporuje c# a HTML komentáře:</span><span class="sxs-lookup"><span data-stu-id="89d59-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="89d59-202">Kód vykreslí následující HTML:</span><span class="sxs-lookup"><span data-stu-id="89d59-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="89d59-203">Razor komentáře jsou odstraněny serverem před vykreslením webové stránky.</span><span class="sxs-lookup"><span data-stu-id="89d59-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="89d59-204">Holicí strojek používá `@*  *@` k vymezení komentáře.</span><span class="sxs-lookup"><span data-stu-id="89d59-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="89d59-205">Následující kód je zakomentován, takže server nevykresluje žádné značky:</span><span class="sxs-lookup"><span data-stu-id="89d59-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="89d59-206">Direktivy</span><span class="sxs-lookup"><span data-stu-id="89d59-206">Directives</span></span>

<span data-ttu-id="89d59-207">Razor direktivy jsou reprezentovány implicitními výrazy s vyhrazenými klíčovými slovy za symbolem. `@`</span><span class="sxs-lookup"><span data-stu-id="89d59-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="89d59-208">Direktiva obvykle mění způsob analýzy zobrazení nebo umožňuje různé funkce.</span><span class="sxs-lookup"><span data-stu-id="89d59-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="89d59-209">Pochopení toho, jak Razor generuje kód pro zobrazení usnadňuje pochopení, jak fungují direktivy.</span><span class="sxs-lookup"><span data-stu-id="89d59-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="89d59-210">Kód generuje třídu podobnou následující:</span><span class="sxs-lookup"><span data-stu-id="89d59-210">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="89d59-211">Dále v tomto článku části [Zkontrolujte razor C # třídy generované pro zobrazení](#inspect-the-razor-c-class-generated-for-a-view) vysvětluje, jak zobrazit tuto generovanou třídu.</span><span class="sxs-lookup"><span data-stu-id="89d59-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="89d59-212">\@Atribut</span><span class="sxs-lookup"><span data-stu-id="89d59-212">\@attribute</span></span>

<span data-ttu-id="89d59-213">Směrnice `@attribute` přidá daný atribut do třídy generované stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="89d59-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="89d59-214">Následující příklad přidá `[Authorize]` atribut:</span><span class="sxs-lookup"><span data-stu-id="89d59-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="89d59-215">\@Kód</span><span class="sxs-lookup"><span data-stu-id="89d59-215">\@code</span></span>

<span data-ttu-id="89d59-216">*Tento scénář platí pouze pro komponenty Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="89d59-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="89d59-217">Blok `@code` umožňuje [komponentě Razor](xref:blazor/components) přidat členy jazyka C# (pole, vlastnosti a metody) do komponenty:</span><span class="sxs-lookup"><span data-stu-id="89d59-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="89d59-218">Pro komponenty `@code` Razor je [`@functions`](#functions) alias a `@functions`doporučený nad .</span><span class="sxs-lookup"><span data-stu-id="89d59-218">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="89d59-219">Je přípustné `@code` více než jeden blok.</span><span class="sxs-lookup"><span data-stu-id="89d59-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="89d59-220">\@Funkce</span><span class="sxs-lookup"><span data-stu-id="89d59-220">\@functions</span></span>

<span data-ttu-id="89d59-221">Směrnice `@functions` umožňuje přidání c# členy (pole, vlastnosti a metody) do generované třídy:</span><span class="sxs-lookup"><span data-stu-id="89d59-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="89d59-222">V [komponenty](xref:blazor/components)Razor `@code` `@functions` , použijte přes přidat c# členy.</span><span class="sxs-lookup"><span data-stu-id="89d59-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="89d59-223">Příklad:</span><span class="sxs-lookup"><span data-stu-id="89d59-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="89d59-224">Kód generuje následující značky HTML:</span><span class="sxs-lookup"><span data-stu-id="89d59-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="89d59-225">Následující kód je vygenerovaná třída Razor C#:</span><span class="sxs-lookup"><span data-stu-id="89d59-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="89d59-226">`@functions`metody slouží jako šablonovací metody, pokud mají značky:</span><span class="sxs-lookup"><span data-stu-id="89d59-226">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="89d59-227">Kód vykreslí následující HTML:</span><span class="sxs-lookup"><span data-stu-id="89d59-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="89d59-228">\@Implementuje</span><span class="sxs-lookup"><span data-stu-id="89d59-228">\@implements</span></span>

<span data-ttu-id="89d59-229">Směrnice `@implements` implementuje rozhraní pro generované třídy.</span><span class="sxs-lookup"><span data-stu-id="89d59-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="89d59-230">Následující příklad implementuje <xref:System.IDisposable?displayProperty=fullName> <xref:System.IDisposable.Dispose*> tak, aby metoda může být volána:</span><span class="sxs-lookup"><span data-stu-id="89d59-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

### <a name="inherits"></a><span data-ttu-id="89d59-231">\@Dědí</span><span class="sxs-lookup"><span data-stu-id="89d59-231">\@inherits</span></span>

<span data-ttu-id="89d59-232">Směrnice `@inherits` poskytuje úplné řízení třídy, kterou zobrazení dědí:</span><span class="sxs-lookup"><span data-stu-id="89d59-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="89d59-233">Následující kód je vlastní typ stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="89d59-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="89d59-234">Zobrazí `CustomText` se v zobrazení:</span><span class="sxs-lookup"><span data-stu-id="89d59-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="89d59-235">Kód vykreslí následující HTML:</span><span class="sxs-lookup"><span data-stu-id="89d59-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="89d59-236">`@model`a `@inherits` lze jej použít ve stejném zobrazení.</span><span class="sxs-lookup"><span data-stu-id="89d59-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="89d59-237">`@inherits`může být v souboru *_ViewImports.cshtml,* který zobrazení importuje:</span><span class="sxs-lookup"><span data-stu-id="89d59-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="89d59-238">Následující kód je příkladem zobrazení silného typu:</span><span class="sxs-lookup"><span data-stu-id="89d59-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="89d59-239">Pokudrick@contoso.comje v modelu předáno " "" pohled, zobrazí se následující značky HTML:</span><span class="sxs-lookup"><span data-stu-id="89d59-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="89d59-240">\@Nastříkne</span><span class="sxs-lookup"><span data-stu-id="89d59-240">\@inject</span></span>

<span data-ttu-id="89d59-241">Směrnice `@inject` umožňuje Razor Page vložit službu z [kontejneru služby](xref:fundamentals/dependency-injection) do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="89d59-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="89d59-242">Další informace naleznete v [tématu vkládání závislostí do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="89d59-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="89d59-243">\@Rozložení</span><span class="sxs-lookup"><span data-stu-id="89d59-243">\@layout</span></span>

<span data-ttu-id="89d59-244">*Tento scénář platí pouze pro komponenty Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="89d59-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="89d59-245">Směrnice `@layout` určuje rozložení komponenty Razor.</span><span class="sxs-lookup"><span data-stu-id="89d59-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="89d59-246">Součásti rozložení se používají, aby se zabránilo duplikaci kódu a nekonzistenci.</span><span class="sxs-lookup"><span data-stu-id="89d59-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="89d59-247">Další informace naleznete v tématu <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="89d59-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="89d59-248">\@Model</span><span class="sxs-lookup"><span data-stu-id="89d59-248">\@model</span></span>

<span data-ttu-id="89d59-249">*Tento scénář se vztahuje pouze na zobrazení MVC a Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="89d59-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="89d59-250">Směrnice `@model` určuje typ modelu předaného zobrazení nebo stránce:</span><span class="sxs-lookup"><span data-stu-id="89d59-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="89d59-251">V aplikaci ASP.NET Core MVC nebo Razor Pages vytvořené pomocí jednotlivých uživatelských účtů obsahuje *zobrazení/účet/Login.cshtml* následující prohlášení modelu:</span><span class="sxs-lookup"><span data-stu-id="89d59-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="89d59-252">Vygenerovaná třída `RazorPage<dynamic>`dědí z :</span><span class="sxs-lookup"><span data-stu-id="89d59-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="89d59-253">Razor zpřístupňuje `Model` vlastnost pro přístup k modelu předané do pohledu:</span><span class="sxs-lookup"><span data-stu-id="89d59-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="89d59-254">Směrnice `@model` určuje typ vlastnosti. `Model`</span><span class="sxs-lookup"><span data-stu-id="89d59-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="89d59-255">Směrnice určuje `T` v `RazorPage<T>` tom, že generované třídy, které je odvozené z zobrazení.</span><span class="sxs-lookup"><span data-stu-id="89d59-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="89d59-256">Pokud `@model` není zadána směrnice, `Model` vlastnost je `dynamic`typu .</span><span class="sxs-lookup"><span data-stu-id="89d59-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="89d59-257">Další informace naleznete [v tématu Silné @model typy modelů a klíčové slovo](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="89d59-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="89d59-258">\@namespace</span><span class="sxs-lookup"><span data-stu-id="89d59-258">\@namespace</span></span>

<span data-ttu-id="89d59-259">Směrnice: `@namespace`</span><span class="sxs-lookup"><span data-stu-id="89d59-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="89d59-260">Nastaví obor názvů třídy generované holicí strojek stránky, Zobrazení MVC nebo Razor komponenty.</span><span class="sxs-lookup"><span data-stu-id="89d59-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="89d59-261">Nastaví kořenové odvozené obory názvů tříd stránek, zobrazení nebo komponent z nejbližšího importovaného souboru ve stromu *adresářů, _ViewImports.cshtml* (zobrazení nebo stránky) nebo *_Imports.razor* (komponenty Razor).</span><span class="sxs-lookup"><span data-stu-id="89d59-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="89d59-262">Pro příklad Razor Pages zobrazený v následující tabulce:</span><span class="sxs-lookup"><span data-stu-id="89d59-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="89d59-263">Každá stránka importuje *Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="89d59-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="89d59-264">*Stránky/_ViewImports.cshtml* `@namespace Hello.World`obsahuje .</span><span class="sxs-lookup"><span data-stu-id="89d59-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="89d59-265">Každá stránka `Hello.World` má jako kořen svého oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="89d59-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="89d59-266">stránka</span><span class="sxs-lookup"><span data-stu-id="89d59-266">Page</span></span>                                        | <span data-ttu-id="89d59-267">Obor názvů</span><span class="sxs-lookup"><span data-stu-id="89d59-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="89d59-268">*Stránky/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="89d59-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="89d59-269">*Stránky/Další stránky/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="89d59-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="89d59-270">*Stránky/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="89d59-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="89d59-271">Předchozí vztahy platí pro import souborů používaných se zobrazeními MVC a komponentami Razor.</span><span class="sxs-lookup"><span data-stu-id="89d59-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="89d59-272">Pokud má více `@namespace` souborů importu direktivu, soubor, který je nejblíže stránce, zobrazení nebo součásti ve stromu adresářů, se použije k nastavení kořenového oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="89d59-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="89d59-273">Pokud složka *EvenMorePages* v předchozím příkladu obsahuje `@namespace Another.Planet` soubor importů s (nebo obsahuje soubor *Pages/MorePages/EvenMorePages/Page.cshtml* `@namespace Another.Planet`), výsledek se zobrazí v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="89d59-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="89d59-274">stránka</span><span class="sxs-lookup"><span data-stu-id="89d59-274">Page</span></span>                                        | <span data-ttu-id="89d59-275">Obor názvů</span><span class="sxs-lookup"><span data-stu-id="89d59-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="89d59-276">*Stránky/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="89d59-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="89d59-277">*Stránky/Další stránky/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="89d59-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="89d59-278">*Stránky/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="89d59-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="89d59-279">\@Stránka</span><span class="sxs-lookup"><span data-stu-id="89d59-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="89d59-280">Směrnice `@page` má různé účinky v závislosti na typu souboru, kde se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="89d59-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="89d59-281">Směrnice:</span><span class="sxs-lookup"><span data-stu-id="89d59-281">The directive:</span></span>

* <span data-ttu-id="89d59-282">V souboru *.cshtml* označuje, že soubor je Razor Page.</span><span class="sxs-lookup"><span data-stu-id="89d59-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="89d59-283">Další informace naleznete [v tématu Vlastní trasy](xref:razor-pages/index#custom-routes) a <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="89d59-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="89d59-284">Určuje, že komponenta Razor by měla zpracovávat požadavky přímo.</span><span class="sxs-lookup"><span data-stu-id="89d59-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="89d59-285">Další informace naleznete v tématu <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="89d59-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="89d59-286">Direktiva `@page` na prvním řádku souboru *.cshtml* označuje, že soubor je Razor Page.</span><span class="sxs-lookup"><span data-stu-id="89d59-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="89d59-287">Další informace naleznete v tématu <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="89d59-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="89d59-288">\@Oddíl</span><span class="sxs-lookup"><span data-stu-id="89d59-288">\@section</span></span>

<span data-ttu-id="89d59-289">*Tento scénář se vztahuje pouze na zobrazení MVC a Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="89d59-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="89d59-290">Směrnice `@section` se používá ve spojení s [rozloženími MVC a Razor Pages,](xref:mvc/views/layout) aby zobrazení nebo stránky mohly vykreslovat obsah v různých částech stránky HTML.</span><span class="sxs-lookup"><span data-stu-id="89d59-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="89d59-291">Další informace naleznete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="89d59-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="89d59-292">\@using</span><span class="sxs-lookup"><span data-stu-id="89d59-292">\@using</span></span>

<span data-ttu-id="89d59-293">Směrnice `@using` přidá `using` direktivu Jazyka C# do generovaného zobrazení:</span><span class="sxs-lookup"><span data-stu-id="89d59-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="89d59-294">V [komponenty](xref:blazor/components) `@using` Razor , také určuje, které součásti jsou v oboru.</span><span class="sxs-lookup"><span data-stu-id="89d59-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="89d59-295">Atributy směrnice</span><span class="sxs-lookup"><span data-stu-id="89d59-295">Directive attributes</span></span>

### <a name="attributes"></a><span data-ttu-id="89d59-296">\@Atributy</span><span class="sxs-lookup"><span data-stu-id="89d59-296">\@attributes</span></span>

<span data-ttu-id="89d59-297">*Tento scénář platí pouze pro komponenty Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="89d59-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="89d59-298">`@attributes`umožňuje komponentě vykreslit nedeklarované atributy.</span><span class="sxs-lookup"><span data-stu-id="89d59-298">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="89d59-299">Další informace naleznete v tématu <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="89d59-299">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="89d59-300">\@Vázat</span><span class="sxs-lookup"><span data-stu-id="89d59-300">\@bind</span></span>

<span data-ttu-id="89d59-301">*Tento scénář platí pouze pro komponenty Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="89d59-301">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="89d59-302">Datová vazba v součástech `@bind` se provádí s atributem.</span><span class="sxs-lookup"><span data-stu-id="89d59-302">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="89d59-303">Další informace naleznete v tématu <xref:blazor/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="89d59-303">For more information, see <xref:blazor/data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="89d59-304">\@zapnuto{EVENT}</span><span class="sxs-lookup"><span data-stu-id="89d59-304">\@on{EVENT}</span></span>

<span data-ttu-id="89d59-305">*Tento scénář platí pouze pro komponenty Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="89d59-305">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="89d59-306">Holicí strojek poskytuje funkce zpracování událostí pro komponenty.</span><span class="sxs-lookup"><span data-stu-id="89d59-306">Razor provides event handling features for components.</span></span> <span data-ttu-id="89d59-307">Další informace naleznete v tématu <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="89d59-307">For more information, see <xref:blazor/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a><span data-ttu-id="89d59-308">\@zapnuto{EVENT}:preventDefault</span><span class="sxs-lookup"><span data-stu-id="89d59-308">\@on{EVENT}:preventDefault</span></span>

<span data-ttu-id="89d59-309">*Tento scénář platí pouze pro komponenty Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="89d59-309">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="89d59-310">Zabrání výchozí akci pro událost.</span><span class="sxs-lookup"><span data-stu-id="89d59-310">Prevents the default action for the event.</span></span>

### <a name="oneventstoppropagation"></a><span data-ttu-id="89d59-311">\@on{EVENT}:stopPropagation</span><span class="sxs-lookup"><span data-stu-id="89d59-311">\@on{EVENT}:stopPropagation</span></span>

<span data-ttu-id="89d59-312">*Tento scénář platí pouze pro komponenty Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="89d59-312">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="89d59-313">Zastaví šíření událostí pro událost.</span><span class="sxs-lookup"><span data-stu-id="89d59-313">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a><span data-ttu-id="89d59-314">\@key</span><span class="sxs-lookup"><span data-stu-id="89d59-314">\@key</span></span>

<span data-ttu-id="89d59-315">*Tento scénář platí pouze pro komponenty Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="89d59-315">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="89d59-316">Atribut `@key` direktiva způsobí, že algoritmus diferenciování komponent zaručuje zachování prvků nebo součástí na základě hodnoty klíče.</span><span class="sxs-lookup"><span data-stu-id="89d59-316">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="89d59-317">Další informace naleznete v tématu <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="89d59-317">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="89d59-318">\@ref</span><span class="sxs-lookup"><span data-stu-id="89d59-318">\@ref</span></span>

<span data-ttu-id="89d59-319">*Tento scénář platí pouze pro komponenty Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="89d59-319">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="89d59-320">Odkazy na`@ref`komponenty ( ) poskytují způsob, jak odkazovat na instanci komponenty, takže můžete vydávat příkazy k této instanci.</span><span class="sxs-lookup"><span data-stu-id="89d59-320">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="89d59-321">Další informace naleznete v tématu <xref:blazor/components#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="89d59-321">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="89d59-322">\@typeparam</span><span class="sxs-lookup"><span data-stu-id="89d59-322">\@typeparam</span></span>

<span data-ttu-id="89d59-323">*Tento scénář platí pouze pro komponenty Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="89d59-323">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="89d59-324">Směrnice `@typeparam` deklaruje parametr obecného typu pro třídu generované součásti.</span><span class="sxs-lookup"><span data-stu-id="89d59-324">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="89d59-325">Další informace naleznete v tématu <xref:blazor/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="89d59-325">For more information, see <xref:blazor/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="89d59-326">Šablony Razor delegáti</span><span class="sxs-lookup"><span data-stu-id="89d59-326">Templated Razor delegates</span></span>

<span data-ttu-id="89d59-327">Šablony razor umožňují definovat fragment uživatelského uživatelského nastavení v následujícím formátu:</span><span class="sxs-lookup"><span data-stu-id="89d59-327">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="89d59-328">Následující příklad ukazuje, jak určit šablonovaný delegát <xref:System.Func%602>razor jako .</span><span class="sxs-lookup"><span data-stu-id="89d59-328">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="89d59-329">[Dynamický typ](/dotnet/csharp/programming-guide/types/using-type-dynamic) je určen pro parametr metody, kterou delegát zapouzdřuje.</span><span class="sxs-lookup"><span data-stu-id="89d59-329">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="89d59-330">[Typ objektu](/dotnet/csharp/language-reference/keywords/object) je určen jako vrácená hodnota delegáta.</span><span class="sxs-lookup"><span data-stu-id="89d59-330">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="89d59-331">Šablona se používá <xref:System.Collections.Generic.List%601> `Pet` s a, která má `Name` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="89d59-331">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="89d59-332">Šablona je vykreslena `pets` s `foreach` dodaným příkazem:</span><span class="sxs-lookup"><span data-stu-id="89d59-332">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="89d59-333">Vykreslený výstup:</span><span class="sxs-lookup"><span data-stu-id="89d59-333">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="89d59-334">Můžete také zadat vložku šablony jako argument metody.</span><span class="sxs-lookup"><span data-stu-id="89d59-334">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="89d59-335">V následujícím příkladu `Repeat` metoda obdrží šablonu Razor.</span><span class="sxs-lookup"><span data-stu-id="89d59-335">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="89d59-336">Metoda používá šablonu k vytvoření obsahu HTML s opakováními položek dodaných ze seznamu:</span><span class="sxs-lookup"><span data-stu-id="89d59-336">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="89d59-337">Pomocí seznamu domácích zvířat z `Repeat` předchozího příkladu je metoda volána pomocí:</span><span class="sxs-lookup"><span data-stu-id="89d59-337">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="89d59-338"><xref:System.Collections.Generic.List%601>v `Pet`roce 2015 .</span><span class="sxs-lookup"><span data-stu-id="89d59-338"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="89d59-339">Počet opakování každého domácího mazlíčka.</span><span class="sxs-lookup"><span data-stu-id="89d59-339">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="89d59-340">Vsazená šablona, která se použije pro položky seznamu neuspořádaného seznamu.</span><span class="sxs-lookup"><span data-stu-id="89d59-340">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="89d59-341">Vykreslený výstup:</span><span class="sxs-lookup"><span data-stu-id="89d59-341">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="89d59-342">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="89d59-342">Tag Helpers</span></span>

<span data-ttu-id="89d59-343">*Tento scénář se vztahuje pouze na zobrazení MVC a Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="89d59-343">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="89d59-344">Existují tři direktivy, které se vztahuje k [Tag Pomocníky](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="89d59-344">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="89d59-345">Směrnice</span><span class="sxs-lookup"><span data-stu-id="89d59-345">Directive</span></span> | <span data-ttu-id="89d59-346">Funkce</span><span class="sxs-lookup"><span data-stu-id="89d59-346">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="89d59-347">Zpřístupní pomocné společování značek v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="89d59-347">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="89d59-348">Odebere pomocné saze značek, které byly dříve přidány ze zobrazení.</span><span class="sxs-lookup"><span data-stu-id="89d59-348">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="89d59-349">Určuje předponu značky, která povolí podporu pomocníka značek a učiní použití pomocníka značek explicitní.</span><span class="sxs-lookup"><span data-stu-id="89d59-349">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="89d59-350">Holicí strojek vyhrazená klíčová slova</span><span class="sxs-lookup"><span data-stu-id="89d59-350">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="89d59-351">Klíčová slova Razor</span><span class="sxs-lookup"><span data-stu-id="89d59-351">Razor keywords</span></span>

* <span data-ttu-id="89d59-352">(Vyžaduje ASP.NET Core 2.1 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="89d59-352">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="89d59-353">namespace</span><span class="sxs-lookup"><span data-stu-id="89d59-353">namespace</span></span>
* <span data-ttu-id="89d59-354"> – funkce</span><span class="sxs-lookup"><span data-stu-id="89d59-354">functions</span></span>
* <span data-ttu-id="89d59-355">Dědí</span><span class="sxs-lookup"><span data-stu-id="89d59-355">inherits</span></span>
* <span data-ttu-id="89d59-356">model</span><span class="sxs-lookup"><span data-stu-id="89d59-356">model</span></span>
* <span data-ttu-id="89d59-357">section</span><span class="sxs-lookup"><span data-stu-id="89d59-357">section</span></span>
* <span data-ttu-id="89d59-358">pomocník (Momentálně není podporovánASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="89d59-358">helper (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="89d59-359">Razor klíčová slova `@(Razor Keyword)` jsou uvozeny s (například). `@(functions)`</span><span class="sxs-lookup"><span data-stu-id="89d59-359">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="89d59-360">C# Razor klíčová slova</span><span class="sxs-lookup"><span data-stu-id="89d59-360">C# Razor keywords</span></span>

* <span data-ttu-id="89d59-361">case</span><span class="sxs-lookup"><span data-stu-id="89d59-361">case</span></span>
* <span data-ttu-id="89d59-362">do</span><span class="sxs-lookup"><span data-stu-id="89d59-362">do</span></span>
* <span data-ttu-id="89d59-363">default</span><span class="sxs-lookup"><span data-stu-id="89d59-363">default</span></span>
* <span data-ttu-id="89d59-364">pro</span><span class="sxs-lookup"><span data-stu-id="89d59-364">for</span></span>
* <span data-ttu-id="89d59-365">foreach</span><span class="sxs-lookup"><span data-stu-id="89d59-365">foreach</span></span>
* <span data-ttu-id="89d59-366">if</span><span class="sxs-lookup"><span data-stu-id="89d59-366">if</span></span>
* <span data-ttu-id="89d59-367">else</span><span class="sxs-lookup"><span data-stu-id="89d59-367">else</span></span>
* <span data-ttu-id="89d59-368">lock</span><span class="sxs-lookup"><span data-stu-id="89d59-368">lock</span></span>
* <span data-ttu-id="89d59-369">switch</span><span class="sxs-lookup"><span data-stu-id="89d59-369">switch</span></span>
* <span data-ttu-id="89d59-370">vyzkoušení</span><span class="sxs-lookup"><span data-stu-id="89d59-370">try</span></span>
* <span data-ttu-id="89d59-371">Chytit</span><span class="sxs-lookup"><span data-stu-id="89d59-371">catch</span></span>
* <span data-ttu-id="89d59-372">finally</span><span class="sxs-lookup"><span data-stu-id="89d59-372">finally</span></span>
* <span data-ttu-id="89d59-373">using</span><span class="sxs-lookup"><span data-stu-id="89d59-373">using</span></span>
* <span data-ttu-id="89d59-374">while</span><span class="sxs-lookup"><span data-stu-id="89d59-374">while</span></span>

<span data-ttu-id="89d59-375">C# Razor klíčová slova musí `@(@C# Razor Keyword)` být double-escaped s `@(@case)`(například).</span><span class="sxs-lookup"><span data-stu-id="89d59-375">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="89d59-376">První `@` unikne analyzátoru Razor.</span><span class="sxs-lookup"><span data-stu-id="89d59-376">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="89d59-377">Druhý `@` unikne analyzátoru Jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="89d59-377">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="89d59-378">Vyhrazená klíčová slova, která razor nepoužívá</span><span class="sxs-lookup"><span data-stu-id="89d59-378">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="89d59-379">třída</span><span class="sxs-lookup"><span data-stu-id="89d59-379">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="89d59-380">Kontrola třídy Razor C# generované pro zobrazení</span><span class="sxs-lookup"><span data-stu-id="89d59-380">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="89d59-381">S .NET Core SDK 2.1 nebo novější, [Razor SDK](xref:razor-pages/sdk) zpracovává kompilaci razor souborů.</span><span class="sxs-lookup"><span data-stu-id="89d59-381">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="89d59-382">Při vytváření projektu vkořenicí strojek SDK generuje *obj/<build_configuration>/<target_framework_moniker>/Razor* adresář v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="89d59-382">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="89d59-383">Adresářová struktura v adresáři *Razor* zrcadlí adresářovou strukturu projektu.</span><span class="sxs-lookup"><span data-stu-id="89d59-383">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="89d59-384">Zvažte následující adresářovou strukturu v projektu ASP.NET Core 2.1 Razor Pages zaměřeného na rozhraní .NET Core 2.1:</span><span class="sxs-lookup"><span data-stu-id="89d59-384">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="89d59-385">**Oblasti/**</span><span class="sxs-lookup"><span data-stu-id="89d59-385">**Areas/**</span></span>
  * <span data-ttu-id="89d59-386">**Admin/**</span><span class="sxs-lookup"><span data-stu-id="89d59-386">**Admin/**</span></span>
    * <span data-ttu-id="89d59-387">**Stránky/**</span><span class="sxs-lookup"><span data-stu-id="89d59-387">**Pages/**</span></span>
      * <span data-ttu-id="89d59-388">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="89d59-388">*Index.cshtml*</span></span>
      * <span data-ttu-id="89d59-389">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="89d59-389">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="89d59-390">**Stránky/**</span><span class="sxs-lookup"><span data-stu-id="89d59-390">**Pages/**</span></span>
  * <span data-ttu-id="89d59-391">**Sdílené/**</span><span class="sxs-lookup"><span data-stu-id="89d59-391">**Shared/**</span></span>
    * <span data-ttu-id="89d59-392">*Soubor _Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="89d59-392">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="89d59-393">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="89d59-393">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="89d59-394">*_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="89d59-394">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="89d59-395">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="89d59-395">*Index.cshtml*</span></span>
  * <span data-ttu-id="89d59-396">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="89d59-396">*Index.cshtml.cs*</span></span>

<span data-ttu-id="89d59-397">Vytvoření projektu v konfiguraci *ladění* poskytuje následující *adresář obj:*</span><span class="sxs-lookup"><span data-stu-id="89d59-397">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="89d59-398">**obj/**</span><span class="sxs-lookup"><span data-stu-id="89d59-398">**obj/**</span></span>
  * <span data-ttu-id="89d59-399">**Ladění/**</span><span class="sxs-lookup"><span data-stu-id="89d59-399">**Debug/**</span></span>
    * <span data-ttu-id="89d59-400">**netcoreapp2,1/**</span><span class="sxs-lookup"><span data-stu-id="89d59-400">**netcoreapp2.1/**</span></span>
      * <span data-ttu-id="89d59-401">**Holicí strojek/**</span><span class="sxs-lookup"><span data-stu-id="89d59-401">**Razor/**</span></span>
        * <span data-ttu-id="89d59-402">**Oblasti/**</span><span class="sxs-lookup"><span data-stu-id="89d59-402">**Areas/**</span></span>
          * <span data-ttu-id="89d59-403">**Admin/**</span><span class="sxs-lookup"><span data-stu-id="89d59-403">**Admin/**</span></span>
            * <span data-ttu-id="89d59-404">**Stránky/**</span><span class="sxs-lookup"><span data-stu-id="89d59-404">**Pages/**</span></span>
              * <span data-ttu-id="89d59-405">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="89d59-405">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="89d59-406">**Stránky/**</span><span class="sxs-lookup"><span data-stu-id="89d59-406">**Pages/**</span></span>
          * <span data-ttu-id="89d59-407">**Sdílené/**</span><span class="sxs-lookup"><span data-stu-id="89d59-407">**Shared/**</span></span>
            * <span data-ttu-id="89d59-408">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="89d59-408">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="89d59-409">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="89d59-409">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="89d59-410">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="89d59-410">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="89d59-411">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="89d59-411">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="89d59-412">Chcete-li zobrazit vygenerovanou třídu pro *pages/Index.cshtml*, otevřete *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="89d59-412">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="89d59-413">Přidejte do projektu ASP.NET Core MVC následující třídu:</span><span class="sxs-lookup"><span data-stu-id="89d59-413">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="89d59-414">V `Startup.ConfigureServices`, přepsat `RazorTemplateEngine` přidané MVC `CustomTemplateEngine` s třídou:</span><span class="sxs-lookup"><span data-stu-id="89d59-414">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="89d59-415">Nastavte zarážku `return csharpDocument;` na `CustomTemplateEngine`příkaz .</span><span class="sxs-lookup"><span data-stu-id="89d59-415">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="89d59-416">Pokud se spuštění programu zastaví na zarážky, zobrazte hodnotu aplikace `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="89d59-416">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Zobrazení vizualizéru textu generovaného kódu](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="89d59-418">Zobrazení vyhledávání a rozlišování malých a velkých písmen</span><span class="sxs-lookup"><span data-stu-id="89d59-418">View lookups and case sensitivity</span></span>

<span data-ttu-id="89d59-419">Modul zobrazení Razor provádí vyhledávání zobrazení rozlišující malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="89d59-419">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="89d59-420">Skutečné vyhledávání je však určeno základním systémem souborů:</span><span class="sxs-lookup"><span data-stu-id="89d59-420">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="89d59-421">Zdroj založený na souboru:</span><span class="sxs-lookup"><span data-stu-id="89d59-421">File based source:</span></span>
  * <span data-ttu-id="89d59-422">V operačních systémech se systémy souborů bez rozlišování velkých a malých písmen (například windows) jsou vyhledávání zprostředkovatele fyzických souborů rozlišována malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="89d59-422">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="89d59-423">`return View("Test")` Výsledkem jsou například shody pro */Views/Home/Test.cshtml*, */Views/home/test.cshtml*a jakoukoli jinou variantu krytu.</span><span class="sxs-lookup"><span data-stu-id="89d59-423">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="89d59-424">V systémech souborů rozlišujících malá a velká `EmbeddedFileProvider`písmena (například Linux, OSX a s ) jsou vyhledávání rozlišována malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="89d59-424">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="89d59-425">Konkrétně `return View("Test")` se shoduje například *s parametrem /Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="89d59-425">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="89d59-426">Předkompilovaná zobrazení: S ASP.NET jádrem 2.0 a novějším je vyhledávání předkompilovaných zobrazení ve všech operačních systémech malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="89d59-426">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="89d59-427">Chování je shodné s chováním zprostředkovatele fyzického souboru v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="89d59-427">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="89d59-428">Pokud se dvě předkompilovaná zobrazení liší pouze v případě, výsledek vyhledávání je nedeterministický.</span><span class="sxs-lookup"><span data-stu-id="89d59-428">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="89d59-429">Vývojáři se doporučuje, aby odpovídaly velikosti písmen souborů a názvů adresářů s velikostí:</span><span class="sxs-lookup"><span data-stu-id="89d59-429">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="89d59-430">Názvy oblastí, kontroleru a akcí.</span><span class="sxs-lookup"><span data-stu-id="89d59-430">Area, controller, and action names.</span></span>
* <span data-ttu-id="89d59-431">Žiletky stránky.</span><span class="sxs-lookup"><span data-stu-id="89d59-431">Razor Pages.</span></span>

<span data-ttu-id="89d59-432">Odpovídající případ zajišťuje nasazení najít jejich zobrazení bez ohledu na základní systém souborů.</span><span class="sxs-lookup"><span data-stu-id="89d59-432">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="89d59-433">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="89d59-433">Additional resources</span></span>

<span data-ttu-id="89d59-434">[Úvod do ASP.NET webového programování pomocí syntaxe holicího strojku](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) poskytuje mnoho vzorků programování se syntaxí Razor.</span><span class="sxs-lookup"><span data-stu-id="89d59-434">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
