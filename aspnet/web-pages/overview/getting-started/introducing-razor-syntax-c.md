---
uid: web-pages/overview/getting-started/introducing-razor-syntax-c
title: Úvod k programování v rozhraní ASP.NET Web používající syntaxi Razor (C#) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tato kapitola obsahuje přehled programování s webovými stránkami ASP.NET pomocí syntaxe Razor. ASP.NET je technologie od Microsoftu pro spouštění dynamického webového pa...
ms.author: riande
ms.date: 02/07/2014
ms.assetid: aa67d304-583b-4bf8-a231-195656cfb587
msc.legacyurl: /web-pages/overview/getting-started/introducing-razor-syntax-c
msc.type: authoredcontent
ms.openlocfilehash: b5eb98dfdf3fc013920f45080d4a20e1fa507725
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021778"
---
<a name="introduction-to-aspnet-web-programming-using-the-razor-syntax-c"></a><span data-ttu-id="1408f-104">Úvod k programování v rozhraní ASP.NET Web používající syntaxi Razor (C#)</span><span class="sxs-lookup"><span data-stu-id="1408f-104">Introduction to ASP.NET Web Programming Using the Razor Syntax (C#)</span></span>
====================
<span data-ttu-id="1408f-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="1408f-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="1408f-106">Tento článek obsahuje přehled programování s webovými stránkami ASP.NET pomocí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="1408f-106">This article gives you an overview of programming with ASP.NET Web Pages using the Razor syntax.</span></span> <span data-ttu-id="1408f-107">ASP.NET je technologie od Microsoftu pro spouštění dynamické webové stránky na webové servery.</span><span class="sxs-lookup"><span data-stu-id="1408f-107">ASP.NET is Microsoft's technology for running dynamic web pages on web servers.</span></span> <span data-ttu-id="1408f-108">Toto se články zaměřuje na pomocí programovacího jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="1408f-108">This articles focuses on using the C# programming language.</span></span>
> 
> <span data-ttu-id="1408f-109">**Co se dozvíte**:</span><span class="sxs-lookup"><span data-stu-id="1408f-109">**What you'll learn**:</span></span>
> 
> - <span data-ttu-id="1408f-110">Začátek 8 Programovací tipy pro zahájení práce s programování pomocí syntaxe Razor rozhraní ASP.NET Web Pages.</span><span class="sxs-lookup"><span data-stu-id="1408f-110">The top 8 programming tips for getting started with programming ASP.NET Web Pages using Razor syntax.</span></span>
> - <span data-ttu-id="1408f-111">Základní koncepty programování, které budete potřebovat.</span><span class="sxs-lookup"><span data-stu-id="1408f-111">Basic programming concepts you'll need.</span></span>
> - <span data-ttu-id="1408f-112">Jaký kód serveru technologie ASP.NET a syntaxe Razor je všechno o.</span><span class="sxs-lookup"><span data-stu-id="1408f-112">What ASP.NET server code and the Razor syntax is all about.</span></span>
>   
> 
> ## <a name="software-versions"></a><span data-ttu-id="1408f-113">Verze softwaru</span><span class="sxs-lookup"><span data-stu-id="1408f-113">Software versions</span></span>
> 
> 
> - <span data-ttu-id="1408f-114">Webové stránky ASP.NET (Razor) 3</span><span class="sxs-lookup"><span data-stu-id="1408f-114">ASP.NET Web Pages (Razor) 3</span></span>
>   
> 
> <span data-ttu-id="1408f-115">V tomto kurzu se také pracuje s ASP.NET Web Pages 2.</span><span class="sxs-lookup"><span data-stu-id="1408f-115">This tutorial also works with ASP.NET Web Pages 2.</span></span>


## <a name="the-top-8-programming-tips"></a><span data-ttu-id="1408f-116">Začátek 8 Tipy pro programování</span><span class="sxs-lookup"><span data-stu-id="1408f-116">The Top 8 Programming Tips</span></span>

<span data-ttu-id="1408f-117">Tato část uvádí několik tipů, které je nezbytně potřeba ví, jak můžete začít psát kód serveru ASP.NET pomocí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="1408f-117">This section lists a few tips that you absolutely need to know as you start writing ASP.NET server code using the Razor syntax.</span></span>

> [!NOTE]
> <span data-ttu-id="1408f-118">Syntaxe Razor je založena na programovací jazyk C# a, který je jazyk, který se často používá s webovými stránkami ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1408f-118">The Razor syntax is based on the C# programming language, and that's the language that's used most often with ASP.NET Web Pages.</span></span> <span data-ttu-id="1408f-119">Syntaxe Razor, ale také podporuje jazyka Visual Basic a všechno, co uvidíte, že můžete provést také v jazyce Visual Basic.</span><span class="sxs-lookup"><span data-stu-id="1408f-119">However, the Razor syntax also supports the Visual Basic language, and everything you see you can also do in Visual Basic.</span></span> <span data-ttu-id="1408f-120">Podrobnosti najdete v tématu dodatku [syntaxe a jazyk Visual Basic](https://go.microsoft.com/fwlink/?LinkId=202908).</span><span class="sxs-lookup"><span data-stu-id="1408f-120">For details, see the appendix [Visual Basic Language and Syntax](https://go.microsoft.com/fwlink/?LinkId=202908).</span></span>


<span data-ttu-id="1408f-121">Později v tomto článku najdete další podrobnosti o většinu těchto programovacích technik.</span><span class="sxs-lookup"><span data-stu-id="1408f-121">You can find more details about most of these programming techniques later in the article.</span></span>

### <a name="1-you-add-code-to-a-page-using-the--character"></a><span data-ttu-id="1408f-122">1. Přidejte kód do stránky pomocí znak @</span><span class="sxs-lookup"><span data-stu-id="1408f-122">1. You add code to a page using the @ character</span></span>

<span data-ttu-id="1408f-123">`@` Začne znak vložené výrazy, jeden příkaz bloky a bloky více příkazy:</span><span class="sxs-lookup"><span data-stu-id="1408f-123">The `@` character starts inline expressions, single statement blocks, and multi-statement blocks:</span></span>

[!code-html[Main](introducing-razor-syntax-c/samples/sample1.html)]

<span data-ttu-id="1408f-124">Je to, jak tyto příkazy vypadat při spuštění stránky v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="1408f-124">This is what these statements look like when the page runs in a browser:</span></span>

![Razor Img1](introducing-razor-syntax-c/_static/image1.jpg)

> [!TIP] 
> 
> <span data-ttu-id="1408f-126">**Kódování HTML**</span><span class="sxs-lookup"><span data-stu-id="1408f-126">**HTML Encoding**</span></span>
> 
> <span data-ttu-id="1408f-127">Při zobrazení obsahu do stránky pomocí `@` znaků, stejně jako v předchozích příkladech ASP.NET umístí kódování HTML výstup.</span><span class="sxs-lookup"><span data-stu-id="1408f-127">When you display content in a page using the `@` character, as in the preceding examples, ASP.NET HTML-encodes the output.</span></span> <span data-ttu-id="1408f-128">To nahrazuje vyhrazené znaky HTML (například `<` a `>` a `&`) s kódy, které umožňují znaků, který se má zobrazit jako znaků na webové stránce nebude interpretován jako značku jazyka HTML nebo entity.</span><span class="sxs-lookup"><span data-stu-id="1408f-128">This replaces reserved HTML characters (such as `<` and `>` and `&`) with codes that enable the characters to be displayed as characters in a web page instead of being interpreted as HTML tags or entities.</span></span> <span data-ttu-id="1408f-129">Bez kódování HTML výstup z kódu serveru se nemusí zobrazit správně a může zpřístupnit stránky na bezpečnostní rizika.</span><span class="sxs-lookup"><span data-stu-id="1408f-129">Without HTML encoding, the output from your server code might not display correctly, and could expose a page to security risks.</span></span>
> 
> <span data-ttu-id="1408f-130">Pokud je vaším cílem je výstup kód HTML, který vykreslí značky jako značka (třeba `<p></p>` odstavce nebo `<em></em>` pro zvýraznění textu), najdete v části [kombinaci textu, značek a kódu v blocích kódu](#BM_CombiningTextMarkupAndCode) dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="1408f-130">If your goal is to output HTML markup that renders tags as markup (for example `<p></p>` for a paragraph or `<em></em>` to emphasize text), see the section [Combining Text, Markup, and Code in Code Blocks](#BM_CombiningTextMarkupAndCode) later in this article.</span></span>
> 
> <span data-ttu-id="1408f-131">Další informace o kódování HTML v [práce s formuláři](https://go.microsoft.com/fwlink/?LinkId=202892).</span><span class="sxs-lookup"><span data-stu-id="1408f-131">You can read more about HTML encoding in [Working with Forms](https://go.microsoft.com/fwlink/?LinkId=202892).</span></span>


### <a name="2-you-enclose-code-blocks-in-braces"></a><span data-ttu-id="1408f-132">2. Bloky kódu je uzavřít do složených závorek</span><span class="sxs-lookup"><span data-stu-id="1408f-132">2. You enclose code blocks in braces</span></span>

<span data-ttu-id="1408f-133">A *blok kódu* obsahuje jeden nebo více příkazů kódu a je uzavřené ve složených závorkách.</span><span class="sxs-lookup"><span data-stu-id="1408f-133">A *code block* includes one or more code statements and is enclosed in braces.</span></span>

[!code-html[Main](introducing-razor-syntax-c/samples/sample2.html)]

<span data-ttu-id="1408f-134">Výsledek zobrazí v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="1408f-134">The result displayed in a browser:</span></span>

![Razor Img2](introducing-razor-syntax-c/_static/image2.jpg)

### <a name="3-inside-a-block-you-end-each-code-statement-with-a-semicolon"></a><span data-ttu-id="1408f-136">3. Uvnitř bloku ukončit každý příkaz kódu oddělte středníkem.</span><span class="sxs-lookup"><span data-stu-id="1408f-136">3. Inside a block, you end each code statement with a semicolon</span></span>

<span data-ttu-id="1408f-137">Každý příkaz kompletní kód uvnitř bloku kódu, musí končit středníkem.</span><span class="sxs-lookup"><span data-stu-id="1408f-137">Inside a code block, each complete code statement must end with a semicolon.</span></span> <span data-ttu-id="1408f-138">Vložené výrazy nejsou končí středníkem.</span><span class="sxs-lookup"><span data-stu-id="1408f-138">Inline expressions don't end with a semicolon.</span></span>

[!code-html[Main](introducing-razor-syntax-c/samples/sample3.html)]

### <a name="4-you-use-variables-to-store-values"></a><span data-ttu-id="1408f-139">4. Použití proměnných k ukládání hodnot</span><span class="sxs-lookup"><span data-stu-id="1408f-139">4. You use variables to store values</span></span>

<span data-ttu-id="1408f-140">Můžete ukládat hodnoty *proměnnou*, včetně řetězců, čísel a data, atd. Vytvoření nové proměnné pomocí `var` – klíčové slovo.</span><span class="sxs-lookup"><span data-stu-id="1408f-140">You can store values in a *variable*, including strings, numbers, and dates, etc. You create a new variable using the `var` keyword.</span></span> <span data-ttu-id="1408f-141">Hodnoty proměnných můžete vložit přímo do stránky pomocí `@`.</span><span class="sxs-lookup"><span data-stu-id="1408f-141">You can insert variable values directly in a page using `@`.</span></span>

[!code-html[Main](introducing-razor-syntax-c/samples/sample4.html)]

<span data-ttu-id="1408f-142">Výsledek zobrazí v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="1408f-142">The result displayed in a browser:</span></span>

![Razor Img3](introducing-razor-syntax-c/_static/image3.jpg)

<a id="ID_StringLiterals"></a>
### <a name="5-you-enclose-literal-string-values-in-double-quotation-marks"></a><span data-ttu-id="1408f-144">5. Použijte hodnoty řetězcový literál v uvozovkách</span><span class="sxs-lookup"><span data-stu-id="1408f-144">5. You enclose literal string values in double quotation marks</span></span>

<span data-ttu-id="1408f-145">A *řetězec* je posloupnost znaků, které jsou považovány za text.</span><span class="sxs-lookup"><span data-stu-id="1408f-145">A *string* is a sequence of characters that are treated as text.</span></span> <span data-ttu-id="1408f-146">Pokud chcete zadat řetězec, uzavřete do dvojitých uvozovek:</span><span class="sxs-lookup"><span data-stu-id="1408f-146">To specify a string, you enclose it in double quotation marks:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample5.cshtml)]

<span data-ttu-id="1408f-147">Pokud řetězec, který chcete zobrazit obsahuje znak zpětného lomítka ( `\` ) nebo dvojité uvozovky ( `"` ), použijte *doslovný řetězec literálu* , který je s předponou `@` operátor.</span><span class="sxs-lookup"><span data-stu-id="1408f-147">If the string that you want to display contains a backslash character ( `\` ) or double quotation marks ( `"` ), use a *verbatim string literal* that's prefixed with the `@` operator.</span></span> <span data-ttu-id="1408f-148">(V jazyce C# \ znak má zvláštní význam, pokud nechcete použít doslovný řetězec literálu.)</span><span class="sxs-lookup"><span data-stu-id="1408f-148">(In C#, the \ character has special meaning unless you use a verbatim string literal.)</span></span>

[!code-html[Main](introducing-razor-syntax-c/samples/sample6.html)]

<span data-ttu-id="1408f-149">K vložení uvozovek, použijte doslovný řetězec literálu a opakujte uvozovek:</span><span class="sxs-lookup"><span data-stu-id="1408f-149">To embed double quotation marks, use a verbatim string literal and repeat the quotation marks:</span></span>

[!code-html[Main](introducing-razor-syntax-c/samples/sample7.html)]

<span data-ttu-id="1408f-150">Tady je výsledkem použití obou těchto příkladech na stránce:</span><span class="sxs-lookup"><span data-stu-id="1408f-150">Here's the result of using both of these examples in a page:</span></span>

![Razor Img4](introducing-razor-syntax-c/_static/image4.jpg)

> [!NOTE]
> <span data-ttu-id="1408f-152">Všimněte si, že `@` znak slouží k označení literálů doslovném řetězci v jazyce C# a označit kód stránek v ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1408f-152">Notice that the `@` character is used both to mark verbatim string literals in C# and to mark code in ASP.NET pages.</span></span>


### <a name="6-code-is-case-sensitive"></a><span data-ttu-id="1408f-153">6. Kód je velká a malá písmena</span><span class="sxs-lookup"><span data-stu-id="1408f-153">6. Code is case sensitive</span></span>

<span data-ttu-id="1408f-154">V jazyce C#, klíčová slova (stejně jako `var`, `true`, a `if`) a názvy proměnných jsou malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="1408f-154">In C#, keywords (like `var`, `true`, and `if`) and variable names are case sensitive.</span></span> <span data-ttu-id="1408f-155">Následující řádky kódu vytvářejí dva různé proměnné, `lastName` a `LastName.`</span><span class="sxs-lookup"><span data-stu-id="1408f-155">The following lines of code create two different variables, `lastName` and `LastName.`</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample8.cshtml)]

<span data-ttu-id="1408f-156">Pokud deklarujete proměnnou jako `var lastName = "Smith";` a pokud se pokusíte odkazují na tuto proměnnou na stránce jako `@LastName`, dochází k chybě, protože `LastName` nebude rozpoznán.</span><span class="sxs-lookup"><span data-stu-id="1408f-156">If you declare a variable as `var lastName = "Smith";` and if you try to reference that variable in your page as `@LastName`, an error results because `LastName` won't be recognized.</span></span>

> [!NOTE]
> <span data-ttu-id="1408f-157">V jazyce Visual Basic, klíčová slova a proměnné jsou *není* velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="1408f-157">In Visual Basic, keywords and variables are *not* case sensitive.</span></span>


### <a name="7-much-of-your-coding-involves-objects"></a><span data-ttu-id="1408f-158">7. Velká část psaní kódu zahrnuje objekty</span><span class="sxs-lookup"><span data-stu-id="1408f-158">7. Much of your coding involves objects</span></span>

<span data-ttu-id="1408f-159">*Objekt* představuje věc, kterou můžete programovat s &#8212; stránku, textové pole, soubor, obrázek, webový požadavek, e-mailovou zprávu, záznam zákazníka (řádek databáze), atd. Objekty mají vlastnosti, které popisují jejich vlastností a že může číst nebo změnit &#8212; textový objekt pole má `Text` vlastnost (mimo jiné), má objekt žádosti `Url` vlastnost, nemá e-mailovou zprávu `From` vlastnost, a objekt zákazníka má `FirstName` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="1408f-159">An *object* represents a thing that you can program with &#8212; a page, a text box, a file, an image, a web request, an email message, a customer record (database row), etc. Objects have properties that describe their characteristics and that you can read or change &#8212; a text box object has a `Text` property (among others), a request object has a `Url` property, an email message has a `From` property, and a customer object has a `FirstName` property.</span></span> <span data-ttu-id="1408f-160">Objekty mají také metody, které jsou &quot;příkazy&quot; mohou provádět.</span><span class="sxs-lookup"><span data-stu-id="1408f-160">Objects also have methods that are the &quot;verbs&quot; they can perform.</span></span> <span data-ttu-id="1408f-161">Mezi příklady patří objektu soubor `Save` metoda, objektu image `Rotate` metoda a objekt e-mailu `Send` – metoda.</span><span class="sxs-lookup"><span data-stu-id="1408f-161">Examples include a file object's `Save` method, an image object's `Rotate` method, and an email object's `Send` method.</span></span>

<span data-ttu-id="1408f-162">Často budete pracovat `Request` objekt, který poskytuje informace, například hodnoty textová pole (polí formuláře) na stránce, jaký typ prohlížeče přišel požadavek, adresa URL stránky, identita uživatele, atd. Následující příklad ukazuje, jak přistupovat k vlastnosti `Request` objekt a jak volat `MapPath` metodu `Request` objektu, který obsahuje absolutní cestu na stránce na serveru:</span><span class="sxs-lookup"><span data-stu-id="1408f-162">You'll often work with the `Request` object, which gives you information like the values of text boxes (form fields) on the page, what type of browser made the request, the URL of the page, the user identity, etc. The following example shows how to access properties of the `Request` object and how to call the `MapPath` method of the `Request` object, which gives you the absolute path of the page on the server:</span></span>

[!code-html[Main](introducing-razor-syntax-c/samples/sample9.html)]

<span data-ttu-id="1408f-163">Výsledek zobrazí v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="1408f-163">The result displayed in a browser:</span></span>

![Razor Img5](introducing-razor-syntax-c/_static/image5.jpg)

### <a name="8-you-can-write-code-that-makes-decisions"></a><span data-ttu-id="1408f-165">8. Můžete napsat kód, který provádí rozhodnutí</span><span class="sxs-lookup"><span data-stu-id="1408f-165">8. You can write code that makes decisions</span></span>

<span data-ttu-id="1408f-166">Klíčovou funkcí dynamické webové stránky je, že určíte, co se provedou v závislosti na podmínkách.</span><span class="sxs-lookup"><span data-stu-id="1408f-166">A key feature of dynamic web pages is that you can determine what to do based on conditions.</span></span> <span data-ttu-id="1408f-167">Nejběžnější způsob je pomocí `if` – příkaz (a volitelné `else` příkaz).</span><span class="sxs-lookup"><span data-stu-id="1408f-167">The most common way to do this is with the `if` statement (and optional `else` statement).</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample10.cshtml)]

<span data-ttu-id="1408f-168">Příkaz `if(IsPost)` je zjednodušený způsob psaní `if(IsPost == true)`.</span><span class="sxs-lookup"><span data-stu-id="1408f-168">The statement `if(IsPost)` is a shorthand way of writing `if(IsPost == true)`.</span></span> <span data-ttu-id="1408f-169">Spolu s `if` příkazy, existuje široká škála způsobů, jak otestovat podmínky, při opakovaném bloky kódu, a tak dále, které jsou popsány dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="1408f-169">Along with `if` statements, there are a variety of ways to test conditions, repeat blocks of code, and so on, which are described later in this article.</span></span>

<span data-ttu-id="1408f-170">Výsledek zobrazí v prohlížeči (po kliknutí na tlačítko **odeslat**):</span><span class="sxs-lookup"><span data-stu-id="1408f-170">The result displayed in a browser (after clicking **Submit**):</span></span>

![Razor Img6](introducing-razor-syntax-c/_static/image6.jpg)

> [!TIP] 
> 
> <a id="SB_HttpGetPost"></a>
> ### <a name="http-get-and-post-methods-and-the-ispost-property"></a><span data-ttu-id="1408f-172">HTTP GET a POST metody a vlastnosti IsPost</span><span class="sxs-lookup"><span data-stu-id="1408f-172">HTTP GET and POST Methods and the IsPost Property</span></span>
> 
> <span data-ttu-id="1408f-173">Protokol použitý pro webové stránky (HTTP) podporuje velmi omezený počet metody (akce), které se používají k provádění požadavků na server.</span><span class="sxs-lookup"><span data-stu-id="1408f-173">The protocol used for web pages (HTTP) supports a very limited number of methods (verbs) that are used to make requests to the server.</span></span> <span data-ttu-id="1408f-174">Nichž dva nejčastější jsou GET, který slouží k načtení stránky a příspěvku, který se používá k odeslání stránky.</span><span class="sxs-lookup"><span data-stu-id="1408f-174">The two most common ones are GET, which is used to read a page, and POST, which is used to submit a page.</span></span> <span data-ttu-id="1408f-175">Obecně platí uživatel požádá o stránku při prvním požadavku na stránku pomocí GET.</span><span class="sxs-lookup"><span data-stu-id="1408f-175">In general, the first time a user requests a page, the page is requested using GET.</span></span> <span data-ttu-id="1408f-176">Pokud uživatel vyplní ve formuláři a poté klikne na tlačítko Odeslat, prohlížeč odešle požadavek POST na server.</span><span class="sxs-lookup"><span data-stu-id="1408f-176">If the user fills in a form and then clicks a submit button, the browser makes a POST request to the server.</span></span>
> 
> <span data-ttu-id="1408f-177">Ve webovém programování často je užitečné vědět, jestli na stránce jsou požadovány jako GET nebo POST, abyste věděli, jak zpracování stránky.</span><span class="sxs-lookup"><span data-stu-id="1408f-177">In web programming, it's often useful to know whether a page is being requested as a GET or as a POST so that you know how to process the page.</span></span> <span data-ttu-id="1408f-178">ASP.NET Web Pages, můžete použít `IsPost` vlastnosti chcete zobrazit, zda je požadavek GET nebo POST.</span><span class="sxs-lookup"><span data-stu-id="1408f-178">In ASP.NET Web Pages, you can use the `IsPost` property to see whether a request is a GET or a POST.</span></span> <span data-ttu-id="1408f-179">Pokud je příspěvek, požadavek `IsPost` vlastnost vrátí hodnotu PRAVDA, a můžete provádět věci, jako je čtení hodnoty polí ve formuláři.</span><span class="sxs-lookup"><span data-stu-id="1408f-179">If the request is a POST, the `IsPost` property will return true, and you can do things like read the values of text boxes on a form.</span></span> <span data-ttu-id="1408f-180">Mnoho příkladů, zobrazí se vám ukazují, jak zpracovat stránce odlišně v závislosti na hodnotě `IsPost`.</span><span class="sxs-lookup"><span data-stu-id="1408f-180">Many examples you'll see show you how to process the page differently depending on the value of `IsPost`.</span></span>


## <a name="a-simple-code-example"></a><span data-ttu-id="1408f-181">Jednoduchým příkladem kódu</span><span class="sxs-lookup"><span data-stu-id="1408f-181">A Simple Code Example</span></span>

<span data-ttu-id="1408f-182">Tento postup ukazuje, jak vytvořit stránku, která ukazuje základní programovací techniky.</span><span class="sxs-lookup"><span data-stu-id="1408f-182">This procedure shows you how to create a page that illustrates basic programming techniques.</span></span> <span data-ttu-id="1408f-183">V tomto příkladu vytvoříte stránky, která umožňuje uživatelům zadat dvě čísla, potom se přidají a zobrazí výsledek.</span><span class="sxs-lookup"><span data-stu-id="1408f-183">In the example, you create a page that lets users enter two numbers, then it adds them and displays the result.</span></span>

1. <span data-ttu-id="1408f-184">V editoru, vytvořte nový soubor s názvem *AddNumbers.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1408f-184">In your editor, create a new file and name it *AddNumbers.cshtml*.</span></span>
2. <span data-ttu-id="1408f-185">Zkopírujte následující kód a kód na stránku, nahrazení nic již na stránce.</span><span class="sxs-lookup"><span data-stu-id="1408f-185">Copy the following code and markup into the page, replacing anything already in the page.</span></span>  

    [!code-cshtml[Main](introducing-razor-syntax-c/samples/sample11.cshtml)]

    <span data-ttu-id="1408f-186">Tady jsou některé věci za vás mějte na paměti:</span><span class="sxs-lookup"><span data-stu-id="1408f-186">Here are some things for you to note:</span></span>

    - <span data-ttu-id="1408f-187">`@` Znak spustí první blok kódu ve stránce, a předchází `totalMessage` proměnné, který je vložený v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="1408f-187">The `@` character starts the first block of code in the page, and it precedes the `totalMessage` variable that's embedded near the bottom of the page.</span></span>
    - <span data-ttu-id="1408f-188">Blok v horní části stránky je uzavřen do složených závorek.</span><span class="sxs-lookup"><span data-stu-id="1408f-188">The block at the top of the page is enclosed in braces.</span></span>
    - <span data-ttu-id="1408f-189">V bloku v horní části všech řádků končí středníkem.</span><span class="sxs-lookup"><span data-stu-id="1408f-189">In the block at the top, all lines end with a semicolon.</span></span>
    - <span data-ttu-id="1408f-190">Proměnné `total`, `num1`, `num2`, a `totalMessage` uložení několika čísla a řetězce.</span><span class="sxs-lookup"><span data-stu-id="1408f-190">The variables `total`, `num1`, `num2`, and `totalMessage` store several numbers and a string.</span></span>
    - <span data-ttu-id="1408f-191">Řetězcový literál hodnota přiřazená `totalMessage` proměnná je do dvojitých uvozovek.</span><span class="sxs-lookup"><span data-stu-id="1408f-191">The literal string value assigned to the `totalMessage` variable is in double quotation marks.</span></span>
    - <span data-ttu-id="1408f-192">Protože kód je velká a malá písmena, když `totalMessage` proměnná se používá v dolní části stránky, jeho název musí přesně odpovídat proměnné v horní části.</span><span class="sxs-lookup"><span data-stu-id="1408f-192">Because the code is case-sensitive, when the `totalMessage` variable is used near the bottom of the page, its name must match the variable at the top exactly.</span></span>
    - <span data-ttu-id="1408f-193">Výraz `num1.AsInt() + num2.AsInt()` ukazuje, jak pracovat s objekty a metody.</span><span class="sxs-lookup"><span data-stu-id="1408f-193">The expression `num1.AsInt() + num2.AsInt()` shows how to work with objects and methods.</span></span> <span data-ttu-id="1408f-194">`AsInt` Metodu na každou proměnnou převede řetězec zadaný uživatelem na číslo (integer) tak, aby aritmetické operace můžete provádět v něm.</span><span class="sxs-lookup"><span data-stu-id="1408f-194">The `AsInt` method on each variable converts the string entered by a user to a number (an integer) so that you can perform arithmetic on it.</span></span>
    - <span data-ttu-id="1408f-195">`<form>` Značka zahrnuje `method="post"` atribut.</span><span class="sxs-lookup"><span data-stu-id="1408f-195">The `<form>` tag includes a `method="post"` attribute.</span></span> <span data-ttu-id="1408f-196">Toto určuje, že když uživatel klikne **přidat**, stránky se odešlou na server pomocí metody POST protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="1408f-196">This specifies that when the user clicks **Add**, the page will be sent to the server using the HTTP POST method.</span></span> <span data-ttu-id="1408f-197">Při odeslání stránky `if(IsPost)` test vyhodnocen jako true a podmíněný kód spuštění zobrazení výsledek přidání čísla.</span><span class="sxs-lookup"><span data-stu-id="1408f-197">When the page is submitted, the `if(IsPost)` test evaluates to true and the conditional code runs, displaying the result of adding the numbers.</span></span>
3. <span data-ttu-id="1408f-198">Uložit na stránku a spustíte ji v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="1408f-198">Save the page and run it in a browser.</span></span> <span data-ttu-id="1408f-199">(Ujistěte se, že je vybrána na stránce v **soubory** pracovního prostoru před jeho spuštěním.) Zadejte dvěma celými čísly a poté klikněte **přidat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="1408f-199">(Make sure the page is selected in the **Files** workspace before you run it.) Enter two whole numbers and then click the **Add** button.</span></span> 

    ![Razor Img7](introducing-razor-syntax-c/_static/image7.jpg)

## <a name="basic-programming-concepts"></a><span data-ttu-id="1408f-201">Základní koncepty programování</span><span class="sxs-lookup"><span data-stu-id="1408f-201">Basic Programming Concepts</span></span>

<span data-ttu-id="1408f-202">Tento článek poskytuje přehled technologie ASP.NET webové programování.</span><span class="sxs-lookup"><span data-stu-id="1408f-202">This article provides you with an overview of ASP.NET web programming.</span></span> <span data-ttu-id="1408f-203">Není vyčerpávající zkoumání, pouze chcete zobrazit rychlou prohlídku prostřednictvím koncepty programování, které budete používat nejčastěji.</span><span class="sxs-lookup"><span data-stu-id="1408f-203">It isn't an exhaustive examination, just a quick tour through the programming concepts you'll use most often.</span></span> <span data-ttu-id="1408f-204">I tak zahrnuje téměř vše, co budete potřebovat, abyste mohli začít s webovými stránkami ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1408f-204">Even so, it covers almost everything you'll need to get started with ASP.NET Web Pages.</span></span>

<span data-ttu-id="1408f-205">Ale nejprve, málo technické znalosti potřebné.</span><span class="sxs-lookup"><span data-stu-id="1408f-205">But first, a little technical background.</span></span>

### <a name="the-razor-syntax-server-code-and-aspnet"></a><span data-ttu-id="1408f-206">Syntaxe Razor, kódu serveru a technologie ASP.NET</span><span class="sxs-lookup"><span data-stu-id="1408f-206">The Razor Syntax, Server Code, and ASP.NET</span></span>

<span data-ttu-id="1408f-207">Syntaxe Razor je jednoduché programovací syntaxi pro vkládání serverový kód na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="1408f-207">Razor syntax is a simple programming syntax for embedding server-based code in a web page.</span></span> <span data-ttu-id="1408f-208">Na webové stránce, která používá syntaxi Razor, existují dva typy obsahu: obsah a server kód klienta.</span><span class="sxs-lookup"><span data-stu-id="1408f-208">In a web page that uses the Razor syntax, there are two kinds of content: client content and server code.</span></span> <span data-ttu-id="1408f-209">Obsah klienta je věci, které jste zvyklí na webových stránkách: Značka jazyka HTML (prvky), stylu informace, jako jsou šablony stylů CSS, možná některé klientského skriptu, jako je JavaScript nebo jako prostý text.</span><span class="sxs-lookup"><span data-stu-id="1408f-209">Client content is the stuff you're used to in web pages: HTML markup (elements), style information such as CSS, maybe some client script such as JavaScript, and plain text.</span></span>

<span data-ttu-id="1408f-210">Syntaxe Razor můžete přidat kód serveru obsah tohoto klienta.</span><span class="sxs-lookup"><span data-stu-id="1408f-210">Razor syntax lets you add server code to this client content.</span></span> <span data-ttu-id="1408f-211">-Li existuje serverový kód na stránce, serveru spustí tento kód nejprve předtím, než ji odešle stránky v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="1408f-211">If there's server code in the page, the server runs that code first, before it sends the page to the browser.</span></span> <span data-ttu-id="1408f-212">Běží na serveru, kód můžete provádět úlohy, které může být mnohem složitější udělat pomocí klienta obsahu samostatně, jako je přístup k databázím na serveru.</span><span class="sxs-lookup"><span data-stu-id="1408f-212">By running on the server, the code can perform tasks that can be a lot more complex to do using client content alone, like accessing server-based databases.</span></span> <span data-ttu-id="1408f-213">Co je nejdůležitější, můžete vytvořit kód serveru dynamicky klientům obsahu &#8212; může generovat kód HTML nebo další obsah v reálném čase a potom je odešle do prohlížeče spolu s statický kód HTML, který stránka může obsahovat.</span><span class="sxs-lookup"><span data-stu-id="1408f-213">Most importantly, server code can dynamically create client content &#8212; it can generate HTML markup or other content on the fly and then send it to the browser along with any static HTML that the page might contain.</span></span> <span data-ttu-id="1408f-214">Z pohledu prohlížeče se nijak neliší od jakýkoli klient obsah klientům obsahu, který je generován kódu serveru.</span><span class="sxs-lookup"><span data-stu-id="1408f-214">From the browser's perspective, client content that's generated by your server code is no different than any other client content.</span></span> <span data-ttu-id="1408f-215">Jak už víte, do kódu serveru, který je potřeba je úplně jednoduché.</span><span class="sxs-lookup"><span data-stu-id="1408f-215">As you've already seen, the server code that's required is quite simple.</span></span>

<span data-ttu-id="1408f-216">Webové stránky ASP.NET, které obsahují syntaxi Razor mít speciální příponu (*.cshtml* nebo *.vbhtml*).</span><span class="sxs-lookup"><span data-stu-id="1408f-216">ASP.NET web pages that include the Razor syntax have a special file extension (*.cshtml* or *.vbhtml*).</span></span> <span data-ttu-id="1408f-217">Server rozpozná tato rozšíření, spustí kód, který je označen se syntaxí Razor a potom odešle stránku v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="1408f-217">The server recognizes these extensions, runs the code that's marked with Razor syntax, and then sends the page to the browser.</span></span>

### <a name="where-does-aspnet-fit-in"></a><span data-ttu-id="1408f-218">Kde se uplatní technologie ASP.NET?</span><span class="sxs-lookup"><span data-stu-id="1408f-218">Where does ASP.NET fit in?</span></span>

<span data-ttu-id="1408f-219">Syntaxe Razor je založena na technologii od Microsoftu, volá se, ASP.NET, která zase je založena na rozhraní Microsoft .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="1408f-219">Razor syntax is based on a technology from Microsoft called ASP.NET, which in turn is based on the Microsoft .NET Framework.</span></span> <span data-ttu-id="1408f-220">Rozhraní.NET Framework je velký objem, komplexní programovací rozhraní od Microsoftu pro vývoj prakticky libovolného typu aplikace.</span><span class="sxs-lookup"><span data-stu-id="1408f-220">The.NET Framework is a big, comprehensive programming framework from Microsoft for developing virtually any type of computer application.</span></span> <span data-ttu-id="1408f-221">ASP.NET je součástí rozhraní .NET Framework, která je navržená speciálně pro vytváření webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="1408f-221">ASP.NET is the part of the .NET Framework that's specifically designed for creating web applications.</span></span> <span data-ttu-id="1408f-222">Vývojáři ASP.NET použili k vytvoření mnoha největší a nejvyšší provoz webů na světě.</span><span class="sxs-lookup"><span data-stu-id="1408f-222">Developers have used ASP.NET to create many of the largest and highest-traffic websites in the world.</span></span> <span data-ttu-id="1408f-223">(Když se zobrazí příponu názvu souboru *.aspx* jako část adresy URL v lokalitě, budete vědět, zda byla vytvořena webu pomocí technologie ASP.NET.)</span><span class="sxs-lookup"><span data-stu-id="1408f-223">(Any time you see the file-name extension *.aspx* as part of the URL in a site, you'll know that the site was written using ASP.NET.)</span></span>

<span data-ttu-id="1408f-224">Syntaxe Razor poskytuje veškerou sílu technologie ASP.NET, ale pomocí zjednodušenou syntaxi, která je jednodušší zjistit, zda jste začátečník, a díky tomu budete produktivnější Pokud nejste odborníkem.</span><span class="sxs-lookup"><span data-stu-id="1408f-224">The Razor syntax gives you all the power of ASP.NET, but using a simplified syntax that's easier to learn if you're a beginner and that makes you more productive if you're an expert.</span></span> <span data-ttu-id="1408f-225">I když tato syntaxe se snadno používá, jeho řady relaci ASP.NET a .NET Framework znamená, že vaše weby jsou složitější, nutné sílu větší architektury, které jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="1408f-225">Even though this syntax is simple to use, its family relationship to ASP.NET and the .NET Framework means that as your websites become more sophisticated, you have the power of the larger frameworks available to you.</span></span>

![Razor Img8](introducing-razor-syntax-c/_static/image8.jpg)

> [!TIP] 
> 
> <span data-ttu-id="1408f-227">**Třídy a instance**</span><span class="sxs-lookup"><span data-stu-id="1408f-227">**Classes and Instances**</span></span>
> 
> <span data-ttu-id="1408f-228">Kód serveru ASP.NET používá objekty, které jsou zase založené na nápad třídy.</span><span class="sxs-lookup"><span data-stu-id="1408f-228">ASP.NET server code uses objects, which are in turn built on the idea of classes.</span></span> <span data-ttu-id="1408f-229">Třída je definice nebo šablony objektu.</span><span class="sxs-lookup"><span data-stu-id="1408f-229">The class is the definition or template for an object.</span></span> <span data-ttu-id="1408f-230">Například může aplikace obsahovat `Customer` třídu, která definuje vlastnosti a metody, které potřebuje každý objekt zákazníka.</span><span class="sxs-lookup"><span data-stu-id="1408f-230">For example, an application might contain a `Customer` class that defines the properties and methods that any customer object needs.</span></span>
> 
> <span data-ttu-id="1408f-231">Pokud aplikace potřebuje pracovat s informacemi o skutečné zákazníka, vytvoří instanci (nebo *vytvoří instanci*) objekt zákazníka.</span><span class="sxs-lookup"><span data-stu-id="1408f-231">When the application needs to work with actual customer information, it creates an instance of (or *instantiates*) a customer object.</span></span> <span data-ttu-id="1408f-232">Je každého zákazníka samostatnou instanci `Customer` třídy.</span><span class="sxs-lookup"><span data-stu-id="1408f-232">Each individual customer is a separate instance of the `Customer` class.</span></span> <span data-ttu-id="1408f-233">Každá instance podporuje stejné vlastnosti a metody, ale hodnoty vlastností pro každou instanci se obvykle liší, protože každý objekt zákazníka je jedinečný.</span><span class="sxs-lookup"><span data-stu-id="1408f-233">Every instance supports the same properties and methods, but the property values for each instance are typically different, because each customer object is unique.</span></span> <span data-ttu-id="1408f-234">V objektu jednoho zákazníka `LastName` vlastnost může být "Novák"; v jiném objektu zákazníků, `LastName` vlastnost může být "Jones."</span><span class="sxs-lookup"><span data-stu-id="1408f-234">In one customer object, the `LastName` property might be "Smith"; in another customer object, the `LastName` property might be "Jones."</span></span>
> 
> <span data-ttu-id="1408f-235">Podobně je všechny jednotlivé webové stránky ve vaší lokalitě `Page` objekt, který je instancí `Page` třídy.</span><span class="sxs-lookup"><span data-stu-id="1408f-235">Similarly, any individual web page in your site is a `Page` object that's an instance of the `Page` class.</span></span> <span data-ttu-id="1408f-236">Je tlačítko na stránce `Button` objekt, který je instancí `Button` třídy a tak dále.</span><span class="sxs-lookup"><span data-stu-id="1408f-236">A button on the page is a `Button` object that is an instance of the `Button` class, and so on.</span></span> <span data-ttu-id="1408f-237">Každá instance má vlastní vlastnosti, ale všechny jsou založeny na zadaných v definici třídy objektu.</span><span class="sxs-lookup"><span data-stu-id="1408f-237">Each instance has its own characteristics, but they all are based on what's specified in the object's class definition.</span></span>


## <a name="basic-syntax"></a><span data-ttu-id="1408f-238">Základní syntaxe</span><span class="sxs-lookup"><span data-stu-id="1408f-238">Basic Syntax</span></span>

<span data-ttu-id="1408f-239">Dříve jste viděli základní příklad, jak vytvořit stránku ASP.NET Web Pages a jak můžete přidat kód serveru do kódu HTML.</span><span class="sxs-lookup"><span data-stu-id="1408f-239">Earlier you saw a basic example of how to create an ASP.NET Web Pages page, and how you can add server code to HTML markup.</span></span> <span data-ttu-id="1408f-240">Zde se dozvíte základní informace o psaní kódu serveru ASP.NET pomocí syntaxe Razor &#8212; tedy programovací jazyk pravidla.</span><span class="sxs-lookup"><span data-stu-id="1408f-240">Here you'll learn the basics of writing ASP.NET server code using the Razor syntax &#8212; that is, the programming language rules.</span></span>

<span data-ttu-id="1408f-241">Pokud máte zkušenosti s programování (zejména pokud jste použili C, C++, C#, Visual Basic nebo JavaScript), velkou část můžete přečíst tady bude známé.</span><span class="sxs-lookup"><span data-stu-id="1408f-241">If you're experienced with programming (especially if you've used C, C++, C#, Visual Basic, or JavaScript), much of what you read here will be familiar.</span></span> <span data-ttu-id="1408f-242">Budete pravděpodobně potřebovat pouze s jak server kód přidá značku v *.cshtml* soubory.</span><span class="sxs-lookup"><span data-stu-id="1408f-242">You'll probably need to familiarize yourself only with how server code is added to markup in *.cshtml* files.</span></span>

<a id="BM_CombiningTextMarkupAndCode"></a>
### <a name="combining-text-markup-and-code-in-code-blocks"></a><span data-ttu-id="1408f-243">Kombinování textu, značek a kódu v blocích kódu</span><span class="sxs-lookup"><span data-stu-id="1408f-243">Combining Text, Markup, and Code in Code Blocks</span></span>

<span data-ttu-id="1408f-244">V serveru bloky kódu často chcete výstup text značky (nebo obojí) na stránce.</span><span class="sxs-lookup"><span data-stu-id="1408f-244">In server code blocks, you often want to output text or markup (or both) to the page.</span></span> <span data-ttu-id="1408f-245">Pokud blok kódu serveru obsahuje text, který není kódu a který místo toho má být vykreslen jako je, musí být nedokáží rozlišit tento text z kódu ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1408f-245">If a server code block contains text that's not code and that instead should be rendered as is, ASP.NET needs to be able to distinguish that text from code.</span></span> <span data-ttu-id="1408f-246">Chcete-li to provést několika způsoby.</span><span class="sxs-lookup"><span data-stu-id="1408f-246">There are several ways to do this.</span></span>

- <span data-ttu-id="1408f-247">Uzavřete text v elementu HTML jako `<p></p>` nebo `<em></em>`:</span><span class="sxs-lookup"><span data-stu-id="1408f-247">Enclose the text in an HTML element like `<p></p>` or `<em></em>`:</span></span>   

    [!code-cshtml[Main](introducing-razor-syntax-c/samples/sample12.cshtml)]

    <span data-ttu-id="1408f-248">HTML element mohou zahrnovat text, další prvky jazyka HTML a výrazy kódu serveru.</span><span class="sxs-lookup"><span data-stu-id="1408f-248">The HTML element can include text, additional HTML elements, and server-code expressions.</span></span> <span data-ttu-id="1408f-249">Když ASP.NET uvidí počáteční značky HTML (například `<p>`), se vykresluje všechno včetně elementu a jeho obsah, jako je v prohlížeči, jak funguje překlad výrazů kódu na serveru.</span><span class="sxs-lookup"><span data-stu-id="1408f-249">When ASP.NET sees the opening HTML tag (for example, `<p>`), it renders everything including the element and its content as is to the browser, resolving server-code expressions as it goes.</span></span>
- <span data-ttu-id="1408f-250">Použití `@:` operátor nebo `<text>` elementu.</span><span class="sxs-lookup"><span data-stu-id="1408f-250">Use the `@:` operator or the `<text>` element.</span></span> <span data-ttu-id="1408f-251">`@:` Výstupy jednořádkový obsah, který obsahuje prostý text nebo nespárované značky jazyka HTML; `<text>` element vloží několik řádků výstupu.</span><span class="sxs-lookup"><span data-stu-id="1408f-251">The `@:` outputs a single line of content containing plain text or unmatched HTML tags; the `<text>` element encloses multiple lines to output.</span></span> <span data-ttu-id="1408f-252">Tyto možnosti jsou užitečné, pokud nechcete, aby k vykreslení elementu HTML jako část ve výstupu.</span><span class="sxs-lookup"><span data-stu-id="1408f-252">These options are useful when you don't want to render an HTML element as part of the output.</span></span>  

    [!code-cshtml[Main](introducing-razor-syntax-c/samples/sample13.cshtml)]

    <span data-ttu-id="1408f-253">Pokud chcete výstup více řádků textu nebo nespárované značky HTML, může předcházet každý řádek `@:`, nebo je možné uzavřít do uvozovek na řádku `<text>` elementu.</span><span class="sxs-lookup"><span data-stu-id="1408f-253">If you want to output multiple lines of text or unmatched HTML tags, you can precede each line with `@:`, or you can enclose the line in a `<text>` element.</span></span> <span data-ttu-id="1408f-254">Podobně jako `@:` operátor`<text>` značky jsou použitý technologií ASP.NET k identifikaci obsahu textu a nikdy se zobrazují v výstup stránky.</span><span class="sxs-lookup"><span data-stu-id="1408f-254">Like the `@:` operator,`<text>` tags are used by ASP.NET to identify text content and are never rendered in the page output.</span></span>

    [!code-cshtml[Main](introducing-razor-syntax-c/samples/sample14.cshtml)]

    <span data-ttu-id="1408f-255">První příklad v předchozím příkladu se opakuje, ale používá jednu dvojici `<text>` značky uzavřít text k vykreslení.</span><span class="sxs-lookup"><span data-stu-id="1408f-255">The first example repeats the previous example but uses a single pair of `<text>` tags to enclose the text to render.</span></span> <span data-ttu-id="1408f-256">V druhém příkladu `<text>` a `</text>` značky uzavřít tři řádky, které mají některé uncontained text a bezkonkurenční značky HTML (`<br />`), spolu s kódu serveru a odpovídající značky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="1408f-256">In the second example, the `<text>` and `</text>` tags enclose three lines, all of which have some uncontained text and unmatched HTML tags (`<br />`), along with server code and matched HTML tags.</span></span> <span data-ttu-id="1408f-257">Znovu, může také předcházet každý řádek zvlášť `@:` operátor; buď způsob, jak funguje.</span><span class="sxs-lookup"><span data-stu-id="1408f-257">Again, you could also precede each line individually with the `@:` operator; either way works.</span></span>

    > [!NOTE]
    > <span data-ttu-id="1408f-258">Když text výstupu jak je znázorněno v této části &#8212; pomocí HTML elementu, `@:` operátor nebo `<text>` element &#8212; ASP.NET není s kódováním HTML výstup.</span><span class="sxs-lookup"><span data-stu-id="1408f-258">When you output text as shown in this section &#8212; using an HTML element, the `@:` operator, or the `<text>` element &#8212; ASP.NET doesn't HTML-encode the output.</span></span> <span data-ttu-id="1408f-259">(Jak je uvedeno výše, ASP.NET kódování výstup výrazů kódu na server a server bloky kódu, které předchází `@`, s výjimkou zvláštní případy, které jste si poznamenali v této části.)</span><span class="sxs-lookup"><span data-stu-id="1408f-259">(As noted earlier, ASP.NET does encode the output of server code expressions and server code blocks that are preceded by `@`, except in the special cases noted in this section.)</span></span>

### <a name="whitespace"></a><span data-ttu-id="1408f-260">Prázdné znaky</span><span class="sxs-lookup"><span data-stu-id="1408f-260">Whitespace</span></span>

<span data-ttu-id="1408f-261">Nadbytečné mezery v příkazu (i mimo něj řetězcový literál) nemají vliv – příkaz:</span><span class="sxs-lookup"><span data-stu-id="1408f-261">Extra spaces in a statement (and outside of a string literal) don't affect the statement:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample15.cshtml)]

<span data-ttu-id="1408f-262">Konec řádku v příkazu nemá žádný vliv na příkazu a příkazy pro lepší čitelnost může obtékat.</span><span class="sxs-lookup"><span data-stu-id="1408f-262">A line break in a statement has no effect on the statement, and you can wrap statements for readability.</span></span> <span data-ttu-id="1408f-263">Následující příkazy jsou stejné:</span><span class="sxs-lookup"><span data-stu-id="1408f-263">The following statements are the same:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample16.cshtml)]

<span data-ttu-id="1408f-264">Nelze však zalomení řádku uprostřed řetězcový literál.</span><span class="sxs-lookup"><span data-stu-id="1408f-264">However, you can't wrap a line in the middle of a string literal.</span></span> <span data-ttu-id="1408f-265">Nebude fungovat v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="1408f-265">The following example doesn't work:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample17.cshtml)]

<span data-ttu-id="1408f-266">Kombinování dlouhý řetězec, který obtéká stejně jako ve výše uvedeném kódu více řádků, existují dvě možnosti.</span><span class="sxs-lookup"><span data-stu-id="1408f-266">To combine a long string that wraps to multiple lines like the above code, there are two options.</span></span> <span data-ttu-id="1408f-267">Můžete použít operátor zřetězení (`+`), což uvidíte dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="1408f-267">You can use the concatenation operator (`+`), which you'll see later in this article.</span></span> <span data-ttu-id="1408f-268">Můžete také použít `@` znak pro vytvoření doslovný řetězec literálu, protože jste viděli dříve v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="1408f-268">You can also use the `@` character to create a verbatim string literal, as you saw earlier in this article.</span></span> <span data-ttu-id="1408f-269">Můžete přerušit doslovný řetězec literálů na řádcích:</span><span class="sxs-lookup"><span data-stu-id="1408f-269">You can break verbatim string literals across lines:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample18.cshtml)]

### <a name="code-and-markup-comments"></a><span data-ttu-id="1408f-270">Kód (a značky) komentáře</span><span class="sxs-lookup"><span data-stu-id="1408f-270">Code (and Markup) Comments</span></span>

<span data-ttu-id="1408f-271">Komentáře umožňují ponechte poznámky k sobě nebo jiné.</span><span class="sxs-lookup"><span data-stu-id="1408f-271">Comments let you leave notes for yourself or others.</span></span> <span data-ttu-id="1408f-272">Také umožňují zakázat (*okomentujte*) část kódu nebo značky, které nechcete, aby ke spuštění, ale chcete zachovat na stránce prozatím.</span><span class="sxs-lookup"><span data-stu-id="1408f-272">They also allow you to disable (*comment out*) a section of code or markup that you don't want to run but want to keep in your page for the time being.</span></span>

<span data-ttu-id="1408f-273">Existuje jiný komentáře syntaxe Razor kód a kód HTML.</span><span class="sxs-lookup"><span data-stu-id="1408f-273">There's different commenting syntax for Razor code and for HTML markup.</span></span> <span data-ttu-id="1408f-274">Stejně jako u všech kódu Razor, komentáře syntaxe Razor jsou zpracování (a potom se odeberou) na serveru před stránky je odesláno prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="1408f-274">As with all Razor code, Razor comments are processed (and then removed) on the server before the page is sent to the browser.</span></span> <span data-ttu-id="1408f-275">Proto přinesly syntaxe Razor umožňuje umístit komentáře do kódu (nebo dokonce do kódu), která se zobrazí, když upravíte soubor, ale uživatelé nezobrazí, i v zdroje stránky.</span><span class="sxs-lookup"><span data-stu-id="1408f-275">Therefore, the Razor commenting syntax lets you put comments into the code (or even into the markup) that you can see when you edit the file, but that users don't see, even in the page source.</span></span>

<span data-ttu-id="1408f-276">Pro komentáře syntaxe Razor rozhraní ASP.NET, spusťte komentář `@*` a ukončit s `*@`.</span><span class="sxs-lookup"><span data-stu-id="1408f-276">For ASP.NET Razor comments, you start the comment with `@*` and end it with `*@`.</span></span> <span data-ttu-id="1408f-277">Komentář může být na jeden nebo více řádků:</span><span class="sxs-lookup"><span data-stu-id="1408f-277">The comment can be on one line or multiple lines:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample19.cshtml)]

<span data-ttu-id="1408f-278">Tady je komentář v rámci bloku kódu:</span><span class="sxs-lookup"><span data-stu-id="1408f-278">Here is a comment within a code block:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample20.cshtml)]

<span data-ttu-id="1408f-279">Tady je stejného bloku kódu, s řádkem kódu označené jako komentář tak, aby na něm nebudou spouštět:</span><span class="sxs-lookup"><span data-stu-id="1408f-279">Here is the same block of code, with the line of code commented out so that it won't run:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample21.cshtml)]

<span data-ttu-id="1408f-280">Uvnitř bloku kódu jako alternativu k použití komentáře syntaxe Razor, můžete použít přinesly syntaxe programovací jazyk, který používáte, jako je C#:</span><span class="sxs-lookup"><span data-stu-id="1408f-280">Inside a code block, as an alternative to using Razor comment syntax, you can use the commenting syntax of the programming language you're using, such as C#:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample22.cshtml)]

<span data-ttu-id="1408f-281">V jazyce C#, Jednořádkové komentáře předchází `//` znaků a víceřádkových komentářů začínat `/*` a na konci `*/`.</span><span class="sxs-lookup"><span data-stu-id="1408f-281">In C#, single-line comments are preceded by the `//` characters, and multi-line comments begin with `/*` and end with `*/`.</span></span> <span data-ttu-id="1408f-282">(Stejně jako u komentáře syntaxe Razor, C# komentáře se zobrazí v prohlížeči.)</span><span class="sxs-lookup"><span data-stu-id="1408f-282">(As with Razor comments, C# comments are not rendered to the browser.)</span></span>

<span data-ttu-id="1408f-283">Pro značky pravděpodobně znáte, můžete vytvořit komentář HTML:</span><span class="sxs-lookup"><span data-stu-id="1408f-283">For markup, as you probably know, you can create an HTML comment:</span></span>

[!code-xml[Main](introducing-razor-syntax-c/samples/sample23.xml)]

<span data-ttu-id="1408f-284">Komentáře HTML začínat `<!--` znaků a končit `-->`.</span><span class="sxs-lookup"><span data-stu-id="1408f-284">HTML comments start with `<!--` characters and end with `-->`.</span></span> <span data-ttu-id="1408f-285">Komentáře ve formátu HTML můžete použít ohraničit pouze text, ale také všechny značky HTML, které si chtít zachovat na stránce, ale nechcete k vykreslení.</span><span class="sxs-lookup"><span data-stu-id="1408f-285">You can use HTML comments to surround not only text, but also any HTML markup that you may want to keep in the page but don't want to render.</span></span> <span data-ttu-id="1408f-286">Tento komentář HTML se skrýt celý obsah značky a text, který obsahují:</span><span class="sxs-lookup"><span data-stu-id="1408f-286">This HTML comment will hide the entire content of the tags and the text they contain:</span></span>

[!code-html[Main](introducing-razor-syntax-c/samples/sample24.html)]

<span data-ttu-id="1408f-287">Na rozdíl od komentáře syntaxe Razor, komentáře HTML *jsou* vykreslen na stránce a uživatel můžete zobrazit podle zdrojové stránky.</span><span class="sxs-lookup"><span data-stu-id="1408f-287">Unlike Razor comments, HTML comments *are* rendered to the page and the user can see them by viewing the page source.</span></span>

<span data-ttu-id="1408f-288">Razor má omezení na vnořené bloky jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="1408f-288">Razor has limitations on nested blocks of C#.</span></span> <span data-ttu-id="1408f-289">Další informace najdete v části [s názvem proměnné jazyka C# a vnořené bloky generovat nefunkční kód](http://aspnetwebstack.codeplex.com/workitem/1914)</span><span class="sxs-lookup"><span data-stu-id="1408f-289">For more information see [Named C# Variables and Nested Blocks Generate Broken Code](http://aspnetwebstack.codeplex.com/workitem/1914)</span></span>

## <a name="variables"></a><span data-ttu-id="1408f-290">Proměnné</span><span class="sxs-lookup"><span data-stu-id="1408f-290">Variables</span></span>

<span data-ttu-id="1408f-291">Proměnná je pojmenovaný objekt, který použijete k ukládání dat.</span><span class="sxs-lookup"><span data-stu-id="1408f-291">A variable is a named object that you use to store data.</span></span> <span data-ttu-id="1408f-292">Můžete pojmenovat proměnné cokoli, ale název musí začínat znakem abecedy a nesmí obsahovat prázdné znaky nebo vyhrazené znaky.</span><span class="sxs-lookup"><span data-stu-id="1408f-292">You can name variables anything, but the name must begin with an alphabetic character and it cannot contain whitespace or reserved characters.</span></span>

### <a name="variables-and-data-types"></a><span data-ttu-id="1408f-293">Datové typy a proměnné</span><span class="sxs-lookup"><span data-stu-id="1408f-293">Variables and Data Types</span></span>

<span data-ttu-id="1408f-294">Proměnná může mít určitý datový typ, který označuje, jaký druh dat je uložen v proměnné.</span><span class="sxs-lookup"><span data-stu-id="1408f-294">A variable can have a specific data type, which indicates what kind of data is stored in the variable.</span></span> <span data-ttu-id="1408f-295">Můžete použít proměnné řetězce, které ukládají hodnoty řetězce (například &quot;Hello world&quot;), celočíselné proměnné, které ukládají hodnoty celé číslo (např. 3 nebo 79) a proměnné pro datum, které ukládají data v různých formátech (např. 4/12/2012 nebo března 2009 ).</span><span class="sxs-lookup"><span data-stu-id="1408f-295">You can have string variables that store string values (like &quot;Hello world&quot;), integer variables that store whole-number values (like 3 or 79), and date variables that store date values in a variety of formats (like 4/12/2012 or March 2009).</span></span> <span data-ttu-id="1408f-296">A existuje mnoho dalších typů dat, která vám pomůže.</span><span class="sxs-lookup"><span data-stu-id="1408f-296">And there are many other data types you can use.</span></span>

<span data-ttu-id="1408f-297">Však obvykle není nutné zadat typ pro proměnnou.</span><span class="sxs-lookup"><span data-stu-id="1408f-297">However, you generally don't have to specify a type for a variable.</span></span> <span data-ttu-id="1408f-298">Ve většině případů, technologie ASP.NET můžete zjistit typ závislosti na tom, jak se používá data v proměnné.</span><span class="sxs-lookup"><span data-stu-id="1408f-298">Most of the time, ASP.NET can figure out the type based on how the data in the variable is being used.</span></span> <span data-ttu-id="1408f-299">(Někdy je nutné zadat typ; uvidíte příkladech, kde je hodnota true.)</span><span class="sxs-lookup"><span data-stu-id="1408f-299">(Occasionally you must specify a type; you'll see examples where this is true.)</span></span>

<span data-ttu-id="1408f-300">Deklarujte proměnné pomocí `var` – klíčové slovo (Pokud nechcete zadat typ) nebo pomocí názvu typu:</span><span class="sxs-lookup"><span data-stu-id="1408f-300">You declare a variable using the `var` keyword (if you don't want to specify a type) or by using the name of the type:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample25.cshtml)]

<span data-ttu-id="1408f-301">Následující příklad ukazuje některé typické použití proměnných na webové stránce:</span><span class="sxs-lookup"><span data-stu-id="1408f-301">The following example shows some typical uses of variables in a web page:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample26.cshtml)]

<span data-ttu-id="1408f-302">Pokud kombinujete v předchozích příkladech na stránce, uvidíte toto zobrazí v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="1408f-302">If you combine the previous examples in a page, you see this displayed in a browser:</span></span>

![Razor Img9](introducing-razor-syntax-c/_static/image9.jpg)

### <a name="converting-and-testing-data-types"></a><span data-ttu-id="1408f-304">Převod a testování datových typů</span><span class="sxs-lookup"><span data-stu-id="1408f-304">Converting and Testing Data Types</span></span>

<span data-ttu-id="1408f-305">Přestože ASP.NET obvykle můžete automaticky určit typ dat, někdy se to neplatí.</span><span class="sxs-lookup"><span data-stu-id="1408f-305">Although ASP.NET can usually determine a data type automatically, sometimes it can't.</span></span> <span data-ttu-id="1408f-306">Proto můžete potřebovat pomoc ASP.NET pomocí provádí explicitní převod.</span><span class="sxs-lookup"><span data-stu-id="1408f-306">Therefore, you might need to help ASP.NET out by performing an explicit conversion.</span></span> <span data-ttu-id="1408f-307">I když nemáte k dispozici pro převod typů, někdy je vhodné otestovat, jaký typ dat je možné, že pracujete s.</span><span class="sxs-lookup"><span data-stu-id="1408f-307">Even if you don't have to convert types, sometimes it's helpful to test to see what type of data you might be working with.</span></span>

<span data-ttu-id="1408f-308">Nejběžnější případ je, že budete muset převést řetězec na jiný typ, například na celé číslo nebo datum.</span><span class="sxs-lookup"><span data-stu-id="1408f-308">The most common case is that you have to convert a string to another type, such as to an integer or date.</span></span> <span data-ttu-id="1408f-309">Následující příklad ukazuje typické případy, kdy je nutné převést řetězec na číslo.</span><span class="sxs-lookup"><span data-stu-id="1408f-309">The following example shows a typical case where you must convert a string to a number.</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample27.cshtml)]

