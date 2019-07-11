---
title: Zabránit webů skriptování mezi weby (XSS) v ASP.NET Core
author: rick-anderson
description: Další informace o skriptování mezi weby (XSS) a techniky pro řešení tohoto ohrožení zabezpečení v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 10/02/2018
uid: security/cross-site-scripting
ms.openlocfilehash: 1e9e988be68313cfd493832519c1be89335d6e48
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815213"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="f3ff5-103">Zabránit webů skriptování mezi weby (XSS) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3ff5-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="f3ff5-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f3ff5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f3ff5-105">Skriptování mezi weby (XSS) je ohrožení zabezpečení, která umožňuje útočníkovi umístí skripty na straně klienta (obvykle JavaScriptu) do webových stránek.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="f3ff5-106">Když ostatní uživatelé načíst ovlivněné stránek, které budou spuštěny skripty útočníka, umožňuje útočníkovi krádež souborů cookie a relace tokeny, změňte obsah webové stránky pomocí manipulace s modelem DOM nebo přesměrovat prohlížeč na jinou stránku.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="f3ff5-107">Ohrožení zabezpečení XSS obecně dojít, když aplikace přijímá vstup uživatele a uloží jej na stránku bez ověřování, kódování nebo ho uvozovací znaky.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="f3ff5-108">Ochrana aplikace proti skriptování mezi servery</span><span class="sxs-lookup"><span data-stu-id="f3ff5-108">Protecting your application against XSS</span></span>

<span data-ttu-id="f3ff5-109">Na základní úrovni XSS funguje tak, přičemž aplikace do vkládání `<script>` značky do vykreslované stránky nebo vložením `On*` událostí do elementu.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="f3ff5-110">Vývojáři by měl následujícím postupem ochrany před únikem informací pro Vyhýbejte XSS do své aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="f3ff5-111">Nikdy nepoužili nedůvěryhodná data váš vstup ve formátu HTML, pokud postupujte podle zbývajících pokynů.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="f3ff5-112">Nedůvěryhodná data jsou všechna data, která mohou být řízena útočník, vstupy formuláře HTML, řetězce dotazů, hlavičky protokolu HTTP, dokonce i data source z databáze, protože útočník může být schopni porušení zabezpečení databáze, i v případě, že se nemůže pronikají do vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="f3ff5-113">Před přepnutím nedůvěryhodná data uvnitř elementu HTML Ujistěte se, že je kódováno jazykem HTML.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="f3ff5-114">Kódování HTML, jako má znaků &lt; a změny do bezpečného formuláře jako &amp;lt;</span><span class="sxs-lookup"><span data-stu-id="f3ff5-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="f3ff5-115">Před uvedením nedůvěryhodná data do atributu HTML Ujistěte se, že je kódováno jazykem HTML.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="f3ff5-116">Kódování atributu HTML je nadstavbou jazyka kódování HTML a další znaky zakóduje jako "a".</span><span class="sxs-lookup"><span data-stu-id="f3ff5-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="f3ff5-117">Před přepnutím nedůvěryhodná data do jazyka JavaScript umístíte data v elementu HTML, jehož obsah načíst za běhu.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="f3ff5-118">Pokud to není možné, pak Ujistěte se, že data jsou kódované jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="f3ff5-119">Kódování JavaScript trvá nebezpečné znaky pro JavaScript a nahradí je jejich hex, například &lt; by být zakódován jako `\u003C`.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="f3ff5-120">Před přepnutím nedůvěryhodná data do řetězce dotazu adresy URL Ujistěte se, že je kódování URL.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-razor"></a><span data-ttu-id="f3ff5-121">Kódování HTML pomocí syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="f3ff5-121">HTML Encoding using Razor</span></span>

<span data-ttu-id="f3ff5-122">Modul Razor použité v MVC automaticky kóduje všechny výstupní zdrojem proměnné, pokud pracujete ve skutečnosti intenzivně zabránit, aby ji uděláte.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-122">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="f3ff5-123">Použije pravidla kódování atributu HTML při každém použití *@* směrnice.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="f3ff5-124">Ve formátu HTML kódování atributu je nadstavbou jazyka kódování HTML, to znamená, že nemáte problém sami se určuje, zda by měl používat kódování HTML nebo kódování atributu HTML.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="f3ff5-125">Musíte zajistit, že používáte pouze v kontextu HTML, ne už při pokusu o vložení nedůvěryhodný vstup přímo do jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="f3ff5-126">Pomocné rutiny značek se také kódování vstup, který použijete v parametrů tag.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="f3ff5-127">Proveďte následující zobrazení Razor:</span><span class="sxs-lookup"><span data-stu-id="f3ff5-127">Take the following Razor view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="f3ff5-128">Toto zobrazení vypíše obsah *untrustedInput* proměnné.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="f3ff5-129">Tato proměnná obsahuje některé znaky, které se používají v útoky XSS, konkrétně &lt;, "a &gt;.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="f3ff5-130">Zkoumání zdroj ukazuje vykresleného výstupu zakódován jako:</span><span class="sxs-lookup"><span data-stu-id="f3ff5-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="f3ff5-131">ASP.NET Core MVC nabízí `HtmlString` třídy, který není kódován automaticky při výstupu.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="f3ff5-132">To byste nikdy neměli používat v kombinaci s nedůvěryhodnému vstupu jako to bude vystavovat chybu XSS.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-razor"></a><span data-ttu-id="f3ff5-133">JavaScript kódování pomocí syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="f3ff5-133">JavaScript Encoding using Razor</span></span>

