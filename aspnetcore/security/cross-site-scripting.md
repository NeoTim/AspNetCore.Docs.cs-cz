---
title: Zabránit skriptování mezi weby (XSS) v ASP.NET Core
author: rick-anderson
description: Přečtěte si o skriptování mezi weby (XSS) a techniky pro řešení této chyby zabezpečení v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 10/02/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cross-site-scripting
ms.openlocfilehash: a94fe1612c023468238f09a91ddb0346b65d52ba
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408016"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="098fb-103">Zabránit skriptování mezi weby (XSS) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="098fb-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="098fb-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="098fb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="098fb-105">Skriptování mezi weby (XSS) je ohrožení zabezpečení, které umožňuje útočníkovi umístit skripty na straně klienta (obvykle JavaScript) do webových stránek.</span><span class="sxs-lookup"><span data-stu-id="098fb-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="098fb-106">Když ostatní uživatelé načtou ovlivněné stránky, spustí se skripty útočníka, což útočníkovi umožní ukrást soubory cookie a tokeny relace, změnit obsah webové stránky prostřednictvím manipulace s modelem DOM nebo přesměrovat prohlížeč na jinou stránku.</span><span class="sxs-lookup"><span data-stu-id="098fb-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="098fb-107">K ohrožení zabezpečení XSS obvykle dochází, když aplikace přebírá uživatelem vstup a výstupuje na stránku bez ověřování, kódování nebo uvozovacího uvozovacího prvku.</span><span class="sxs-lookup"><span data-stu-id="098fb-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="098fb-108">Ochrana aplikace proti XSS</span><span class="sxs-lookup"><span data-stu-id="098fb-108">Protecting your application against XSS</span></span>

<span data-ttu-id="098fb-109">Na základní úrovni technologie XSS funguje tak, že se podíváme na vaši aplikaci na vložení `<script>` značky na vykreslenou stránku nebo vložením `On*` události do prvku.</span><span class="sxs-lookup"><span data-stu-id="098fb-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="098fb-110">Vývojáři by měli pomocí následujících kroků prevence zabránit zavlečení SKRIPTOVÁNÍ do své aplikace.</span><span class="sxs-lookup"><span data-stu-id="098fb-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="098fb-111">Nikdy neumísťujte nedůvěryhodná data do vstupu ve formátu HTML, pokud nepoužijete zbývající část následujících kroků.</span><span class="sxs-lookup"><span data-stu-id="098fb-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="098fb-112">Nedůvěryhodná data jsou všechna data, která může být ovládána útočníkem, vstupy formulářů HTML, řetězce dotazů, hlavičky protokolu HTTP, dokonce i data, která jsou zdrojem tohoto útočníka, může být, že by mohlo dojít k porušení zabezpečení vaší databáze, i když nemůžou porušit vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="098fb-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="098fb-113">Před vložením nedůvěryhodných dat do elementu HTML zajistěte, aby byl kódovaný HTML.</span><span class="sxs-lookup"><span data-stu-id="098fb-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="098fb-114">Kódování HTML přebírá znaky jako &lt; a mění je v bezpečné podobě, jako je &amp; lt;</span><span class="sxs-lookup"><span data-stu-id="098fb-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="098fb-115">Před vložením nedůvěryhodných dat do atributu HTML zajistěte, aby byl kódovaný HTML.</span><span class="sxs-lookup"><span data-stu-id="098fb-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="098fb-116">Kódování atributů HTML je nadmnožinou kódování HTML a kóduje další znaky, jako například "a".</span><span class="sxs-lookup"><span data-stu-id="098fb-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="098fb-117">Před vložením nedůvěryhodných dat do JavaScriptu umístěte data v elementu HTML, jehož obsah načítáte za běhu.</span><span class="sxs-lookup"><span data-stu-id="098fb-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="098fb-118">Pokud to není možné, zajistěte, aby byla data kódovaná pomocí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="098fb-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="098fb-119">Kódování JavaScriptu přebírá nebezpečné znaky pro JavaScript a nahrazuje je šestnáctkově, například &lt; by bylo zakódováno jako `\u003C` .</span><span class="sxs-lookup"><span data-stu-id="098fb-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="098fb-120">Před vložením nedůvěryhodných dat do řetězce dotazu URL zajistěte, aby byla zakódovaná adresa URL.</span><span class="sxs-lookup"><span data-stu-id="098fb-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-razor"></a><span data-ttu-id="098fb-121">Kódování HTML pomocíRazor</span><span class="sxs-lookup"><span data-stu-id="098fb-121">HTML Encoding using Razor</span></span>