<span data-ttu-id="1408f-310">Jako pravidlo uživatelský vstup je pro vás řetězce.</span><span class="sxs-lookup"><span data-stu-id="1408f-310">As a rule, user input comes to you as strings.</span></span> <span data-ttu-id="1408f-311">I v případě, že výzva uživateli k zadání čísla a i v případě, že jste zadali číslici, při odeslání vstup uživatele a čtení v kódu, data jsou ve formátu řetězce.</span><span class="sxs-lookup"><span data-stu-id="1408f-311">Even if you've prompted users to enter a number, and even if they've entered a digit, when user input is submitted and you read it in code, the data is in string format.</span></span> <span data-ttu-id="1408f-312">Proto je nutné převést řetězec na číslo.</span><span class="sxs-lookup"><span data-stu-id="1408f-312">Therefore, you must convert the string to a number.</span></span> <span data-ttu-id="1408f-313">V příkladu při pokusu o provedení aritmetické operace na hodnotách bez převodu, chybová zpráva výsledky, protože ASP.NET nejde přidat dva řetězce:</span><span class="sxs-lookup"><span data-stu-id="1408f-313">In the example, if you try to perform arithmetic on the values without converting them, the following error results, because ASP.NET cannot add two strings:</span></span>

<span data-ttu-id="1408f-314">*Typ "řetězec" na 'int' nelze implicitně převést.*</span><span class="sxs-lookup"><span data-stu-id="1408f-314">*Cannot implicitly convert type 'string' to 'int'.*</span></span>

