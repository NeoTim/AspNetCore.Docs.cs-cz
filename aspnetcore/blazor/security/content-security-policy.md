---
title: Vynutili zásady zabezpečení obsahu pro ASP.NET CoreBlazor
author: guardrex
description: Naučte se používat zásady zabezpečení obsahu (CSP) s aplikacemi ASP.NET Core Blazor k ochraně proti útokům skriptování mezi weby (XSS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/content-security-policy
ms.openlocfilehash: 5c53ac64d3ae1b365b40c519eb119f913d58cad1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402439"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="f8995-103">Vynutili zásady zabezpečení obsahu pro ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="f8995-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="f8995-104">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f8995-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f8995-105">[Skriptování mezi weby (XSS)](xref:security/cross-site-scripting) je ohrožení zabezpečení v případě, kdy útočník umístí do vykresleného obsahu aplikace jeden nebo více škodlivých skriptů na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f8995-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="f8995-106">Zásady zabezpečení obsahu (CSP) pomáhají chránit před útoky XSS pomocí informování prohlížeče o platných verzích:</span><span class="sxs-lookup"><span data-stu-id="f8995-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="f8995-107">Zdroje pro načtený obsah, včetně skriptů, šablon stylů a obrázků.</span><span class="sxs-lookup"><span data-stu-id="f8995-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="f8995-108">Akce provedená stránkou a určením povolených cílů adres URL formulářů.</span><span class="sxs-lookup"><span data-stu-id="f8995-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="f8995-109">Moduly plug-in, které lze načíst.</span><span class="sxs-lookup"><span data-stu-id="f8995-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="f8995-110">Pokud chcete použít zprostředkovatele CSP pro aplikaci, vývojář určí několik *direktiv* zabezpečení obsahu CSP v jedné nebo několika `Content-Security-Policy` hlavičkách nebo `<meta>` značkách.</span><span class="sxs-lookup"><span data-stu-id="f8995-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="f8995-111">Zásady jsou vyhodnocovány prohlížečem během načítání stránky.</span><span class="sxs-lookup"><span data-stu-id="f8995-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="f8995-112">Prohlížeč zkontroluje zdroje stránky a určí, zda splňují požadavky na direktivy zabezpečení obsahu.</span><span class="sxs-lookup"><span data-stu-id="f8995-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="f8995-113">Pokud se pro prostředek nesplní směrnice zásad, prohlížeč nenačte prostředek.</span><span class="sxs-lookup"><span data-stu-id="f8995-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="f8995-114">Představte si třeba zásadu, která nepovoluje skripty třetích stran.</span><span class="sxs-lookup"><span data-stu-id="f8995-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="f8995-115">Pokud stránka obsahuje `<script>` značku se zdrojem třetí strany v `src` atributu, prohlížeč brání načtení skriptu.</span><span class="sxs-lookup"><span data-stu-id="f8995-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="f8995-116">CSP se podporuje ve většině moderních desktopových a mobilních prohlížečích, včetně Chrome, Edge, Firefox, Operau a Safari.</span><span class="sxs-lookup"><span data-stu-id="f8995-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="f8995-117">Pro aplikace se doporučuje CSP Blazor .</span><span class="sxs-lookup"><span data-stu-id="f8995-117">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="f8995-118">Direktivy zásad</span><span class="sxs-lookup"><span data-stu-id="f8995-118">Policy directives</span></span>

<span data-ttu-id="f8995-119">Minimálně zadejte následující direktivy a zdroje pro Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="f8995-119">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="f8995-120">Podle potřeby přidejte další direktivy a zdroje.</span><span class="sxs-lookup"><span data-stu-id="f8995-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="f8995-121">V části [použití zásad](#apply-the-policy) v tomto článku se používají následující direktivy, kde jsou k dispozici příklady zásad zabezpečení pro Blazor WebAssembly a Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="f8995-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="f8995-122">[Base-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): omezí adresy URL pro `<base>` značku stránky.</span><span class="sxs-lookup"><span data-stu-id="f8995-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="f8995-123">Určete `self` , aby bylo jasné, že původ aplikace, včetně schématu a čísla portu, je platným zdrojem.</span><span class="sxs-lookup"><span data-stu-id="f8995-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="f8995-124">[blokování všech smíšených obsahu](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): zabraňuje načtení smíšeného obsahu HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f8995-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="f8995-125">[Výchozí hodnota-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): označuje zálohu pro zdrojové direktivy, které nejsou explicitně určeny zásadou.</span><span class="sxs-lookup"><span data-stu-id="f8995-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="f8995-126">Určete `self` , aby bylo jasné, že původ aplikace, včetně schématu a čísla portu, je platným zdrojem.</span><span class="sxs-lookup"><span data-stu-id="f8995-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="f8995-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): označuje platné zdroje pro obrázky.</span><span class="sxs-lookup"><span data-stu-id="f8995-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="f8995-128">Určuje `data:` , že se mají povolit načítání imagí z `data:` adres URL.</span><span class="sxs-lookup"><span data-stu-id="f8995-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="f8995-129">Určuje `https:` , že se mají povolit načítání imagí z koncových bodů https.</span><span class="sxs-lookup"><span data-stu-id="f8995-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="f8995-130">[objekt-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): označuje platné zdroje pro `<object>` značky, `<embed>` a `<applet>` .</span><span class="sxs-lookup"><span data-stu-id="f8995-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="f8995-131">Určete `none` , aby se zabránilo všem zdrojům adresy URL.</span><span class="sxs-lookup"><span data-stu-id="f8995-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="f8995-132">[skript-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): označuje platné zdroje pro skripty.</span><span class="sxs-lookup"><span data-stu-id="f8995-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="f8995-133">Zadejte `https://stackpath.bootstrapcdn.com/` zdroj hostitele pro skripty Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="f8995-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="f8995-134">Určete `self` , aby bylo jasné, že původ aplikace, včetně schématu a čísla portu, je platným zdrojem.</span><span class="sxs-lookup"><span data-stu-id="f8995-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="f8995-135">V Blazor WebAssembly aplikaci:</span><span class="sxs-lookup"><span data-stu-id="f8995-135">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="f8995-136">Zadejte následující hodnoty hash, aby bylo Blazor WebAssembly možné načíst požadované vložené skripty:</span><span class="sxs-lookup"><span data-stu-id="f8995-136">Specify the following hashes to permit the required Blazor WebAssembly inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="f8995-137">Určete `unsafe-eval` , že se mají použít `eval()` metody a pro vytvoření kódu z řetězců.</span><span class="sxs-lookup"><span data-stu-id="f8995-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="f8995-138">V Blazor Server aplikaci zadejte `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hodnotu hash vloženého skriptu, který provádí záložní detekci pro šablony stylů.</span><span class="sxs-lookup"><span data-stu-id="f8995-138">In a Blazor Server app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="f8995-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): označuje platné zdroje pro šablony stylů.</span><span class="sxs-lookup"><span data-stu-id="f8995-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="f8995-140">Zadejte `https://stackpath.bootstrapcdn.com/` zdroj hostitele pro Bootstrap šablon stylů.</span><span class="sxs-lookup"><span data-stu-id="f8995-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="f8995-141">Určete `self` , aby bylo jasné, že původ aplikace, včetně schématu a čísla portu, je platným zdrojem.</span><span class="sxs-lookup"><span data-stu-id="f8995-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="f8995-142">Určete `unsafe-inline` , aby bylo možné použít vložené styly.</span><span class="sxs-lookup"><span data-stu-id="f8995-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="f8995-143">Vložená deklarace se vyžaduje pro uživatelské rozhraní v Blazor Server aplikacích pro opětovné připojení klienta a serveru po počátečním požadavku.</span><span class="sxs-lookup"><span data-stu-id="f8995-143">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="f8995-144">V budoucí verzi je možné odebrat vložené styly, aby `unsafe-inline` se už nevyžadovaly.</span><span class="sxs-lookup"><span data-stu-id="f8995-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="f8995-145">[upgrade – nezabezpečené – požadavky](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): označuje, že adresy URL obsahu z nezabezpečených (http) zdrojů by měly být zabezpečeny prostřednictvím protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f8995-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="f8995-146">Předchozí direktivy jsou podporovány všemi prohlížeči kromě aplikace Microsoft Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="f8995-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="f8995-147">Získání hodnot hash SHA pro další vložené skripty:</span><span class="sxs-lookup"><span data-stu-id="f8995-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="f8995-148">Použijte zprostředkovatele CSP, který je uveden v části [použití zásad](#apply-the-policy) .</span><span class="sxs-lookup"><span data-stu-id="f8995-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="f8995-149">Přístup ke konzole nástroje pro vývojáře v prohlížeči, při které se aplikace spouští místně</span><span class="sxs-lookup"><span data-stu-id="f8995-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="f8995-150">Prohlížeč vypočítá a zobrazí hodnoty hash pro blokované skripty, když `meta` je přítomna hlavička nebo značka CSP.</span><span class="sxs-lookup"><span data-stu-id="f8995-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="f8995-151">Zkopírujte hodnoty hash poskytované prohlížečem do `script-src` zdrojů.</span><span class="sxs-lookup"><span data-stu-id="f8995-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="f8995-152">Kolem každé hodnoty hash použijte jednoduché uvozovky.</span><span class="sxs-lookup"><span data-stu-id="f8995-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="f8995-153">Informace o podpoře prohlížečů úrovně zásad zabezpečení obsahu 2 najdete v tématu [Jak můžu použít: úroveň zásad zabezpečení obsahu 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span><span class="sxs-lookup"><span data-stu-id="f8995-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="f8995-154">Použít zásady</span><span class="sxs-lookup"><span data-stu-id="f8995-154">Apply the policy</span></span>

<span data-ttu-id="f8995-155">Použijte `<meta>` značku pro aplikování zásad:</span><span class="sxs-lookup"><span data-stu-id="f8995-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="f8995-156">Nastavte hodnotu `http-equiv` atributu na `Content-Security-Policy` .</span><span class="sxs-lookup"><span data-stu-id="f8995-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="f8995-157">Umístěte direktivy do `content` hodnoty atributu.</span><span class="sxs-lookup"><span data-stu-id="f8995-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="f8995-158">Oddělte direktivy středníkem ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="f8995-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="f8995-159">Vždy umístit `meta` značku do `<head>` obsahu.</span><span class="sxs-lookup"><span data-stu-id="f8995-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="f8995-160">V následujících částech jsou uvedeny příklady zásad pro Blazor WebAssembly a Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="f8995-160">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="f8995-161">Tyto příklady jsou ve verzi tohoto článku pro každou verzi Blazor .</span><span class="sxs-lookup"><span data-stu-id="f8995-161">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="f8995-162">Pokud chcete použít verzi vhodnou pro vaši verzi, vyberte na této webové stránce verzi dokumentu s rozevíracím selektorem **verzí** .</span><span class="sxs-lookup"><span data-stu-id="f8995-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### Blazor WebAssembly

<span data-ttu-id="f8995-163">V `<head>` obsahu `wwwroot/index.html` stránky hostitele použijte direktivy popsané v části [direktivy zásad](#policy-directives) :</span><span class="sxs-lookup"><span data-stu-id="f8995-163">In the `<head>` content of the `wwwroot/index.html` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### Blazor Server

<span data-ttu-id="f8995-164">V `<head>` obsahu `Pages/_Host.cshtml` stránky hostitele použijte direktivy popsané v části [direktivy zásad](#policy-directives) :</span><span class="sxs-lookup"><span data-stu-id="f8995-164">In the `<head>` content of the `Pages/_Host.cshtml` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a><span data-ttu-id="f8995-165">Omezení meta značek</span><span class="sxs-lookup"><span data-stu-id="f8995-165">Meta tag limitations</span></span>

<span data-ttu-id="f8995-166">`<meta>`Zásady značek nepodporují následující direktivy:</span><span class="sxs-lookup"><span data-stu-id="f8995-166">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="f8995-167">předchůdci v rámci</span><span class="sxs-lookup"><span data-stu-id="f8995-167">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="f8995-168">Sestava – do</span><span class="sxs-lookup"><span data-stu-id="f8995-168">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="f8995-169">identifikátor URI sestavy</span><span class="sxs-lookup"><span data-stu-id="f8995-169">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="f8995-170">úložišti</span><span class="sxs-lookup"><span data-stu-id="f8995-170">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="f8995-171">Pro podporu předchozích direktiv použijte záhlaví s názvem `Content-Security-Policy` .</span><span class="sxs-lookup"><span data-stu-id="f8995-171">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="f8995-172">Řetězec direktivy je hodnota hlavičky.</span><span class="sxs-lookup"><span data-stu-id="f8995-172">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="f8995-173">Testování zásad a přijímání sestav o porušení</span><span class="sxs-lookup"><span data-stu-id="f8995-173">Test a policy and receive violation reports</span></span>

<span data-ttu-id="f8995-174">Testování pomáhá ověřit, jestli se skripty třetích stran při vytváření počátečních zásad nechtěně nezablokovaly.</span><span class="sxs-lookup"><span data-stu-id="f8995-174">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="f8995-175">Chcete-li testovat zásadu v časovém intervalu bez vynucování direktiv zásad, nastavte `<meta>` `http-equiv` atribut značky nebo záhlaví zásady na základě záhlaví na `Content-Security-Policy-Report-Only` .</span><span class="sxs-lookup"><span data-stu-id="f8995-175">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="f8995-176">Zprávy o chybách se odesílají do zadané adresy URL jako dokumenty JSON.</span><span class="sxs-lookup"><span data-stu-id="f8995-176">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="f8995-177">Další informace najdete v tématu [MDN web Docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span><span class="sxs-lookup"><span data-stu-id="f8995-177">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="f8995-178">Pro vytváření sestav o porušeních, když je zásada aktivní, se podívejte na následující články:</span><span class="sxs-lookup"><span data-stu-id="f8995-178">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="f8995-179">Sestava – do</span><span class="sxs-lookup"><span data-stu-id="f8995-179">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="f8995-180">identifikátor URI sestavy</span><span class="sxs-lookup"><span data-stu-id="f8995-180">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="f8995-181">I když `report-uri` se už nedoporučuje používat, obě direktivy by se měly používat, dokud `report-to` je nepodporují všechny hlavní prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f8995-181">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="f8995-182">Nepoužívejte výhradně `report-uri` , protože podpora pro `report-uri` je předmětem vyřazení *at any time* z prohlížečů kdykoli.</span><span class="sxs-lookup"><span data-stu-id="f8995-182">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="f8995-183">`report-uri`Pokud je podpora plně podporovaná, odeberte ji ve svých zásadách `report-to` .</span><span class="sxs-lookup"><span data-stu-id="f8995-183">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="f8995-184">Informace o tom, jak se `report-to` dá sledovat, najdete v tématu [použití: Report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span><span class="sxs-lookup"><span data-stu-id="f8995-184">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="f8995-185">Otestujte a aktualizujte zásady aplikace při každé vydané verzi.</span><span class="sxs-lookup"><span data-stu-id="f8995-185">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f8995-186">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="f8995-186">Troubleshoot</span></span>

* <span data-ttu-id="f8995-187">V konzole nástroje pro vývojáře v prohlížeči se zobrazí chyby.</span><span class="sxs-lookup"><span data-stu-id="f8995-187">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="f8995-188">Prohlížeče poskytují informace o:</span><span class="sxs-lookup"><span data-stu-id="f8995-188">Browsers provide information about:</span></span>
  * <span data-ttu-id="f8995-189">Prvky, které nejsou v rozporu s touto zásadou.</span><span class="sxs-lookup"><span data-stu-id="f8995-189">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="f8995-190">Postup úpravy zásady tak, aby umožňovaly blokovanou položku.</span><span class="sxs-lookup"><span data-stu-id="f8995-190">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="f8995-191">Zásady jsou zcela účinné pouze v případě, že prohlížeč klienta podporuje všechny zahrnuté direktivy.</span><span class="sxs-lookup"><span data-stu-id="f8995-191">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="f8995-192">Aktuální matici podpory prohlížeče najdete v tématu [použití: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span><span class="sxs-lookup"><span data-stu-id="f8995-192">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8995-193">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f8995-193">Additional resources</span></span>

* [<span data-ttu-id="f8995-194">MDN web Docs: Content-Security-Policy</span><span class="sxs-lookup"><span data-stu-id="f8995-194">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="f8995-195">Úroveň zásad zabezpečení obsahu 2</span><span class="sxs-lookup"><span data-stu-id="f8995-195">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="f8995-196">Vyhodnocovací filtr Google CSP</span><span class="sxs-lookup"><span data-stu-id="f8995-196">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)