<span data-ttu-id="f3ff5-134">Může nastat situace, které chcete vložit do jazyka JavaScript ke zpracování v zobrazení hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="f3ff5-135">Existují dva způsoby, jak to provést.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-135">There are two ways to do this.</span></span> <span data-ttu-id="f3ff5-136">Nejbezpečnější způsob, jak vložit hodnoty je hodnota atributu data značky a načíst v JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="f3ff5-137">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f3ff5-137">For example:</span></span>

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

<span data-ttu-id="f3ff5-138">To vytvoří následující kód HTML</span><span class="sxs-lookup"><span data-stu-id="f3ff5-138">This will produce the following HTML</span></span>

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

<span data-ttu-id="f3ff5-139">Který při spuštění, zobrazí se pak následující:</span><span class="sxs-lookup"><span data-stu-id="f3ff5-139">Which, when it runs, will render the following:</span></span>

```none
<"123">
   <"123">
   ```

<span data-ttu-id="f3ff5-140">Kodér JavaScript můžete také volat přímo:</span><span class="sxs-lookup"><span data-stu-id="f3ff5-140">You can also call the JavaScript encoder directly:</span></span>

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

<span data-ttu-id="f3ff5-141">To bude vykreslení v prohlížeči následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="f3ff5-141">This will render in the browser as follows:</span></span>

```html
<script>
       document.write("\u003C\u0022123\u0022\u003E");
   </script>
   ```

>[!WARNING]
> <span data-ttu-id="f3ff5-142">Nedůvěryhodný vstup v jazyce JavaScript, k vytváření prvků modelu DOM není zřetězit.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-142">Don't concatenate untrusted input in JavaScript to create DOM elements.</span></span> <span data-ttu-id="f3ff5-143">Měli byste použít `createElement()` a odpovídajícím způsobem, jako přiřadit hodnoty vlastností `node.TextContent=`, nebo použijte `element.SetAttribute()` / `element[attribute]=` jinak zpřístupníte sami na základě modelu DOM XSS.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-143">You should use `createElement()` and assign property values appropriately such as `node.TextContent=`, or use `element.SetAttribute()`/`element[attribute]=` otherwise you expose yourself to DOM-based XSS.</span></span>

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="f3ff5-144">Přístup k kodérů v kódu</span><span class="sxs-lookup"><span data-stu-id="f3ff5-144">Accessing encoders in code</span></span>

<span data-ttu-id="f3ff5-145">Jsou k dispozici dvě možnosti, jak váš kód kodérů HTML, JavaScript a adresu URL, můžete vložit pomocí [injektáž závislostí](xref:fundamentals/dependency-injection) nebo můžete použít výchozí kodérů součástí `System.Text.Encodings.Web` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-145">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="f3ff5-146">Pokud používáte výchozí kodérů a veškeré použité k rozsahů znaků považovány za bezpečné projeví – výchozí kodérů použijte nejbezpečnější pravidla kódování, je to možné.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-146">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="f3ff5-147">Použití konfigurovatelné kodérů prostřednictvím DI zabere vaše konstruktory *HtmlEncoder*, *JavaScriptEncoder* a *UrlEncoder* parametr podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-147">To use the configurable encoders via DI your constructors should take an *HtmlEncoder*, *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="f3ff5-148">Například;</span><span class="sxs-lookup"><span data-stu-id="f3ff5-148">For example;</span></span>

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

## <a name="encoding-url-parameters"></a><span data-ttu-id="f3ff5-149">Kódování adresy URL parametry</span><span class="sxs-lookup"><span data-stu-id="f3ff5-149">Encoding URL Parameters</span></span>

<span data-ttu-id="f3ff5-150">Pokud chcete sestavit řetězec dotazu adresy URL s nedůvěryhodný vstup jako hodnotu použít `UrlEncoder` ke kódování hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-150">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="f3ff5-151">Například</span><span class="sxs-lookup"><span data-stu-id="f3ff5-151">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="f3ff5-152">Po kódování encodedValue bude obsahovat proměnnou `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-152">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="f3ff5-153">Mezery, nabídek, interpunkce a dalších problematické znaky budou procenta kódovány za účelem jejich šestnáctkové hodnoty, například znak mezery se stanou % 20.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-153">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="f3ff5-154">Nepoužívejte nedůvěryhodnému vstupu jako část cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-154">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="f3ff5-155">Vždycky předáte nedůvěryhodný vstup jako hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-155">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="f3ff5-156">Přizpůsobení u kodérů</span><span class="sxs-lookup"><span data-stu-id="f3ff5-156">Customizing the Encoders</span></span>