<span data-ttu-id="1408f-315">K převodu hodnoty na celá čísla, volání `AsInt` metody.</span><span class="sxs-lookup"><span data-stu-id="1408f-315">To convert the values to integers, you call the `AsInt` method.</span></span> <span data-ttu-id="1408f-316">Pokud převod není úspěšné, pak přidáte čísla.</span><span class="sxs-lookup"><span data-stu-id="1408f-316">If the conversion is successful, you can then add the numbers.</span></span>

<span data-ttu-id="1408f-317">Následující tabulka uvádí některé běžné metody převodu a testování pro proměnné.</span><span class="sxs-lookup"><span data-stu-id="1408f-317">The following table lists some common conversion and test methods for variables.</span></span>

:::row:::
    :::column:::
    <span data-ttu-id="1408f-318"><strong>– Metoda</strong></span><span class="sxs-lookup"><span data-stu-id="1408f-318"><strong>Method</strong></span></span>
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-319"><strong>Popis</strong></span><span class="sxs-lookup"><span data-stu-id="1408f-319"><strong>Description</strong></span></span>
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-320"><strong>Příklad</strong></span><span class="sxs-lookup"><span data-stu-id="1408f-320"><strong>Example</strong></span></span>
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `AsInt(), IsInt()`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-321">Převede řetězec představující celé číslo (např. "593") na celé číslo.</span><span class="sxs-lookup"><span data-stu-id="1408f-321">Converts a string that represents a whole number (like "593") to an integer.</span></span>
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample28.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `AsBool(), IsBool()`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-322">Převede řetězec jako &quot;true&quot; nebo &quot;false&quot; s typem Boolean.</span><span class="sxs-lookup"><span data-stu-id="1408f-322">Converts a string like &quot;true&quot; or &quot;false&quot; to a Boolean type.</span></span>
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample29.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `AsFloat(), IsFloat()`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-323">Převede řetězec obsahující desetinná hodnota jako &quot;1.3&quot; nebo &quot;7.439&quot; na číslo s plovoucí desetinnou čárkou.</span><span class="sxs-lookup"><span data-stu-id="1408f-323">Converts a string that has a decimal value like &quot;1.3&quot; or &quot;7.439&quot; to a floating-point number.</span></span>
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample30.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `AsDecimal(), IsDecimal()`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-324">Převede řetězec obsahující desetinná hodnota jako &quot;1.3&quot; nebo &quot;7.439&quot; na desetinné číslo.</span><span class="sxs-lookup"><span data-stu-id="1408f-324">Converts a string that has a decimal value like &quot;1.3&quot; or &quot;7.439&quot; to a decimal number.</span></span> <span data-ttu-id="1408f-325">(V technologii ASP.NET je přesnější než číslo s plovoucí desetinnou čárkou desetinné číslo.)</span><span class="sxs-lookup"><span data-stu-id="1408f-325">(In ASP.NET, a decimal number is more precise than a floating-point number.)</span></span>
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample31.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `AsDateTime(), IsDateTime()`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-326">Převede řetězec představující hodnotu data a času na ASP.NET `DateTime` typu.</span><span class="sxs-lookup"><span data-stu-id="1408f-326">Converts a string that represents a date and time value to the ASP.NET `DateTime` type.</span></span>
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample32.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `ToString()`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-327">Převede jakýkoli jiný typ dat na řetězec.</span><span class="sxs-lookup"><span data-stu-id="1408f-327">Converts any other data type to a string.</span></span>
    :::column-end:::
    :::column:::
        [!code-javascript[Main](introducing-razor-syntax-c/samples/sample33.js)]
    :::column-end:::