<span data-ttu-id="098fb-122">RazorModul používaný v MVC automaticky zakóduje všechny výstupy zdroje z proměnných, pokud nepracujete opravdu k tomu, aby se tak zabránilo.</span><span class="sxs-lookup"><span data-stu-id="098fb-122">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="098fb-123">Používá pravidla kódování atributů HTML vždy, když použijete *@* směrnici.</span><span class="sxs-lookup"><span data-stu-id="098fb-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="098fb-124">Kódování atributu HTML je nadmnožinou kódování HTML to znamená, že se nemusíte zabývat sami, ať už používáte kódování HTML nebo kódování atributů HTML.</span><span class="sxs-lookup"><span data-stu-id="098fb-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="098fb-125">Je nutné, abyste měli jistotu, že budete používat pouze @ v kontextu jazyka HTML, nikoli při pokusu o vložení nedůvěryhodného vstupu přímo do JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="098fb-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="098fb-126">Pomocník značek také zakóduje vstup, který použijete v parametrech značek.</span><span class="sxs-lookup"><span data-stu-id="098fb-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="098fb-127">Proveďte následující Razor zobrazení:</span><span class="sxs-lookup"><span data-stu-id="098fb-127">Take the following Razor view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="098fb-128">Toto zobrazení vypíše obsah proměnné *untrustedInput* .</span><span class="sxs-lookup"><span data-stu-id="098fb-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="098fb-129">Tato proměnná zahrnuje některé znaky, které jsou používány v útocích XSS, konkrétně &lt; a &gt; .</span><span class="sxs-lookup"><span data-stu-id="098fb-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="098fb-130">Prozkoumání zdroje zobrazuje Vykreslený výstup kódovaný jako:</span><span class="sxs-lookup"><span data-stu-id="098fb-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="098fb-131">ASP.NET Core MVC poskytuje `HtmlString` třídu, která není automaticky kódována při výstupu.</span><span class="sxs-lookup"><span data-stu-id="098fb-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="098fb-132">Tato akce by se nikdy neměla používat v kombinaci s nedůvěryhodným vstupem, protože to vystavuje chybu zabezpečení XSS.</span><span class="sxs-lookup"><span data-stu-id="098fb-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-razor"></a><span data-ttu-id="098fb-133">Kódování JavaScriptu pomocíRazor</span><span class="sxs-lookup"><span data-stu-id="098fb-133">JavaScript Encoding using Razor</span></span>

<span data-ttu-id="098fb-134">Může nastat situace, kdy budete chtít vložit hodnotu do JavaScriptu pro zpracování v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="098fb-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="098fb-135">Můžete to provést dvěma způsoby.</span><span class="sxs-lookup"><span data-stu-id="098fb-135">There are two ways to do this.</span></span> <span data-ttu-id="098fb-136">Nejbezpečnější způsob, jak vkládat hodnoty, je umístit hodnotu do atributu data tagu a načíst ho v JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="098fb-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="098fb-137">Například:</span><span class="sxs-lookup"><span data-stu-id="098fb-137">For example:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   <div
       id="injectedData"
       data-untrustedinput="@untrustedInput" />

   <script>
     var injectedData = document.getElementById("injectedData");

     // All clients
     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     // HTML 5 clients only
     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

<span data-ttu-id="098fb-138">Tím se vytvoří následující kód HTML</span><span class="sxs-lookup"><span data-stu-id="098fb-138">This will produce the following HTML</span></span>

```html
<div
     id="injectedData"
     data-untrustedinput="&lt;&quot;123&quot;&gt;" />

   <script>
     var injectedData = document.getElementById("injectedData");

     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

<span data-ttu-id="098fb-139">V takovém případě se při spuštění vykreslí následující:</span><span class="sxs-lookup"><span data-stu-id="098fb-139">Which, when it runs, will render the following:</span></span>

```
<"123">
   <"123">
```

<span data-ttu-id="098fb-140">JavaScriptový kodér můžete také volat přímo:</span><span class="sxs-lookup"><span data-stu-id="098fb-140">You can also call the JavaScript encoder directly:</span></span>

```cshtml
@using System.Text.Encodings.Web;
   @inject JavaScriptEncoder encoder;

   @{
       var untrustedInput = "<\"123\">";
   }

   <script>
       document.write("@encoder.Encode(untrustedInput)");
   </script>
```

<span data-ttu-id="098fb-141">Vykreslí se v prohlížeči následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="098fb-141">This will render in the browser as follows:</span></span>

```html
<script>
    document.write("\u003C\u0022123\u0022\u003E");