<span data-ttu-id="f3ff5-157">Ve výchozím nastavení kodérů pomocí seznamu bezpečných omezeno na rozsahu základní latinky Unicode a kódování všechny znaky mimo tento rozsah jako jejich ekvivalenty kód znaku.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-157">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="f3ff5-158">Toto chování Taghelperu Razor a HtmlHelper vykreslování ovlivní také, jak se bude používat u kodérů pro výstupní vaše řetězce.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-158">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="f3ff5-159">Zdůvodnění to je pro ochranu před chybami neznámý nebo budoucí prohlížeče (předchozí chyby prohlížeče mít zasekne analýzy založené na zpracování jiných než anglických znaků).</span><span class="sxs-lookup"><span data-stu-id="f3ff5-159">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="f3ff5-160">Pokud vaše webová stránka značně používá jiné znaky než latinku, jako je například čínština, cyrilice, nebo jinými toto není pravděpodobně chování, které chcete.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-160">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="f3ff5-161">Můžete přizpůsobit seznamy bezpečných kodér zahrnout rozsahy vhodnými pro vaši aplikaci při spuštění v kódování Unicode `ConfigureServices()`.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-161">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="f3ff5-162">Například pomocí výchozí konfigurace můžete použít syntaxi Razor HtmlHelper takto;</span><span class="sxs-lookup"><span data-stu-id="f3ff5-162">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="f3ff5-163">Po zobrazení zdrojového kódu webové stránky uvidíte, že má se vykreslí následujícím způsobem čínštině kódování;</span><span class="sxs-lookup"><span data-stu-id="f3ff5-163">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="f3ff5-164">Chcete-li rozšířit znaky považované za bezpečné kodér by vložíte následující řádek do `ConfigureServices()` metoda `startup.cs`;</span><span class="sxs-lookup"><span data-stu-id="f3ff5-164">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="f3ff5-165">Tento příklad rozšiřuje seznamu bezpečných zahrnout CjkUnifiedIdeographs rozsah Unicode.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-165">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="f3ff5-166">Teď už vykresleného výstupu</span><span class="sxs-lookup"><span data-stu-id="f3ff5-166">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="f3ff5-167">Seznamu bezpečných rozsahy jsou zadané jako grafy kódu Unicode, ne jazyky.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-167">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="f3ff5-168">[Unicode standard](https://unicode.org/) má seznam [kódu grafy](https://www.unicode.org/charts/index.html) můžete použít k vyhledání graf obsahující znaky.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-168">The [Unicode standard](https://unicode.org/) has a list of [code charts](https://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="f3ff5-169">Každý kodér, Html, JavaScript a adresu Url, musí být nakonfigurované samostatně.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-169">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="f3ff5-170">Přizpůsobení seznamu bezpečných ovlivňuje pouze Source prostřednictvím DI kodérů.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-170">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="f3ff5-171">Pokud přímý přístup k kodéru prostřednictvím `System.Text.Encodings.Web.*Encoder.Default` pak výchozí základní latinky se použije pouze safelist.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-171">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="f3ff5-172">Pokud byste umístit kódování vzít?</span><span class="sxs-lookup"><span data-stu-id="f3ff5-172">Where should encoding take place?</span></span>

<span data-ttu-id="f3ff5-173">Obecné přijme, postup je, že kódování probíhá místě výstup a kódovaného hodnoty by nikdy neměly být uloženy v databázi.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-173">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="f3ff5-174">Kódování místě výstup vám umožní změnit používání dat, například z HTML na hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-174">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="f3ff5-175">Také umožňuje snadno prohledávat svá data bez nutnosti kódování hodnoty před vyhledáváním a umožňuje vám umožní využívat jakékoli změny a opravy chyb. kodérů.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-175">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="f3ff5-176">Ověření jako XSS techniku ochrany před únikem informací</span><span class="sxs-lookup"><span data-stu-id="f3ff5-176">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="f3ff5-177">Ověření může být užitečný nástroj v omezení útoky XSS.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-177">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="f3ff5-178">Například číselných řetězců obsahující pouze znaky 0-9, nebude spustí XSS útoku.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-178">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="f3ff5-179">Ověření bude složitější, při přijetí HTML vstup uživatele.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-179">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="f3ff5-180">Analýza elementu input kódu HTML je obtížné, pokud není možné.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-180">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="f3ff5-181">Markdown, společně se analyzátor, který odstraní vložený HTML, je bezpečnější možnost pro příjem formátovaný vstup.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-181">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="f3ff5-182">Nikdy spoléhat na ověřovací samostatně.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-182">Never rely on validation alone.</span></span> <span data-ttu-id="f3ff5-183">Vždy kódování nedůvěryhodný vstup před výstupu, bez ohledu na to, jaké ověřování nebo čištění pro zadávání byla provedena.</span><span class="sxs-lookup"><span data-stu-id="f3ff5-183">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