:::row-end:::

## <a name="operators"></a><span data-ttu-id="1408f-328">Operátory</span><span class="sxs-lookup"><span data-stu-id="1408f-328">Operators</span></span>

<span data-ttu-id="1408f-329">Operátor je klíčové slovo nebo znak, který říká technologie ASP.NET, jaký druh příkaz k provedení ve výrazu.</span><span class="sxs-lookup"><span data-stu-id="1408f-329">An operator is a keyword or character that tells ASP.NET what kind of command to perform in an expression.</span></span> <span data-ttu-id="1408f-330">Jazyk C# (a syntaxi Razor, která je založena na něm) podporuje mnoho operátorů, ale je potřeba jenom rozpoznat pár, abyste mohli začít.</span><span class="sxs-lookup"><span data-stu-id="1408f-330">The C# language (and the Razor syntax that's based on it) supports many operators, but you only need to recognize a few to get started.</span></span> <span data-ttu-id="1408f-331">Následující tabulka shrnuje nejčastější operátory.</span><span class="sxs-lookup"><span data-stu-id="1408f-331">The following table summarizes the most common operators.</span></span>


:::row:::
    :::column:::
    <span data-ttu-id="1408f-332"><strong>– Operátor</strong></span><span class="sxs-lookup"><span data-stu-id="1408f-332"><strong>Operator</strong></span></span>
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-333"><strong>Popis</strong></span><span class="sxs-lookup"><span data-stu-id="1408f-333"><strong>Description</strong></span></span>
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-334"><strong>Příklady</strong></span><span class="sxs-lookup"><span data-stu-id="1408f-334"><strong>Examples</strong></span></span>
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `+` `-` `*` `/`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-335">Matematické operátory používat ve výrazech pro číselná.</span><span class="sxs-lookup"><span data-stu-id="1408f-335">Math operators used in numerical expressions.</span></span>
    :::column-end:::
    :::column:::
        [!code-css[Main](introducing-razor-syntax-c/samples/sample34.css)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `=`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-336">Přiřazení.</span><span class="sxs-lookup"><span data-stu-id="1408f-336">Assignment.</span></span> <span data-ttu-id="1408f-337">Přiřadí hodnotu na pravé straně příkazu na objekt na levé straně.</span><span class="sxs-lookup"><span data-stu-id="1408f-337">Assigns the value on the right side of a statement to the object on the left side.</span></span>
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample35.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `==`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-338">Rovnost.</span><span class="sxs-lookup"><span data-stu-id="1408f-338">Equality.</span></span> <span data-ttu-id="1408f-339">Vrátí `true` Pokud jsou hodnoty stejné.</span><span class="sxs-lookup"><span data-stu-id="1408f-339">Returns `true` if the values are equal.</span></span> <span data-ttu-id="1408f-340">(Všimněte si rozdílu mezi `=` operátor a `==` operátor.)</span><span class="sxs-lookup"><span data-stu-id="1408f-340">(Notice the distinction between the `=` operator and the `==` operator.)</span></span>
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample36.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `!=`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-341">Nerovnost.</span><span class="sxs-lookup"><span data-stu-id="1408f-341">Inequality.</span></span> <span data-ttu-id="1408f-342">Vrátí `true` Pokud hodnoty nejsou shodné.</span><span class="sxs-lookup"><span data-stu-id="1408f-342">Returns `true` if the values are not equal.</span></span>
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample37.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `< > <= >=`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-343">Less – než, větší-než, menší než – nebo se znaménkem rovná a větší než nebo rovno.</span><span class="sxs-lookup"><span data-stu-id="1408f-343">Less-than, greater-than, less-than-or-equal, and greater-than-or-equal.</span></span>
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample38.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `+`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-344">Zřetězení, který se používá pro připojení řetězce.</span><span class="sxs-lookup"><span data-stu-id="1408f-344">Concatenation, which is used to join strings.</span></span> <span data-ttu-id="1408f-345">ASP.NET ví rozdíl mezi tento operátor a operátor sčítání na základě datového typu výrazu.</span><span class="sxs-lookup"><span data-stu-id="1408f-345">ASP.NET knows the difference between this operator and the addition operator based on the data type of the expression.</span></span>
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample39.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `+=` `-=`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-346">Přírůstek a snížení operátory, které operátorů sčítání a odečítání 1 (v uvedeném pořadí) z proměnné.</span><span class="sxs-lookup"><span data-stu-id="1408f-346">The increment and decrement operators, which add and subtract 1 (respectively) from a variable.</span></span>
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample40.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `.`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-347">Tečka.</span><span class="sxs-lookup"><span data-stu-id="1408f-347">Dot.</span></span> <span data-ttu-id="1408f-348">Použít k rozlišení objekty a jejich vlastnosti a metody.</span><span class="sxs-lookup"><span data-stu-id="1408f-348">Used to distinguish objects and their properties and methods.</span></span>
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample41.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `()`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-349">Závorky.</span><span class="sxs-lookup"><span data-stu-id="1408f-349">Parentheses.</span></span> <span data-ttu-id="1408f-350">Použít skupinové výrazy a předat parametry metody.</span><span class="sxs-lookup"><span data-stu-id="1408f-350">Used to group expressions and to pass parameters to methods.</span></span>
    :::column-end:::
    :::column:::
        [!code-javascript[Main](introducing-razor-syntax-c/samples/sample42.js)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `[]`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-351">Složené závorky.</span><span class="sxs-lookup"><span data-stu-id="1408f-351">Brackets.</span></span> <span data-ttu-id="1408f-352">Používá pro přístup k hodnoty pole nebo kolekce.</span><span class="sxs-lookup"><span data-stu-id="1408f-352">Used for accessing values in arrays or collections.</span></span>
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample43.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `!`
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-353">Není.</span><span class="sxs-lookup"><span data-stu-id="1408f-353">Not.</span></span> <span data-ttu-id="1408f-354">Obrátí `true` hodnota, která se `false` a naopak.</span><span class="sxs-lookup"><span data-stu-id="1408f-354">Reverses a `true` value to `false` and vice versa.</span></span> <span data-ttu-id="1408f-355">Obvykle se používá jako zjednodušený způsob, jak otestovat pro `false` (to znamená pro není `true`).</span><span class="sxs-lookup"><span data-stu-id="1408f-355">Typically used as a shorthand way to test for `false` (that is, for not `true`).</span></span>
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample44.cs)]
    :::column-end:::