</script>
```

>[!WARNING]
> <span data-ttu-id="098fb-142">Nezřetězení nedůvěryhodného vstupu v JavaScriptu pro vytváření elementů DOM.</span><span class="sxs-lookup"><span data-stu-id="098fb-142">Don't concatenate untrusted input in JavaScript to create DOM elements.</span></span> <span data-ttu-id="098fb-143">Měli byste použít `createElement()` a přiřadit hodnoty vlastností odpovídajícím způsobem `node.TextContent=` , jako je, nebo `element.SetAttribute()` / `element[attribute]=` jinak můžete vystavit sami sobě pomocí skriptování na základě modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="098fb-143">You should use `createElement()` and assign property values appropriately such as `node.TextContent=`, or use `element.SetAttribute()`/`element[attribute]=` otherwise you expose yourself to DOM-based XSS.</span></span>

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="098fb-144">Přístup k kodérům v kódu</span><span class="sxs-lookup"><span data-stu-id="098fb-144">Accessing encoders in code</span></span>

<span data-ttu-id="098fb-145">Kodéry HTML, JavaScript a URL jsou pro váš kód k dispozici dvěma způsoby, můžete je vložit prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection) nebo můžete použít výchozí kodéry obsažené v `System.Text.Encodings.Web` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="098fb-145">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="098fb-146">Pokud použijete výchozí kodéry, pak všechny použité pro rozsahy znaků, které se mají považovat za bezpečné, se neprojeví – Výchozí kodéry použijí nejbezpečnější pravidla kódování.</span><span class="sxs-lookup"><span data-stu-id="098fb-146">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="098fb-147">Chcete-li použít konfigurovatelné kodéry přes DI, vaše konstruktory by měly podle potřeby přebírat parametr *HtmlEncode*, *JavaScriptEncoder* a *UrlEncoder* .</span><span class="sxs-lookup"><span data-stu-id="098fb-147">To use the configurable encoders via DI your constructors should take an *HtmlEncoder*, *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="098fb-148">Například;</span><span class="sxs-lookup"><span data-stu-id="098fb-148">For example;</span></span>

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a><span data-ttu-id="098fb-149">Kódování parametrů adresy URL</span><span class="sxs-lookup"><span data-stu-id="098fb-149">Encoding URL Parameters</span></span>

<span data-ttu-id="098fb-150">Pokud chcete vytvořit řetězec dotazu URL s nedůvěryhodným vstupem jako hodnotu, použijte `UrlEncoder` ke kódování hodnoty.</span><span class="sxs-lookup"><span data-stu-id="098fb-150">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="098fb-151">Třeba</span><span class="sxs-lookup"><span data-stu-id="098fb-151">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="098fb-152">Po kódování bude proměnná encodedValue obsahovat `%22Quoted%20Value%20with%20spaces%20and%20%26%22` .</span><span class="sxs-lookup"><span data-stu-id="098fb-152">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="098fb-153">Mezery, uvozovky, interpunkční znaménka a další nebezpečné znaky budou v procentech zakódovány na jejich hexadecimální hodnotu, například znak mezery se stane %20.</span><span class="sxs-lookup"><span data-stu-id="098fb-153">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="098fb-154">Nepoužívejte nedůvěryhodný vstup jako součást cesty URL.</span><span class="sxs-lookup"><span data-stu-id="098fb-154">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="098fb-155">Vždy předejte nedůvěryhodný vstup jako hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="098fb-155">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="098fb-156">Přizpůsobení kodérů</span><span class="sxs-lookup"><span data-stu-id="098fb-156">Customizing the Encoders</span></span>

<span data-ttu-id="098fb-157">Ve výchozím nastavení používají kodéry zabezpečený seznam omezený na základní rozsah Latinské sady Latin Unicode a zakódovat všechny znaky mimo tento rozsah jako jejich ekvivalenty kódu znaku.</span><span class="sxs-lookup"><span data-stu-id="098fb-157">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="098fb-158">Toto chování má vliv také Razor na taghelperu a HtmlHelper vykreslování, protože bude používat kodéry k výstupu řetězců.</span><span class="sxs-lookup"><span data-stu-id="098fb-158">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="098fb-159">Důvodem je ochrana proti neznámým nebo budoucím chybám prohlížeče (předchozí chyby prohlížeče mají Trip analýzu na základě zpracování neanglických znaků).</span><span class="sxs-lookup"><span data-stu-id="098fb-159">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="098fb-160">Pokud váš web používá velké množství znaků mimo Latin, jako je čínština, cyrilice nebo jiné, pravděpodobně to není chování, které požadujete.</span><span class="sxs-lookup"><span data-stu-id="098fb-160">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="098fb-161">Seznam bezpečných seznamů pro kodér můžete přizpůsobit tak, aby zahrnoval rozsahy Unicode, které jsou vhodné pro vaši aplikaci během spouštění, v `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="098fb-161">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="098fb-162">Například pomocí výchozí konfigurace můžete použít Razor HtmlHelper jako tak,</span><span class="sxs-lookup"><span data-stu-id="098fb-162">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="098fb-163">Po zobrazení zdroje webové stránky uvidíte, že byla vykreslena takto, s kódovaným čínským textem;</span><span class="sxs-lookup"><span data-stu-id="098fb-163">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="098fb-164">Chcete-li rozšířit znaky zpracovávané jako bezpečné kodérem, vložte následující řádek do `ConfigureServices()` metody v `startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="098fb-164">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="098fb-165">Tento příklad rozšiřuje seznam bezpečných dat tak, aby zahrnoval CjkUnifiedIdeographs rozsah znaků Unicode.</span><span class="sxs-lookup"><span data-stu-id="098fb-165">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="098fb-166">Vykreslený výstup by se teď stal</span><span class="sxs-lookup"><span data-stu-id="098fb-166">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="098fb-167">Bezpečné rozsahy seznamů jsou zadány jako grafy kódu Unicode, nikoli jazyky.</span><span class="sxs-lookup"><span data-stu-id="098fb-167">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="098fb-168">[Standard Unicode](https://unicode.org/) obsahuje seznam [kódových grafů](https://www.unicode.org/charts/index.html) , pomocí kterých můžete najít graf obsahující vaše znaky.</span><span class="sxs-lookup"><span data-stu-id="098fb-168">The [Unicode standard](https://unicode.org/) has a list of [code charts](https://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="098fb-169">Každý kodér, HTML, JavaScript a adresu URL je nutné nakonfigurovat samostatně.</span><span class="sxs-lookup"><span data-stu-id="098fb-169">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="098fb-170">Přizpůsobení seznamu bezpečný má vliv pouze na kodéry, které jsou zdrojem přes DI.</span><span class="sxs-lookup"><span data-stu-id="098fb-170">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="098fb-171">Pokud přímo přistupujete k kodéru přes `System.Text.Encodings.Web.*Encoder.Default` výchozí nastavení, použije se jenom základní Safelist s latinkou.</span><span class="sxs-lookup"><span data-stu-id="098fb-171">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="098fb-172">Kde má být kódování provedeno?</span><span class="sxs-lookup"><span data-stu-id="098fb-172">Where should encoding take place?</span></span>

<span data-ttu-id="098fb-173">Obecně přijatý postup spočívá v tom, že kódování probíhá na místě výstupu a kódované hodnoty by nikdy neměly být uloženy v databázi.</span><span class="sxs-lookup"><span data-stu-id="098fb-173">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="098fb-174">Kódování v bodě výstupu umožňuje změnit použití dat, například z formátu HTML na hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="098fb-174">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="098fb-175">Umožňuje také snadné vyhledávání dat bez nutnosti kódování hodnot před vyhledáváním a umožňuje využít všechny změny nebo opravy chyb provedené v kodérech.</span><span class="sxs-lookup"><span data-stu-id="098fb-175">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="098fb-176">Ověřování jako technika ochrany XSS</span><span class="sxs-lookup"><span data-stu-id="098fb-176">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="098fb-177">Ověřování může být užitečným nástrojem v omezení útoků XSS.</span><span class="sxs-lookup"><span data-stu-id="098fb-177">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="098fb-178">Například číselný řetězec obsahující pouze znaky 0-9 nespustí Útok XSS.</span><span class="sxs-lookup"><span data-stu-id="098fb-178">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="098fb-179">Při přijímání HTML při vstupu uživatele se ověřování bude složitější.</span><span class="sxs-lookup"><span data-stu-id="098fb-179">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="098fb-180">Analýza vstupu HTML je obtížná, pokud není možná.</span><span class="sxs-lookup"><span data-stu-id="098fb-180">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="098fb-181">Markdownu společně s analyzátorem, který odděluje vložený kód HTML, je bezpečnější možností pro přijetí formátovaného vstupu.</span><span class="sxs-lookup"><span data-stu-id="098fb-181">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="098fb-182">Nikdy nespoléhat jenom na ověřování.</span><span class="sxs-lookup"><span data-stu-id="098fb-182">Never rely on validation alone.</span></span> <span data-ttu-id="098fb-183">Vždy kódování nedůvěryhodného vstupu před výstupem bez ohledu na to, jaké ověření nebo úpravu bylo provedeno.</span><span class="sxs-lookup"><span data-stu-id="098fb-183">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