:::row-end:::
* * *
:::row:::
    :::column:::
        `&&` <code>&#124;&#124;</code>
    :::column-end:::
    :::column:::
    <span data-ttu-id="1408f-356">Logický operátor AND a které se používají k propojení podmínky společně.</span><span class="sxs-lookup"><span data-stu-id="1408f-356">Logical AND and OR, which are used to link conditions together.</span></span>
    :::column-end:::
    :::column:::
        [!code-csharp[Main](introducing-razor-syntax-c/samples/sample45.cs)]
    :::column-end:::
:::row-end:::

<a id="ID_WorkingWithFileAndFolderPaths"></a>
## <a name="working-with-file-and-folder-paths-in-code"></a><span data-ttu-id="1408f-357">Práce s souboru a cesty ke složkám v kódu</span><span class="sxs-lookup"><span data-stu-id="1408f-357">Working with File and Folder Paths in Code</span></span>

<span data-ttu-id="1408f-358">Často budete pracovat s cestami k souborům a složkám v kódu.</span><span class="sxs-lookup"><span data-stu-id="1408f-358">You'll often work with file and folder paths in your code.</span></span> <span data-ttu-id="1408f-359">Tady je příklad struktury fyzické složce pro web, jak se může zobrazovat ve svém vývojovém počítači:</span><span class="sxs-lookup"><span data-stu-id="1408f-359">Here is an example of physical folder structure for a website as it might appear on your development computer:</span></span>

`C:\WebSites\MyWebSite default.cshtml datafile.txt \images Logo.jpg \styles Styles.css`

<span data-ttu-id="1408f-360">Tady jsou některé důležité podrobnosti o adresy URL a cesty:</span><span class="sxs-lookup"><span data-stu-id="1408f-360">Here are some essential details about URLs and paths:</span></span>

- <span data-ttu-id="1408f-361">Adresa URL začíná na buď název domény (`http://www.example.com`) nebo název serveru (`http://localhost`, `http://mycomputer`).</span><span class="sxs-lookup"><span data-stu-id="1408f-361">A URL begins with either a domain name (`http://www.example.com`) or a server name (`http://localhost`, `http://mycomputer`).</span></span>
- <span data-ttu-id="1408f-362">Adresa URL odpovídá fyzickou cestu na hostitelském počítači.</span><span class="sxs-lookup"><span data-stu-id="1408f-362">A URL corresponds to a physical path on a host computer.</span></span> <span data-ttu-id="1408f-363">Například `http://myserver` může odpovídat složce *C:\websites\mywebsite* na serveru.</span><span class="sxs-lookup"><span data-stu-id="1408f-363">For example, `http://myserver` might correspond to the folder *C:\websites\mywebsite* on the server.</span></span>
- <span data-ttu-id="1408f-364">Virtuální cesta je zkratka pro reprezentaci cest v kódu bez nutnosti zadávat úplnou cestu.</span><span class="sxs-lookup"><span data-stu-id="1408f-364">A virtual path is shorthand to represent paths in code without having to specify the full path.</span></span> <span data-ttu-id="1408f-365">Obsahuje části adresy URL, která následuje název domény nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="1408f-365">It includes the portion of a URL that follows the domain or server name.</span></span> <span data-ttu-id="1408f-366">Při použití virtuální cesty, můžete přesunout kód do jiné domény nebo serveru bez nutnosti aktualizovat cesty.</span><span class="sxs-lookup"><span data-stu-id="1408f-366">When you use virtual paths, you can move your code to a different domain or server without having to update the paths.</span></span>

<span data-ttu-id="1408f-367">Tady je příklad, který vám pomůže pochopit rozdíly:</span><span class="sxs-lookup"><span data-stu-id="1408f-367">Here's an example to help you understand the differences:</span></span>

| <span data-ttu-id="1408f-368">Úplnou adresu URL</span><span class="sxs-lookup"><span data-stu-id="1408f-368">Complete URL</span></span> | `http://mycompanyserver/humanresources/CompanyPolicy.htm` |
| --- | --- |
| <span data-ttu-id="1408f-369">Název serveru</span><span class="sxs-lookup"><span data-stu-id="1408f-369">Server name</span></span> | <span data-ttu-id="1408f-370">*mycompanyserver*</span><span class="sxs-lookup"><span data-stu-id="1408f-370">*mycompanyserver*</span></span> |
| <span data-ttu-id="1408f-371">Virtuální cesta</span><span class="sxs-lookup"><span data-stu-id="1408f-371">Virtual path</span></span> | <span data-ttu-id="1408f-372">*/humanresources/CompanyPolicy.htm*</span><span class="sxs-lookup"><span data-stu-id="1408f-372">*/humanresources/CompanyPolicy.htm*</span></span> |
| <span data-ttu-id="1408f-373">Fyzická cesta</span><span class="sxs-lookup"><span data-stu-id="1408f-373">Physical path</span></span> | <span data-ttu-id="1408f-374">*C:\mywebsites\humanresources\CompanyPolicy.htm*</span><span class="sxs-lookup"><span data-stu-id="1408f-374">*C:\mywebsites\humanresources\CompanyPolicy.htm*</span></span> |

<span data-ttu-id="1408f-375">Virtuální kořenový adresář je /, stejně jako kořenový C: jednotka je \.</span><span class="sxs-lookup"><span data-stu-id="1408f-375">The virtual root is /, just like the root of your C: drive is \.</span></span> <span data-ttu-id="1408f-376">(Virtuální složka cesty vždy používat lomítka.) Virtuální cesta ke složce nemusí mít stejný název jako fyzické složce. může se jednat jako alias.</span><span class="sxs-lookup"><span data-stu-id="1408f-376">(Virtual folder paths always use forward slashes.) The virtual path of a folder doesn't have to have the same name as the physical folder; it can be an alias.</span></span> <span data-ttu-id="1408f-377">(Na provozních serverech virtuální cesta zřídka odpovídá přesně fyzickou cestu.)</span><span class="sxs-lookup"><span data-stu-id="1408f-377">(On production servers, the virtual path rarely matches an exact physical path.)</span></span>

<span data-ttu-id="1408f-378">Při práci se soubory a složkami v kódu, někdy potřebujete odkazovat na fyzickou cestu a někdy virtuální cesty, v závislosti na tom, jaké objekty pracujete s.</span><span class="sxs-lookup"><span data-stu-id="1408f-378">When you work with files and folders in code, sometimes you need to reference the physical path and sometimes a virtual path, depending on what objects you're working with.</span></span> <span data-ttu-id="1408f-379">Technologie ASP.NET poskytuje tyto nástroje pro práci s cestami k souborům a složkám v kódu: `Server.MapPath` metody a `~` operátor a `Href` metoda.</span><span class="sxs-lookup"><span data-stu-id="1408f-379">ASP.NET gives you these tools for working with file and folder paths in code: the `Server.MapPath` method, and the `~` operator and `Href` method.</span></span>

### <a name="converting-virtual-to-physical-paths-the-servermappath-method"></a><span data-ttu-id="1408f-380">Převod virtuálních a fyzických cest: Server.MapPath – metoda</span><span class="sxs-lookup"><span data-stu-id="1408f-380">Converting virtual to physical paths: the Server.MapPath method</span></span>

<span data-ttu-id="1408f-381">`Server.MapPath` Metoda převede virtuální cestu (například */default.cshtml*) na absolutní cestu, fyzické (jako *C:\WebSites\MyWebSiteFolder\default.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="1408f-381">The `Server.MapPath` method converts a virtual path (like */default.cshtml*) to an absolute physical path (like *C:\WebSites\MyWebSiteFolder\default.cshtml*).</span></span> <span data-ttu-id="1408f-382">Tuto metodu použijte kdykoli potřebujete úplná fyzická cesta.</span><span class="sxs-lookup"><span data-stu-id="1408f-382">You use this method any time you need a complete physical path.</span></span> <span data-ttu-id="1408f-383">Typickým příkladem je při čtení nebo zápis textový soubor nebo soubor obrázku na webovém serveru.</span><span class="sxs-lookup"><span data-stu-id="1408f-383">A typical example is when you're reading or writing a text file or image file on the web server.</span></span>

<span data-ttu-id="1408f-384">Obvykle neznáte absolutní fyzická cesta k webu na serveru pro hostování lokality, abyste této metody můžete převést cestu si jisti, virtuální cestu – do příslušné cesty na serveru za vás.</span><span class="sxs-lookup"><span data-stu-id="1408f-384">You typically don't know the absolute physical path of your site on a hosting site's server, so this method can convert the path you do know — the virtual path — to the corresponding path on the server for you.</span></span> <span data-ttu-id="1408f-385">Předat virtuální cestu k souboru nebo složky na metodu a vrátí fyzickou cestu:</span><span class="sxs-lookup"><span data-stu-id="1408f-385">You pass the virtual path to a file or folder to the method, and it returns the physical path:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample46.cshtml)]

### <a name="referencing-the-virtual-root-the--operator-and-href-method"></a><span data-ttu-id="1408f-386">Odkazování na virtuální kořenový adresář: ~ – operátor a metoda Href</span><span class="sxs-lookup"><span data-stu-id="1408f-386">Referencing the virtual root: the ~ operator and Href method</span></span>

<span data-ttu-id="1408f-387">V *.cshtml* nebo *.vbhtml* soubor, můžete odkazovat pomocí cesty kořenové složky `~` operátor.</span><span class="sxs-lookup"><span data-stu-id="1408f-387">In a *.cshtml* or *.vbhtml* file, you can reference the virtual root path using the `~` operator.</span></span> <span data-ttu-id="1408f-388">To je velmi užitečný, protože se můžete pohybovat stránky na webu a jakékoli odkazy, které obsahují další stránky nesmí být nefunkční.</span><span class="sxs-lookup"><span data-stu-id="1408f-388">This is very handy because you can move pages around in a site, and any links they contain to other pages won't be broken.</span></span> <span data-ttu-id="1408f-389">Je taky užitečný v případě, kdy přesunout svůj web do jiného umístění.</span><span class="sxs-lookup"><span data-stu-id="1408f-389">It's also handy in case you ever move your website to a different location.</span></span> <span data-ttu-id="1408f-390">Následuje několik příkladů:</span><span class="sxs-lookup"><span data-stu-id="1408f-390">Here are some examples:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample47.cshtml)]

<span data-ttu-id="1408f-391">Pokud webová stránka se `http://myserver/myapp`, zde je, jak ASP.NET bude zpracovávat tyto cesty při spuštění stránky:</span><span class="sxs-lookup"><span data-stu-id="1408f-391">If the website is `http://myserver/myapp`, here's how ASP.NET will treat these paths when the page runs:</span></span>

- <span data-ttu-id="1408f-392">`myImagesFolder`: `http://myserver/myapp/images`</span><span class="sxs-lookup"><span data-stu-id="1408f-392">`myImagesFolder`: `http://myserver/myapp/images`</span></span>
- <span data-ttu-id="1408f-393">`myStyleSheet` : `http://myserver/myapp/styles/Stylesheet.css`</span><span class="sxs-lookup"><span data-stu-id="1408f-393">`myStyleSheet` : `http://myserver/myapp/styles/Stylesheet.css`</span></span>

<span data-ttu-id="1408f-394">(Ve skutečnosti neuvidíte těchto cest jako hodnotu proměnné, ale ASP.NET bude zacházet s cesty, jak, pokud je to, co byly.)</span><span class="sxs-lookup"><span data-stu-id="1408f-394">(You won't actually see these paths as the values of the variable, but ASP.NET will treat the paths as if that's what they were.)</span></span>

<span data-ttu-id="1408f-395">Můžete použít `~` operátor v serverovém kódu (jak je uvedeno výše) a v kódu, například takto:</span><span class="sxs-lookup"><span data-stu-id="1408f-395">You can use the `~` operator both in server code (as above) and in markup, like this:</span></span>

[!code-html[Main](introducing-razor-syntax-c/samples/sample48.html)]

<span data-ttu-id="1408f-396">V kódu, můžete použít `~` operátor k vytvoření cesty k prostředkům, jako jsou soubory obrázků, jiné webové stránky a souborů CSS.</span><span class="sxs-lookup"><span data-stu-id="1408f-396">In markup, you use the `~` operator to create paths to resources like image files, other web pages, and CSS files.</span></span> <span data-ttu-id="1408f-397">Při spuštění stránky ASP.NET vypadat prostřednictvím stránky (kódu a kódu) a řeší všechny `~` odkazy na příslušné cesty.</span><span class="sxs-lookup"><span data-stu-id="1408f-397">When the page runs, ASP.NET looks through the page (both code and markup) and resolves all the `~` references to the appropriate path.</span></span>

## <a name="conditional-logic-and-loops"></a><span data-ttu-id="1408f-398">Podmíněnou logiku a smyčky</span><span class="sxs-lookup"><span data-stu-id="1408f-398">Conditional Logic and Loops</span></span>

<span data-ttu-id="1408f-399">Kód serveru ASP.NET vám umožní provádět úkoly na základě podmínek a napsat kód, který se opakuje příkazy s konkrétním počtem opakování (to znamená, že kód, který běží smyčky).</span><span class="sxs-lookup"><span data-stu-id="1408f-399">ASP.NET server code lets you perform tasks based on conditions and write code that repeats statements a specific number of times (that is, code that runs a loop).</span></span>

### <a name="testing-conditions"></a><span data-ttu-id="1408f-400">Testování podmínek</span><span class="sxs-lookup"><span data-stu-id="1408f-400">Testing Conditions</span></span>

<span data-ttu-id="1408f-401">K otestování jednoduché podmínky použití `if` příkaz, který vrátí hodnotu true nebo false podle testu zadáte:</span><span class="sxs-lookup"><span data-stu-id="1408f-401">To test a simple condition you use the `if` statement, which returns true or false based on a test you specify:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample49.cshtml)]

<span data-ttu-id="1408f-402">`if` – Klíčové slovo začíná bloku.</span><span class="sxs-lookup"><span data-stu-id="1408f-402">The `if` keyword starts a block.</span></span> <span data-ttu-id="1408f-403">Skutečný test (podmínky) v závorkách a vrátí hodnotu true nebo false.</span><span class="sxs-lookup"><span data-stu-id="1408f-403">The actual test (condition) is in parentheses and returns true or false.</span></span> <span data-ttu-id="1408f-404">Příkazy, na kterých běží, je-li test true jsou uzavřeny ve složených závorkách.</span><span class="sxs-lookup"><span data-stu-id="1408f-404">The statements that run if the test is true are enclosed in braces.</span></span> <span data-ttu-id="1408f-405">`if` Výraz může obsahovat `else` blok, který určuje příkazy ke spuštění, pokud podmínka není splněna:</span><span class="sxs-lookup"><span data-stu-id="1408f-405">An `if` statement can include an `else` block that specifies statements to run if the condition is false:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample50.cshtml)]

<span data-ttu-id="1408f-406">Můžete přidat více podmínek použití `else if` blok:</span><span class="sxs-lookup"><span data-stu-id="1408f-406">You can add multiple conditions using an `else if` block:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample51.cshtml)]

<span data-ttu-id="1408f-407">V tomto příkladu, pokud první podmínka vyhodnocena jako v oblasti, pokud blok není true, `else if` podmínka je zaškrtnuté políčko.</span><span class="sxs-lookup"><span data-stu-id="1408f-407">In this example, if the first condition in the if block is not true, the `else if` condition is checked.</span></span> <span data-ttu-id="1408f-408">Pokud tato podmínka je splněna, příkazy v `else if` jsou spuštěny.</span><span class="sxs-lookup"><span data-stu-id="1408f-408">If that condition is met, the statements in the `else if` block are executed.</span></span> <span data-ttu-id="1408f-409">Pokud jsou splněna žádná z podmínek, příkazy v `else` jsou spuštěny.</span><span class="sxs-lookup"><span data-stu-id="1408f-409">If none of the conditions are met, the statements in the `else` block are executed.</span></span> <span data-ttu-id="1408f-410">Můžete přidat libovolný počet else if blokuje a pak zavřete s `else` blokovat, jako &quot;všechno ostatní&quot; podmínku.</span><span class="sxs-lookup"><span data-stu-id="1408f-410">You can add any number of else if blocks, and then close with an `else` block as the &quot;everything else&quot; condition.</span></span>

<span data-ttu-id="1408f-411">Chcete-li otestovat velký počet podmínek, použijte `switch` blok:</span><span class="sxs-lookup"><span data-stu-id="1408f-411">To test a large number of conditions, use a `switch` block:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample52.cshtml)]

<span data-ttu-id="1408f-412">K otestování hodnotu v závorkách (v tomto příkladu `weekday` proměnné).</span><span class="sxs-lookup"><span data-stu-id="1408f-412">The value to test is in parentheses (in the example, the `weekday` variable).</span></span> <span data-ttu-id="1408f-413">Používá každý samostatný test `case` příkaz, který končí dvojtečkou (:).</span><span class="sxs-lookup"><span data-stu-id="1408f-413">Each individual test uses a `case` statement that ends with a colon (:).</span></span> <span data-ttu-id="1408f-414">Pokud hodnota `case` příkazu se shoduje s hodnotou testu, spuštění kódu v tomto případě bloku.</span><span class="sxs-lookup"><span data-stu-id="1408f-414">If the value of a `case` statement matches the test value, the code in that case block is executed.</span></span> <span data-ttu-id="1408f-415">Zavřete každou case – příkaz s `break` příkazu.</span><span class="sxs-lookup"><span data-stu-id="1408f-415">You close each case statement with a `break` statement.</span></span> <span data-ttu-id="1408f-416">(Pokud jste zapomněli začlenit přerušení v každém `case` bloku kódu z dalších `case` bude také spustit příkaz.) A `switch` blok má často `default` příkaz jako poslední případ &quot;všechno ostatní&quot; možnost, která se spustí v případě, že žádný z ostatních případech není true.</span><span class="sxs-lookup"><span data-stu-id="1408f-416">(If you forget to include break in each `case` block, the code from the next `case` statement will run also.) A `switch` block often has a `default` statement as the last case for an &quot;everything else&quot; option that runs if none of the other cases are true.</span></span>

<span data-ttu-id="1408f-417">Výsledek poslední dva podmíněné bloky zobrazí v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="1408f-417">The result of the last two conditional blocks displayed in a browser:</span></span>

![Razor Img10](introducing-razor-syntax-c/_static/image10.jpg)

### <a name="looping-code"></a><span data-ttu-id="1408f-419">Opakování ve smyčce kódu</span><span class="sxs-lookup"><span data-stu-id="1408f-419">Looping Code</span></span>

<span data-ttu-id="1408f-420">Často je potřeba spustit stejné příkazy opakovaně.</span><span class="sxs-lookup"><span data-stu-id="1408f-420">You often need to run the same statements repeatedly.</span></span> <span data-ttu-id="1408f-421">To provedete tak, že opakování ve smyčce.</span><span class="sxs-lookup"><span data-stu-id="1408f-421">You do this by looping.</span></span> <span data-ttu-id="1408f-422">Například můžete často spustit stejné příkazy pro každou položku v kolekci dat.</span><span class="sxs-lookup"><span data-stu-id="1408f-422">For example, you often run the same statements for each item in a collection of data.</span></span> <span data-ttu-id="1408f-423">Pokud víte přesně kolikrát chcete opakovat, můžete použít `for` smyčky.</span><span class="sxs-lookup"><span data-stu-id="1408f-423">If you know exactly how many times you want to loop, you can use a `for` loop.</span></span> <span data-ttu-id="1408f-424">Tento druh smyčky je užitečné zejména pro počítání nebo počítání:</span><span class="sxs-lookup"><span data-stu-id="1408f-424">This kind of loop is especially useful for counting up or counting down:</span></span>

[!code-html[Main](introducing-razor-syntax-c/samples/sample53.html)]

<span data-ttu-id="1408f-425">Smyčky začíná `for` – klíčové slovo, za nímž následuje tři příkazy v závorkách, každý ukončeným středníkem.</span><span class="sxs-lookup"><span data-stu-id="1408f-425">The loop begins with the `for` keyword, followed by three statements in parentheses, each terminated with a semicolon.</span></span>

- <span data-ttu-id="1408f-426">Uvnitř závorek, prvním příkazem (`var i=10;`) vytvoří čítač a inicializuje ji na hodnotu 10.</span><span class="sxs-lookup"><span data-stu-id="1408f-426">Inside the parentheses, the first statement (`var i=10;`) creates a counter and initializes it to 10.</span></span> <span data-ttu-id="1408f-427">Není nutné pojmenovat čítač `i` &#8212; můžete použít jakoukoli proměnnou.</span><span class="sxs-lookup"><span data-stu-id="1408f-427">You don't have to name the counter `i` &#8212; you can use any variable.</span></span> <span data-ttu-id="1408f-428">Když `for` cyklu, hodnota čítače se zvýší automaticky.</span><span class="sxs-lookup"><span data-stu-id="1408f-428">When the `for` loop runs, the counter is automatically incremented.</span></span>
- <span data-ttu-id="1408f-429">Druhý příkaz (`i < 21;`) nastaví podmínku, jak daleko se mají spočítat.</span><span class="sxs-lookup"><span data-stu-id="1408f-429">The second statement (`i < 21;`) sets the condition for how far you want to count.</span></span> <span data-ttu-id="1408f-430">V takovém případě bude nutné přejít na maximálně 20 (to znamená, pokračujte dál čítač je menší než 21).</span><span class="sxs-lookup"><span data-stu-id="1408f-430">In this case, you want it to go to a maximum of 20 (that is, keep going while the counter is less than 21).</span></span>
- <span data-ttu-id="1408f-431">Třetí příkaz (`i++` ) používá operátor přírůstku, který jednoduše Určuje, že čítač musí mít 1 přidá do něj při každém spuštění smyčky.</span><span class="sxs-lookup"><span data-stu-id="1408f-431">The third statement (`i++` ) uses an increment operator, which simply specifies that the counter should have 1 added to it each time the loop runs.</span></span>

<span data-ttu-id="1408f-432">Uvnitř závorek je kód, který se spustí pro každou iteraci smyčky.</span><span class="sxs-lookup"><span data-stu-id="1408f-432">Inside the braces is the code that will run for each iteration of the loop.</span></span> <span data-ttu-id="1408f-433">Kód vytvoří nový odstavec (`<p>` element) každý čas a přidá nový řádek do výstupu, zobrazování hodnotě z `i` (Čítač).</span><span class="sxs-lookup"><span data-stu-id="1408f-433">The markup creates a new paragraph (`<p>` element) each time and adds a line to the output, displaying the value of `i` (the counter).</span></span> <span data-ttu-id="1408f-434">Když spustíte tuto stránku, tento příklad vytvoří 11 řádky zobrazení výstupu, s textem na každém řádku označující počet položek.</span><span class="sxs-lookup"><span data-stu-id="1408f-434">When you run this page, the example creates 11 lines displaying the output, with the text in each line indicating the item number.</span></span>

![Razor Img11](introducing-razor-syntax-c/_static/image11.jpg)

<span data-ttu-id="1408f-436">Pokud pracujete s kolekce nebo pole, často používají `foreach` smyčky.</span><span class="sxs-lookup"><span data-stu-id="1408f-436">If you're working with a collection or array, you often use a `foreach` loop.</span></span> <span data-ttu-id="1408f-437">Kolekce je skupina podobných objektů a `foreach` smyčky umožňuje provést úlohu pro každou položku v kolekci.</span><span class="sxs-lookup"><span data-stu-id="1408f-437">A collection is a group of similar objects, and the `foreach` loop lets you carry out a task on each item in the collection.</span></span> <span data-ttu-id="1408f-438">Tento druh smyčky je vhodné pro kolekce, protože na rozdíl od `for` smyčky, není nutné zvýší čítač nebo nastavit omezení.</span><span class="sxs-lookup"><span data-stu-id="1408f-438">This type of loop is convenient for collections, because unlike a `for` loop, you don't have to increment the counter or set a limit.</span></span> <span data-ttu-id="1408f-439">Místo toho `foreach` kód smyčky jednoduše pokračuje přes kolekci, dokud se nedokončí.</span><span class="sxs-lookup"><span data-stu-id="1408f-439">Instead, the `foreach` loop code simply proceeds through the collection until it's finished.</span></span>

<span data-ttu-id="1408f-440">Například následující kód vrátí položky v `Request.ServerVariables` kolekce, které je objekt, který obsahuje informace o webovém serveru.</span><span class="sxs-lookup"><span data-stu-id="1408f-440">For example, the following code returns the items in the `Request.ServerVariables` collection, which is an object that contains information about your web server.</span></span> <span data-ttu-id="1408f-441">Použije `foreac` h cyklu zobrazovaný název každé položky tak, že vytvoříte nový `<li>` prvek v seznamu s odrážkami HTML.</span><span class="sxs-lookup"><span data-stu-id="1408f-441">It uses a `foreac` h loop to display the name of each item by creating a new `<li>` element in an HTML bulleted list.</span></span>

[!code-html[Main](introducing-razor-syntax-c/samples/sample54.html)]

<span data-ttu-id="1408f-442">`foreach` – Klíčové slovo postupuje podle závorky, pokud deklarujete proměnnou, která představuje jednu položku v kolekci (v tomto příkladu `var item`) a po něm `in` – klíčové slovo, za nímž následuje kolekci chcete projít.</span><span class="sxs-lookup"><span data-stu-id="1408f-442">The `foreach` keyword is followed by parentheses where you declare a variable that represents a single item in the collection (in the example, `var item`), followed by the `in` keyword, followed by the collection you want to loop through.</span></span> <span data-ttu-id="1408f-443">V těle `foreach` smyčky, dostanete aktuální položky pomocí proměnné, která je deklarována jako dříve.</span><span class="sxs-lookup"><span data-stu-id="1408f-443">In the body of the `foreach` loop, you can access the current item using the variable that you declared earlier.</span></span>

![Razor Img12](introducing-razor-syntax-c/_static/image12.jpg)

<span data-ttu-id="1408f-445">Chcete-li vytvořit smyčku více pro obecné účely, použijte `while` – příkaz:</span><span class="sxs-lookup"><span data-stu-id="1408f-445">To create a more general-purpose loop, use the `while` statement:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample55.cshtml)]

<span data-ttu-id="1408f-446">A `while` smyčky začíná `while` – klíčové slovo, za nímž následuje závorky, kde můžete určit, jak dlouho bude pokračovat smyčky (zde pro tak dlouho, dokud `countNum` je menší než 50), pak bloku opakovat.</span><span class="sxs-lookup"><span data-stu-id="1408f-446">A `while` loop begins with the `while` keyword, followed by parentheses where you specify how long the loop continues (here, for as long as `countNum` is less than 50), then the block to repeat.</span></span> <span data-ttu-id="1408f-447">Obvykle zvýšit smyčky (přidání do) nebo dekrementace (odečíst od) proměnné nebo objektu se používá pro počítání.</span><span class="sxs-lookup"><span data-stu-id="1408f-447">Loops typically increment (add to) or decrement (subtract from) a variable or object used for counting.</span></span> <span data-ttu-id="1408f-448">V tomto příkladu `+=` operátor přičte 1 k `countNum` při každém spuštění smyčky.</span><span class="sxs-lookup"><span data-stu-id="1408f-448">In the example, the `+=` operator adds 1 to `countNum` each time the loop runs.</span></span> <span data-ttu-id="1408f-449">(Chcete-li snížení proměnná ve smyčce počítá směrem dolů, použijte operátor dekrementace `-=`).</span><span class="sxs-lookup"><span data-stu-id="1408f-449">(To decrement a variable in a loop that counts down, you would use the decrement operator `-=`).</span></span>

## <a name="objects-and-collections"></a><span data-ttu-id="1408f-450">Objekty a kolekce</span><span class="sxs-lookup"><span data-stu-id="1408f-450">Objects and Collections</span></span>

<span data-ttu-id="1408f-451">Téměř vše na webu technologie ASP.NET je objekt, včetně webové stránky.</span><span class="sxs-lookup"><span data-stu-id="1408f-451">Nearly everything in an ASP.NET website is an object, including the web page itself.</span></span> <span data-ttu-id="1408f-452">Tato část popisuje některé důležité objekty, které budete pracovat se často ve vašem kódu.</span><span class="sxs-lookup"><span data-stu-id="1408f-452">This section discusses some important objects you'll work with frequently in your code.</span></span>

### <a name="page-objects"></a><span data-ttu-id="1408f-453">Objekty stránky</span><span class="sxs-lookup"><span data-stu-id="1408f-453">Page Objects</span></span>

<span data-ttu-id="1408f-454">Na stránce je nejzákladnější objekt v technologii ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1408f-454">The most basic object in ASP.NET is the page.</span></span> <span data-ttu-id="1408f-455">Můžete přistupovat k vlastnosti objektu page přímo bez žádné opravňující objektu.</span><span class="sxs-lookup"><span data-stu-id="1408f-455">You can access properties of the page object directly without any qualifying object.</span></span> <span data-ttu-id="1408f-456">Následující kód načte cestu k souboru na stránce, pomocí `Request` objektu stránky:</span><span class="sxs-lookup"><span data-stu-id="1408f-456">The following code gets the page's file path, using the `Request` object of the page:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample56.cshtml)]

<span data-ttu-id="1408f-457">Aby bylo jasné, že už odkazuje vlastnosti a metody na aktuální objekt stránky, můžete volitelně použít klíčové slovo `this` k reprezentaci objektu page ve vašem kódu.</span><span class="sxs-lookup"><span data-stu-id="1408f-457">To make it clear that you're referencing properties and methods on the current page object, you can optionally use the keyword `this` to represent the page object in your code.</span></span> <span data-ttu-id="1408f-458">Tady je předchozí příklad kódu s `this` přidán k reprezentaci stránky:</span><span class="sxs-lookup"><span data-stu-id="1408f-458">Here is the previous code example, with `this` added to represent the page:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample57.cshtml)]

<span data-ttu-id="1408f-459">Můžete použít vlastnosti `Page` můžete získat velké množství informací, jako například:</span><span class="sxs-lookup"><span data-stu-id="1408f-459">You can use properties of the `Page` object to get a lot of information, such as:</span></span>

- <span data-ttu-id="1408f-460">`Request`.</span><span class="sxs-lookup"><span data-stu-id="1408f-460">`Request`.</span></span> <span data-ttu-id="1408f-461">Jak už víte, to je kolekce informací o aktuálním požadavku, včetně typu prohlížeče přišel požadavek, adresa URL stránky, identita uživatele, atd.</span><span class="sxs-lookup"><span data-stu-id="1408f-461">As you've already seen, this is a collection of information about the current request, including what type of browser made the request, the URL of the page, the user identity, etc.</span></span>
- <span data-ttu-id="1408f-462">`Response`.</span><span class="sxs-lookup"><span data-stu-id="1408f-462">`Response`.</span></span> <span data-ttu-id="1408f-463">Toto je kolekce informací o odpovědi (stránky), které se odešlou do prohlížeče při dokončení kódu serveru.</span><span class="sxs-lookup"><span data-stu-id="1408f-463">This is a collection of information about the response (page) that will be sent to the browser when the server code has finished running.</span></span> <span data-ttu-id="1408f-464">Tuto vlastnost můžete například použít při zápisu informací do odpovědi.</span><span class="sxs-lookup"><span data-stu-id="1408f-464">For example, you can use this property to write information into the response.</span></span> 

    [!code-cshtml[Main](introducing-razor-syntax-c/samples/sample58.cshtml)]

<a id="ID_CollectionsAndObjects"></a>
### <a name="collection-objects-arrays-and-dictionaries"></a><span data-ttu-id="1408f-465">Kolekce objektů (polí a slovníky)</span><span class="sxs-lookup"><span data-stu-id="1408f-465">Collection Objects (Arrays and Dictionaries)</span></span>

<span data-ttu-id="1408f-466">A *kolekce* je skupina objektů stejného typu, jako je například kolekce `Customer` objekty z databáze.</span><span class="sxs-lookup"><span data-stu-id="1408f-466">A *collection* is a group of objects of the same type, such as a collection of `Customer` objects from a database.</span></span> <span data-ttu-id="1408f-467">Technologie ASP.NET obsahuje mnoho předdefinovaných kolekcí, jako `Request.Files` kolekce.</span><span class="sxs-lookup"><span data-stu-id="1408f-467">ASP.NET contains many built-in collections, like the `Request.Files` collection.</span></span>

<span data-ttu-id="1408f-468">Často budete pracovat s daty v kolekcích.</span><span class="sxs-lookup"><span data-stu-id="1408f-468">You'll often work with data in collections.</span></span> <span data-ttu-id="1408f-469">Jsou dvě běžné typy kolekcí *pole* a *slovníku*.</span><span class="sxs-lookup"><span data-stu-id="1408f-469">Two common collection types are the *array* and the *dictionary*.</span></span> <span data-ttu-id="1408f-470">Pole je užitečné, když chcete uložit kolekci podobných položek, ale nebudete chtít vytvořit samostatné proměnnou pro uchování položky:</span><span class="sxs-lookup"><span data-stu-id="1408f-470">An array is useful when you want to store a collection of similar items but don't want to create a separate variable to hold each item:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample59.cshtml)]

<span data-ttu-id="1408f-471">S poli, je třeba deklarovat určitý datový typ. `string`, `int`, nebo `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="1408f-471">With arrays, you declare a specific data type, such as `string`, `int`, or `DateTime`.</span></span> <span data-ttu-id="1408f-472">K označení, že proměnné můžou obsahovat pole, přidat závorky deklarace (například `string[]` nebo `int[]`).</span><span class="sxs-lookup"><span data-stu-id="1408f-472">To indicate that the variable can contain an array, you add brackets to the declaration (such as `string[]` or `int[]`).</span></span> <span data-ttu-id="1408f-473">Můžete přístup k položkám v poli pomocí jejich pozice (index) nebo pomocí `foreach` příkazu.</span><span class="sxs-lookup"><span data-stu-id="1408f-473">You can access items in an array using their position (index) or by using the `foreach` statement.</span></span> <span data-ttu-id="1408f-474">Indexy pole jsou počítány od nuly &#8212; tedy první položka je na pozici 0, druhá položka je na pozici 1 a tak dále.</span><span class="sxs-lookup"><span data-stu-id="1408f-474">Array indexes are zero-based &#8212; that is, the first item is at position 0, the second item is at position 1, and so on.</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample60.cshtml)]

<span data-ttu-id="1408f-475">Můžete určit počet položek v poli získáním jeho `Length` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="1408f-475">You can determine the number of items in an array by getting its `Length` property.</span></span> <span data-ttu-id="1408f-476">Získá pozici konkrétní položku v poli (k vyhledání pole), použijte `Array.IndexOf` metody.</span><span class="sxs-lookup"><span data-stu-id="1408f-476">To get the position of a specific item in the array (to search the array), use the `Array.IndexOf` method.</span></span> <span data-ttu-id="1408f-477">Můžete také provést třeba zpětného obsah pole ( `Array.Reverse` metoda) nebo řazení obsahu ( `Array.Sort` metoda).</span><span class="sxs-lookup"><span data-stu-id="1408f-477">You can also do things like reverse the contents of an array (the `Array.Reverse` method) or sort the contents (the `Array.Sort` method).</span></span>

<span data-ttu-id="1408f-478">Výstupní řetězec pole Kód zobrazený v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="1408f-478">The output of the string array code displayed in a browser:</span></span>

![Razor Img13](introducing-razor-syntax-c/_static/image13.jpg)

<span data-ttu-id="1408f-480">Slovník je kolekce párů klíč/hodnota, ve kterém zadat klíč (nebo název) k nastavení nebo načtení odpovídající hodnotě:</span><span class="sxs-lookup"><span data-stu-id="1408f-480">A dictionary is a collection of key/value pairs, where you provide the key (or name) to set or retrieve the corresponding value:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample61.cshtml)]

<span data-ttu-id="1408f-481">Chcete-li vytvořit slovník, použijte `new` – klíčové slovo k označení, že vytváříte nový objekt slovníku.</span><span class="sxs-lookup"><span data-stu-id="1408f-481">To create a dictionary, you use the `new` keyword to indicate that you're creating a new dictionary object.</span></span> <span data-ttu-id="1408f-482">Slovník můžete přiřadit k proměnné pomocí `var` – klíčové slovo.</span><span class="sxs-lookup"><span data-stu-id="1408f-482">You can assign a dictionary to a variable using the `var` keyword.</span></span> <span data-ttu-id="1408f-483">Označení datové typy položek ve slovníku pomocí ostrých závorek ( `< >` ).</span><span class="sxs-lookup"><span data-stu-id="1408f-483">You indicate the data types of the items in the dictionary using angle brackets ( `< >` ).</span></span> <span data-ttu-id="1408f-484">Na konci deklarace je nutné přidat dvojici závorek, protože je to ve skutečnosti metodou, která vytvoří nový slovník.</span><span class="sxs-lookup"><span data-stu-id="1408f-484">At the end of the declaration, you must add a pair of parentheses, because this is actually a method that creates a new dictionary.</span></span>

<span data-ttu-id="1408f-485">Přidání položek do slovníku, můžete volat `Add` metoda proměnnou slovníku (`myScores` v tomto případě) a pak zadejte klíč a hodnotu.</span><span class="sxs-lookup"><span data-stu-id="1408f-485">To add items to the dictionary, you can call the `Add` method of the dictionary variable (`myScores` in this case), and then specify a key and a value.</span></span> <span data-ttu-id="1408f-486">Alternativně můžete použít hranaté závorky k označení klíče a provádět jednoduché přiřazení, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="1408f-486">Alternatively, you can use square brackets to indicate the key and do a simple assignment, as in the following example:</span></span>

[!code-csharp[Main](introducing-razor-syntax-c/samples/sample62.cs)]

<span data-ttu-id="1408f-487">Pokud chcete získat hodnotu ze slovníku, můžete zadat klíč v závorkách:</span><span class="sxs-lookup"><span data-stu-id="1408f-487">To get a value from the dictionary, you specify the key in brackets:</span></span>

[!code-csharp[Main](introducing-razor-syntax-c/samples/sample63.cs)]

## <a name="calling-methods-with-parameters"></a><span data-ttu-id="1408f-488">Volání metody s parametry</span><span class="sxs-lookup"><span data-stu-id="1408f-488">Calling Methods with Parameters</span></span>

<span data-ttu-id="1408f-489">Jak čtete výše v tomto článku, může mít objekty, které můžete naprogramovat pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="1408f-489">As you read earlier in this article, the objects that you program with can have methods.</span></span> <span data-ttu-id="1408f-490">Například `Database` objekt může mít `Database.Connect` metody.</span><span class="sxs-lookup"><span data-stu-id="1408f-490">For example, a `Database` object might have a `Database.Connect` method.</span></span> <span data-ttu-id="1408f-491">Mnoho metody mají také jeden nebo více parametrů.</span><span class="sxs-lookup"><span data-stu-id="1408f-491">Many methods also have one or more parameters.</span></span> <span data-ttu-id="1408f-492">A *parametr* je hodnota, která můžete předat metodě pro povolení metody a dokončení úkolu.</span><span class="sxs-lookup"><span data-stu-id="1408f-492">A *parameter* is a value that you pass to a method to enable the method to complete its task.</span></span> <span data-ttu-id="1408f-493">Podívejte se například na deklaraci `Request.MapPath` metodu, která přijímá tři parametry:</span><span class="sxs-lookup"><span data-stu-id="1408f-493">For example, look at a declaration for the `Request.MapPath` method, which takes three parameters:</span></span>

[!code-csharp[Main](introducing-razor-syntax-c/samples/sample64.cs)]

<span data-ttu-id="1408f-494">(Řádek byl zabalen do byl čitelnější.</span><span class="sxs-lookup"><span data-stu-id="1408f-494">(The line has been wrapped to make it more readable.</span></span> <span data-ttu-id="1408f-495">Mějte na paměti, že můžete vložit konce řádků téměř všech místech, s výjimkou uvnitř řetězců, které jsou uzavřeny v uvozovkách.)</span><span class="sxs-lookup"><span data-stu-id="1408f-495">Remember that you can put line breaks almost any place except inside strings that are enclosed in quotation marks.)</span></span>

<span data-ttu-id="1408f-496">Tato metoda vrací fyzickou cestu na serveru, který odpovídá pro zadanou virtuální cestu.</span><span class="sxs-lookup"><span data-stu-id="1408f-496">This method returns the physical path on the server that corresponds to a specified virtual path.</span></span> <span data-ttu-id="1408f-497">Jsou tři parametry pro metodu `virtualPath`, `baseVirtualDir`, a `allowCrossAppMapping`.</span><span class="sxs-lookup"><span data-stu-id="1408f-497">The three parameters for the method are `virtualPath`, `baseVirtualDir`, and `allowCrossAppMapping`.</span></span> <span data-ttu-id="1408f-498">(Všimněte si, že v deklaraci, parametry jsou uvedeny s datovými typy dat, který bude přijímat.) Při volání této metody, je třeba zadat hodnoty pro všemi třemi parametry.</span><span class="sxs-lookup"><span data-stu-id="1408f-498">(Notice that in the declaration, the parameters are listed with the data types of the data that they'll accept.) When you call this method, you must supply values for all three parameters.</span></span>

<span data-ttu-id="1408f-499">Syntaxe Razor poskytuje dvě možnosti pro předání parametrů metodě: *poziční parametry* a *pojmenované parametry*.</span><span class="sxs-lookup"><span data-stu-id="1408f-499">The Razor syntax gives you two options for passing parameters to a method: *positional parameters* and *named parameters*.</span></span> <span data-ttu-id="1408f-500">Volání metody pomocí poziční parametry, předání parametrů v případě přísného pořadí zadaném v deklaraci metody.</span><span class="sxs-lookup"><span data-stu-id="1408f-500">To call a method using positional parameters, you pass the parameters in a strict order that's specified in the method declaration.</span></span> <span data-ttu-id="1408f-501">(By obvykle znáte toto pořadí najdete dokumentaci k metodě.) Je nutné postupovat podle pořadí, a přeskočíte nelze některé z parametrů &#8212; Pokud potřeby předáte prázdný řetězec (`""`) nebo `null` pro poziční parametr, který není nutné hodnotu.</span><span class="sxs-lookup"><span data-stu-id="1408f-501">(You would typically know this order by reading documentation for the method.) You must follow the order, and you can't skip any of the parameters &#8212; if necessary, you pass an empty string (`""`) or `null` for a positional parameter that you don't have a value for.</span></span>

<span data-ttu-id="1408f-502">Následující příklad předpokládá, že máte složku s názvem *skripty* na vašem webu.</span><span class="sxs-lookup"><span data-stu-id="1408f-502">The following example assumes you have a folder named *scripts* on your website.</span></span> <span data-ttu-id="1408f-503">Kód volá `Request.MapPath` metoda a předá hodnoty pro tři parametry ve správném pořadí.</span><span class="sxs-lookup"><span data-stu-id="1408f-503">The code calls the `Request.MapPath` method and passes values for the three parameters in the correct order.</span></span> <span data-ttu-id="1408f-504">Pak zobrazí výslednou cestu namapované.</span><span class="sxs-lookup"><span data-stu-id="1408f-504">It then displays the resulting mapped path.</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample65.cshtml)]

<span data-ttu-id="1408f-505">Když metoda má velký počet parametrů, můžete zachovat váš kód lépe čitelný pomocí pojmenovaných parametrů.</span><span class="sxs-lookup"><span data-stu-id="1408f-505">When a method has many parameters, you can keep your code more readable by using named parameters.</span></span> <span data-ttu-id="1408f-506">Volání metody pomocí pojmenované parametry, zadejte název parametru následovaný dvojtečkou (:) a hodnota.</span><span class="sxs-lookup"><span data-stu-id="1408f-506">To call a method using named parameters, you specify the parameter name followed by a colon (:), and then the value.</span></span> <span data-ttu-id="1408f-507">Výhodou pojmenované parametry je, že budete předávat v libovolném pořadí, které chcete.</span><span class="sxs-lookup"><span data-stu-id="1408f-507">The advantage of named parameters is that you can pass them in any order you want.</span></span> <span data-ttu-id="1408f-508">(Nevýhodou je, že volání metody není co nejkompaktnější.)</span><span class="sxs-lookup"><span data-stu-id="1408f-508">(A disadvantage is that the method call is not as compact.)</span></span>

<span data-ttu-id="1408f-509">Následující příklad volá stejnou metodu, jak je uvedeno výše, ale používá pojmenované parametry k zadání hodnot:</span><span class="sxs-lookup"><span data-stu-id="1408f-509">The following example calls the same method as above, but uses named parameters to supply the values:</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample66.cshtml)]

<span data-ttu-id="1408f-510">Jak je vidět, parametry jsou předány v jiném pořadí.</span><span class="sxs-lookup"><span data-stu-id="1408f-510">As you can see, the parameters are passed in a different order.</span></span> <span data-ttu-id="1408f-511">Pokud spustíte z předchozího příkladu a v tomto příkladu, ale budete vrátí stejnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="1408f-511">However, if you run the previous example and this example, they'll return the same value.</span></span>

<a id="ID_HandlingErrors"></a>
## <a name="handling-errors"></a><span data-ttu-id="1408f-512">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="1408f-512">Handling Errors</span></span>

### <a name="try-catch-statements"></a><span data-ttu-id="1408f-513">Try-Catch – příkazy</span><span class="sxs-lookup"><span data-stu-id="1408f-513">Try-Catch Statements</span></span>

<span data-ttu-id="1408f-514">Často musíte příkazů v kódu, který může selhat z důvodu mimo vaši kontrolu.</span><span class="sxs-lookup"><span data-stu-id="1408f-514">You'll often have statements in your code that might fail for reasons outside your control.</span></span> <span data-ttu-id="1408f-515">Příklad:</span><span class="sxs-lookup"><span data-stu-id="1408f-515">For example:</span></span>

- <span data-ttu-id="1408f-516">Pokud váš kód se pokusí vytvořit nebo získat přístup k souboru, může dojít k všechny možné druhy chyb.</span><span class="sxs-lookup"><span data-stu-id="1408f-516">If your code tries to create or access a file, all sorts of errors might occur.</span></span> <span data-ttu-id="1408f-517">Požadovaný soubor nemusí existovat, může být zablokován, kód nemusí mít oprávnění a tak dále.</span><span class="sxs-lookup"><span data-stu-id="1408f-517">The file you want might not exist, it might be locked, the code might not have permissions, and so on.</span></span>
- <span data-ttu-id="1408f-518">Podobně pokud váš kód se pokouší aktualizovat záznamy v databázi, může být problémy s oprávněními, může dojít ke ztrátě připojení k databázi, data a šetřit tak může být neplatný a tak dále.</span><span class="sxs-lookup"><span data-stu-id="1408f-518">Similarly, if your code tries to update records in a database, there can be permissions issues, the connection to the database might be dropped, the data to save might be invalid, and so on.</span></span>

<span data-ttu-id="1408f-519">V programovacím prostředí, se nazývají těmito situacemi *výjimky*.</span><span class="sxs-lookup"><span data-stu-id="1408f-519">In programming terms, these situations are called *exceptions*.</span></span> <span data-ttu-id="1408f-520">Pokud váš kód narazí na výjimku, generuje (vyvolá výjimku) chybovou zprávu, která je v nejlepším případě obtěžující uživatelům:</span><span class="sxs-lookup"><span data-stu-id="1408f-520">If your code encounters an exception, it generates (throws) an error message that's, at best, annoying to users:</span></span>

![Razor Img14](introducing-razor-syntax-c/_static/image14.jpg)

<span data-ttu-id="1408f-522">V situacích, kdy váš kód může nastat výjimky a pokud se chcete vyhnout chybové zprávy tohoto typu, můžete použít `try/catch` příkazy.</span><span class="sxs-lookup"><span data-stu-id="1408f-522">In situations where your code might encounter exceptions, and in order to avoid error messages of this type, you can use `try/catch` statements.</span></span> <span data-ttu-id="1408f-523">V `try` příkazu spustit kód, který při kontrole.</span><span class="sxs-lookup"><span data-stu-id="1408f-523">In the `try` statement, you run the code that you're checking.</span></span> <span data-ttu-id="1408f-524">V jedné nebo více `catch` příkazy, můžete vyhledat konkrétní chyby (konkrétní typy výjimek), které mohly nastat.</span><span class="sxs-lookup"><span data-stu-id="1408f-524">In one or more `catch` statements, you can look for specific errors (specific types of exceptions) that might have occurred.</span></span> <span data-ttu-id="1408f-525">Můžete vytvořit tolik `catch` příkazy jako vy, musíme se podívat chyb, které jsou očekávání.</span><span class="sxs-lookup"><span data-stu-id="1408f-525">You can include as many `catch` statements as you need to look for errors that you are anticipating.</span></span>

> [!NOTE]
> <span data-ttu-id="1408f-526">Doporučujeme, abyste je velmi riskantní používat `Response.Redirect` metoda `try/catch` příkazy, protože to může způsobit výjimku na stránce.</span><span class="sxs-lookup"><span data-stu-id="1408f-526">We recommend that you avoid using the `Response.Redirect` method in `try/catch` statements, because it can cause an exception in your page.</span></span>


<span data-ttu-id="1408f-527">Následující příklad ukazuje stránka, která vytváří textový soubor na první žádost o a poté zobrazí tlačítko, které umožňuje uživateli otevřít soubor.</span><span class="sxs-lookup"><span data-stu-id="1408f-527">The following example shows a page that creates a text file on the first request and then displays a button that lets the user open the file.</span></span> <span data-ttu-id="1408f-528">V příkladu záměrně používá chybný název souboru tak, aby způsobí výjimku.</span><span class="sxs-lookup"><span data-stu-id="1408f-528">The example deliberately uses a bad file name so that it will cause an exception.</span></span> <span data-ttu-id="1408f-529">Tento kód obsahuje `catch` příkazy pro dvě výjimky: `FileNotFoundException`, která nastane, pokud název souboru je chybný, a `DirectoryNotFoundException`, která nastane, pokud ASP.NET i nelze najít složku.</span><span class="sxs-lookup"><span data-stu-id="1408f-529">The code includes `catch` statements for two possible exceptions: `FileNotFoundException`, which occurs if the file name is bad, and `DirectoryNotFoundException`, which occurs if ASP.NET can't even find the folder.</span></span> <span data-ttu-id="1408f-530">(Příkaz v tomto příkladu můžete odkomentovat Chcete-li zobrazit, jak se spustí při všechno funguje správně.)</span><span class="sxs-lookup"><span data-stu-id="1408f-530">(You can uncomment a statement in the example in order to see how it runs when everything works properly.)</span></span>

<span data-ttu-id="1408f-531">Pokud váš kód nebyl zpracovat výjimky, zobrazí se chybová stránka jako na předchozím snímku obrazovky.</span><span class="sxs-lookup"><span data-stu-id="1408f-531">If your code didn't handle the exception, you would see an error page like the previous screen shot.</span></span> <span data-ttu-id="1408f-532">Ale `try/catch` část pomůže zabránit uživateli v zobrazení tyto typy chyb.</span><span class="sxs-lookup"><span data-stu-id="1408f-532">However, the `try/catch` section helps prevent the user from seeing these types of errors.</span></span>

[!code-cshtml[Main](introducing-razor-syntax-c/samples/sample67.cshtml)]

## <a name="additional-resources"></a><span data-ttu-id="1408f-533">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="1408f-533">Additional Resources</span></span>

<span data-ttu-id="1408f-534">**Programování v jazyce Visual Basic**</span><span class="sxs-lookup"><span data-stu-id="1408f-534">**Programming with Visual Basic**</span></span>


[<span data-ttu-id="1408f-535">Příloha: syntaxe a jazyk Visual Basic</span><span class="sxs-lookup"><span data-stu-id="1408f-535">Appendix: Visual Basic Language and Syntax</span></span>](https://go.microsoft.com/fwlink/?LinkId=202908)


<span data-ttu-id="1408f-536">**Referenční dokumentace**</span><span class="sxs-lookup"><span data-stu-id="1408f-536">**Reference Documentation**</span></span>


[<span data-ttu-id="1408f-537">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="1408f-537">ASP.NET</span></span>](https://msdn.microsoft.com/library/ee532866.aspx)

[<span data-ttu-id="1408f-538">Jazyk C#</span><span class="sxs-lookup"><span data-stu-id="1408f-538">C# Language</span></span>](https://msdn.microsoft.com/library/kx37x362.aspx)
