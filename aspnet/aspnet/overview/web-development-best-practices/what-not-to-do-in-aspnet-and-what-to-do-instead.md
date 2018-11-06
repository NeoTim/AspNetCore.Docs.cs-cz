---
uid: aspnet/overview/web-development-best-practices/what-not-to-do-in-aspnet-and-what-to-do-instead
title: Co nedělat v ASP.NET a jak to udělat správně | Dokumentace Microsoftu
author: Rick-Anderson
description: Toto téma popisuje několik běžných chyb, které uživatelé provést v rámci webové projekty ASP.NET. Poskytuje doporučení pro co dělat, aby se zabránilo tyto commo...
ms.author: riande
ms.date: 05/08/2014
ms.assetid: c39b9965-545c-4b04-8f55-21be7f28a9e5
msc.legacyurl: /aspnet/overview/web-development-best-practices/what-not-to-do-in-aspnet-and-what-to-do-instead
msc.type: authoredcontent
ms.openlocfilehash: 69040ca6a1ddeaf029062da45475dd2171b1afa6
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021440"
---
<a name="what-not-to-do-in-aspnet-and-what-to-do-instead"></a><span data-ttu-id="6b871-104">Co nedělat v ASP.NET a jak to udělat správně</span><span class="sxs-lookup"><span data-stu-id="6b871-104">What not to do in ASP.NET, and what to do instead</span></span>
====================
<span data-ttu-id="6b871-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="6b871-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="6b871-106">Toto téma popisuje několik běžných chyb, které uživatelé provést v rámci webové projekty ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="6b871-106">This topic describes several common mistakes people make within ASP.NET web projects.</span></span> <span data-ttu-id="6b871-107">Poskytuje doporučení pro co dělat, aby se zabránilo těchto běžných chyb.</span><span class="sxs-lookup"><span data-stu-id="6b871-107">It provides recommendations for what you should do to avoid these common mistakes.</span></span> <span data-ttu-id="6b871-108">Je založen na [prezentace](http://vimeo.com/68390507) podle **Damianem Edwardsem** na konferenci Ndc vývojáři.</span><span class="sxs-lookup"><span data-stu-id="6b871-108">It is based on a [presentation](http://vimeo.com/68390507) by **Damian Edwards** at Norwegian Developers Conference.</span></span>


## <a name="disclaimer"></a><span data-ttu-id="6b871-109">Právní omezení</span><span class="sxs-lookup"><span data-stu-id="6b871-109">Disclaimer</span></span>

<span data-ttu-id="6b871-110">Toto téma není určené úplnou příručku k zajištění, že aplikace je zabezpečené a efektivní.</span><span class="sxs-lookup"><span data-stu-id="6b871-110">This topic is not intended as a complete guide to ensure your application is secure and efficient.</span></span> <span data-ttu-id="6b871-111">Stále musíte dodržovat osvědčené postupy pro zabezpečení a výkonu, které nejsou uvedené v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="6b871-111">You still need to follow best practices for security and performance that are not outlined in this topic.</span></span> <span data-ttu-id="6b871-112">Navrhne pouze jak se vyhnout běžné chyby týkající se třídy rozhraní .NET a procesy.</span><span class="sxs-lookup"><span data-stu-id="6b871-112">It only suggests how to avoid common mistakes related to .NET classes and processes.</span></span>

## <a name="overview"></a><span data-ttu-id="6b871-113">Přehled</span><span class="sxs-lookup"><span data-stu-id="6b871-113">Overview</span></span>

<span data-ttu-id="6b871-114">Toto téma obsahuje následující oddíly:</span><span class="sxs-lookup"><span data-stu-id="6b871-114">This topic contains the following sections:</span></span>

- [<span data-ttu-id="6b871-115">Standardy dodržování předpisů</span><span class="sxs-lookup"><span data-stu-id="6b871-115">Standards Compliance</span></span>](#standards)

    - [<span data-ttu-id="6b871-116">Adaptérů ovládacích prvků</span><span class="sxs-lookup"><span data-stu-id="6b871-116">Control Adapters</span></span>](#adapters)
    - [<span data-ttu-id="6b871-117">Vlastnosti stylu v ovládacích prvcích</span><span class="sxs-lookup"><span data-stu-id="6b871-117">Style Properties on Controls</span></span>](#styleprop)
    - [<span data-ttu-id="6b871-118">Stránky a zpětná volání ovládacího prvku</span><span class="sxs-lookup"><span data-stu-id="6b871-118">Page and Control Callbacks</span></span>](#callback)
    - [<span data-ttu-id="6b871-119">Zjišťování schopností prohlížeče</span><span class="sxs-lookup"><span data-stu-id="6b871-119">Browser Capability Detection</span></span>](#browsercap)
- [<span data-ttu-id="6b871-120">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="6b871-120">Security</span></span>](#security)

    - [<span data-ttu-id="6b871-121">Žádost o ověření</span><span class="sxs-lookup"><span data-stu-id="6b871-121">Request Validation</span></span>](#validation)
    - [<span data-ttu-id="6b871-122">Ověřování pomocí formulářů bez souborů cookie a relace</span><span class="sxs-lookup"><span data-stu-id="6b871-122">Cookieless Forms Authentication and Session</span></span>](#cookieless)
    - [<span data-ttu-id="6b871-123">EnableViewStateMac</span><span class="sxs-lookup"><span data-stu-id="6b871-123">EnableViewStateMac</span></span>](#viewstatemac)
    - [<span data-ttu-id="6b871-124">Úrovni Medium Trust</span><span class="sxs-lookup"><span data-stu-id="6b871-124">Medium Trust</span></span>](#medium)
    - [<span data-ttu-id="6b871-125">&lt;appSettings&gt;</span><span class="sxs-lookup"><span data-stu-id="6b871-125">&lt;appSettings&gt;</span></span>](#appsettings)
    - [<span data-ttu-id="6b871-126">UrlPathEncode</span><span class="sxs-lookup"><span data-stu-id="6b871-126">UrlPathEncode</span></span>](#urlpathencode)
- [<span data-ttu-id="6b871-127">Spolehlivost a výkon</span><span class="sxs-lookup"><span data-stu-id="6b871-127">Reliability and Performance</span></span>](#performance)

    - [<span data-ttu-id="6b871-128">PreSendRequestHeaders a PreSendRequestContent</span><span class="sxs-lookup"><span data-stu-id="6b871-128">PreSendRequestHeaders and PreSendRequestContent</span></span>](#presend)
    - [<span data-ttu-id="6b871-129">Události asynchronní stránky s webovými formuláři</span><span class="sxs-lookup"><span data-stu-id="6b871-129">Asynchronous Page Events with Web Forms</span></span>](#asyncevents)
    - [<span data-ttu-id="6b871-130">Oheň a zapomenout práce</span><span class="sxs-lookup"><span data-stu-id="6b871-130">Fire-and-Forget Work</span></span>](#fire)
    - [<span data-ttu-id="6b871-131">Obsah Entity žádosti</span><span class="sxs-lookup"><span data-stu-id="6b871-131">Request Entity Body</span></span>](#requestentity)
    - [<span data-ttu-id="6b871-132">Response.Redirect a metody Response.End</span><span class="sxs-lookup"><span data-stu-id="6b871-132">Response.Redirect and Response.End</span></span>](#redirect)
    - [<span data-ttu-id="6b871-133">EnableViewState a ViewStateMode</span><span class="sxs-lookup"><span data-stu-id="6b871-133">EnableViewState and ViewStateMode</span></span>](#viewstatemode)
    - [<span data-ttu-id="6b871-134">SqlMembershipProvider</span><span class="sxs-lookup"><span data-stu-id="6b871-134">SqlMembershipProvider</span></span>](#sqlprovider)
    - [<span data-ttu-id="6b871-135">Dlouhé žádosti spuštění (> 110 sekund)</span><span class="sxs-lookup"><span data-stu-id="6b871-135">Long Running Requests (>110 seconds)</span></span>](#long)

<a id="standards"></a>

## <a name="standards-compliance"></a><span data-ttu-id="6b871-136">Standardy dodržování předpisů</span><span class="sxs-lookup"><span data-stu-id="6b871-136">Standards Compliance</span></span>

<a id="adapters"></a>

### <a name="control-adapters"></a><span data-ttu-id="6b871-137">Adaptérů ovládacích prvků</span><span class="sxs-lookup"><span data-stu-id="6b871-137">Control Adapters</span></span>

<span data-ttu-id="6b871-138">Doporučení: Přestat používat adaptérů ovládacích prvků pro adaptivní vykreslování a místo toho použít dotazy na média šablon stylů CSS a HTML standardům.</span><span class="sxs-lookup"><span data-stu-id="6b871-138">Recommendation: Stop using control adapters for adaptive rendering, and instead use CSS media queries and standards-compliant HTML.</span></span>

<span data-ttu-id="6b871-139">Ovládací prvky adaptéry byly zavedeny v rozhraní .NET 2.0 k vykreslení prezentaci kódu, který byl přizpůsoben pro různá zařízení a prostředí.</span><span class="sxs-lookup"><span data-stu-id="6b871-139">Controls Adapters were introduced in .NET 2.0 to render presentation code that was customized for different devices and environments.</span></span> <span data-ttu-id="6b871-140">Nyní tento adaptivního vykreslování můžete provést pomocí šablon stylů CSS a HTML.</span><span class="sxs-lookup"><span data-stu-id="6b871-140">Now, this adaptive rendering can be accomplished with CSS and HTML.</span></span> <span data-ttu-id="6b871-141">Měli přestat používat adaptérů ovládacích prvků a převeďte všechny existující adaptéry šablon stylů CSS a HTML.</span><span class="sxs-lookup"><span data-stu-id="6b871-141">You should stop using Control Adapters and convert any existing adapters to CSS and HTML.</span></span>

<span data-ttu-id="6b871-142">Další informace najdete v tématu [dotazy na média](http://www.w3.org/TR/css3-mediaqueries/) a [postupy: Přidání mobilní stránky na vaše webové formuláře ASP.NET / aplikace MVC](../../../whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application.md).</span><span class="sxs-lookup"><span data-stu-id="6b871-142">For more information, see [Media Queries](http://www.w3.org/TR/css3-mediaqueries/) and [How To: Add Mobile Pages to Your ASP.NET Web Forms / MVC Application](../../../whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application.md).</span></span>

<a id="styleprop"></a>

### <a name="style-properties-on-controls"></a><span data-ttu-id="6b871-143">Vlastnosti stylu v ovládacích prvcích</span><span class="sxs-lookup"><span data-stu-id="6b871-143">Style Properties on Controls</span></span>

<span data-ttu-id="6b871-144">Doporučení: Zastavte nastavit styl hodnoty do ovládacího prvku a místo toho nastavit formátování hodnoty v šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="6b871-144">Recommendation: Stop setting style values in the control markup, and instead set formatting values in CSS stylesheets.</span></span>

<span data-ttu-id="6b871-145">Ovládací prvky webového serveru obsahují desítky vlastnosti, které můžete použít k nastavení vlastnosti stylu v řádku.</span><span class="sxs-lookup"><span data-stu-id="6b871-145">Web server controls contain dozens of properties which can be used to set in-line style properties.</span></span> <span data-ttu-id="6b871-146">Například vlastnosti ForeColor nastaví barvu textu pro ovládací prvek.</span><span class="sxs-lookup"><span data-stu-id="6b871-146">For example, the ForeColor property sets the color of the text for a control.</span></span> <span data-ttu-id="6b871-147">Můžete provést tento stejný účinek efektivněji pomocí šablon stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="6b871-147">You can accomplish this same effect more efficiently through CSS stylesheets.</span></span> <span data-ttu-id="6b871-148">Šablony stylů umožňují centralizovat hodnoty stylu a vyhněte se nastavení tyto hodnoty v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b871-148">Stylesheets enable you to centralize style values and avoid setting these values throughout your application.</span></span>

<span data-ttu-id="6b871-149">Následující příklad ukazuje třídu šablony stylů CSS nastaví text, který se červené.</span><span class="sxs-lookup"><span data-stu-id="6b871-149">The following example shows a CSS class the sets text to red.</span></span>

[!code-css[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample1.css)]

<span data-ttu-id="6b871-150">Následující příklad ukazuje, jak dynamicky nastavit třídu CSS.</span><span class="sxs-lookup"><span data-stu-id="6b871-150">The next example shows how to dynamically apply the CSS class.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample2.cs)]

<a id="callback"></a>

### <a name="page-and-control-callbacks"></a><span data-ttu-id="6b871-151">Stránky a zpětná volání ovládacího prvku</span><span class="sxs-lookup"><span data-stu-id="6b871-151">Page and Control Callbacks</span></span>

<span data-ttu-id="6b871-152">Doporučení: Přestat používat stránky a ovládací prvek zpětná volání a místo toho použít některý z následujících akcí: AJAX, UpdatePanel, metody akce MVC, webové rozhraní API nebo SignalR.</span><span class="sxs-lookup"><span data-stu-id="6b871-152">Recommendation: Stop using page and control callbacks, and instead use any of the following: AJAX, UpdatePanel, MVC action methods, Web API, or SignalR.</span></span>

<span data-ttu-id="6b871-153">V předchozích verzích technologie ASP.NET umožňovala stránky a ovládací prvek metody zpětného volání aktualizovat část webové stránky bez aktualizace celé stránky.</span><span class="sxs-lookup"><span data-stu-id="6b871-153">In earlier versions of ASP.NET, Page and Control callback methods enabled you to update part of the web page without refreshing an entire page.</span></span> <span data-ttu-id="6b871-154">Teď můžete provádět částečné aktualizace stránky prostřednictvím [AJAX](../../../ajax/index.md), [UpdatePanel](https://msdn.microsoft.com/library/bb386454.aspx), [MVC](../../../mvc/index.md), [webového rozhraní API](../../../web-api/index.md) nebo [SignalR](../../../signalr/index.md).</span><span class="sxs-lookup"><span data-stu-id="6b871-154">You can now accomplish partial-page updates through [AJAX](../../../ajax/index.md), [UpdatePanel](https://msdn.microsoft.com/library/bb386454.aspx), [MVC](../../../mvc/index.md), [Web API](../../../web-api/index.md) or [SignalR](../../../signalr/index.md).</span></span> <span data-ttu-id="6b871-155">By se měla zastavit pomocí metod zpětného volání, protože může způsobit problémy s přátelské adresy URL a směrování.</span><span class="sxs-lookup"><span data-stu-id="6b871-155">You should stop using callback methods because they can cause issues with friendly URLs and routing.</span></span> <span data-ttu-id="6b871-156">Ve výchozím nastavení ovládací prvky nepovolujte metody zpětného volání, ale pokud povolíte tuto funkci v ovládacím prvku, měli byste zakázat.</span><span class="sxs-lookup"><span data-stu-id="6b871-156">By default, controls do not enable callback methods, but if you enabled this feature in a control, you should disable it.</span></span>

<a id="browsercap"></a>

### <a name="browser-capability-detection"></a><span data-ttu-id="6b871-157">Zjišťování schopností prohlížeče</span><span class="sxs-lookup"><span data-stu-id="6b871-157">Browser Capability Detection</span></span>

<span data-ttu-id="6b871-158">Doporučení: Přestat používat zjišťování schopností statické prohlížeče a místo toho použít funkce Dynamická detekce.</span><span class="sxs-lookup"><span data-stu-id="6b871-158">Recommendation: Stop using static browser capability detection, and instead use dynamic feature detection.</span></span>

<span data-ttu-id="6b871-159">V předchozích verzích technologie ASP.NET podporované funkce pro každým prohlížečem byly uloženy do souboru XML.</span><span class="sxs-lookup"><span data-stu-id="6b871-159">In earlier versions of ASP.NET, the supported features for each browser were stored in an XML file.</span></span> <span data-ttu-id="6b871-160">Rozpoznání podporu prostřednictvím statických vyhledávání není nejlepším řešením.</span><span class="sxs-lookup"><span data-stu-id="6b871-160">Detecting feature support through a static lookup is not the best approach.</span></span> <span data-ttu-id="6b871-161">Nyní můžete dynamicky zjistit pomocí funkce zjišťování rozhraní, jako je funkce podporované prohlížeče [Modernizr](http://modernizr.com/).</span><span class="sxs-lookup"><span data-stu-id="6b871-161">Now, you can dynamically detect a browser's supported features by using a feature detection framework, such as [Modernizr](http://modernizr.com/).</span></span> <span data-ttu-id="6b871-162">Funkce detekce, zda podporu tím pokouší použít metodu nebo vlastnost a potom kontroluje se, pokud prohlížeč vytvořený požadovaný výsledek.</span><span class="sxs-lookup"><span data-stu-id="6b871-162">Feature detection determines support by attempting to use a method or property and then checking to see if the browser produced the desired result.</span></span> <span data-ttu-id="6b871-163">Ve výchozím nastavení Modernizr je součástí šablony webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b871-163">By default, Modernizr is included in the Web application templates.</span></span>

<a id="security"></a>

## <a name="security"></a><span data-ttu-id="6b871-164">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="6b871-164">Security</span></span>

<a id="validation"></a>

### <a name="request-validation"></a><span data-ttu-id="6b871-165">Žádost o ověření</span><span class="sxs-lookup"><span data-stu-id="6b871-165">Request Validation</span></span>

<span data-ttu-id="6b871-166">Doporučení: Ověření vstupu uživatele a kódování výstupu od uživatelů.</span><span class="sxs-lookup"><span data-stu-id="6b871-166">Recommendation: Validate user input, and encode output from users.</span></span>

<span data-ttu-id="6b871-167">Žádost o ověření je funkce technologie ASP.NET, která kontroluje každý požadavek a požadavek se zastaví, pokud se nachází zjištěné hrozby.</span><span class="sxs-lookup"><span data-stu-id="6b871-167">Request validation is a feature of ASP.NET that inspects each request and stops the request if a perceived threat is found.</span></span> <span data-ttu-id="6b871-168">Není závislý na ověření žádosti pro zabezpečení aplikace před útoky skriptování napříč weby.</span><span class="sxs-lookup"><span data-stu-id="6b871-168">Do not depend on request validation for securing your application against cross-site scripting attacks.</span></span> <span data-ttu-id="6b871-169">Místo toho ověřte všechny vstupy od uživatele a kódování výstupu.</span><span class="sxs-lookup"><span data-stu-id="6b871-169">Instead, validate all input from users and encode the output.</span></span> <span data-ttu-id="6b871-170">V některých omezených případech regulární výrazy můžete použít k ověření vstupu, ale ve složitější případů, měli byste ověřit vstup pomocí třídy rozhraní .NET, které určí, zda hodnota odpovídá povolené hodnoty.</span><span class="sxs-lookup"><span data-stu-id="6b871-170">In some limited cases, you can use regular expressions to validate the input, but in more complicated cases you should validate user input by using .NET classes that determine if the value matches allowed values.</span></span>

<span data-ttu-id="6b871-171">Následující příklad ukazuje způsob použití statickou metodu ve třídě identifikátoru Uri k určení, zda je zadaný uživatelem identifikátor Uri platný.</span><span class="sxs-lookup"><span data-stu-id="6b871-171">The following example shows how to use a static method in the Uri class to determine whether the Uri provided by a user is valid.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample3.cs)]

<span data-ttu-id="6b871-172">Ale dostatečně ověření identifikátoru Uri, podívejte se na Ujistěte se, že Určuje `http` nebo `https`.</span><span class="sxs-lookup"><span data-stu-id="6b871-172">However, to sufficiently verify the Uri, you should also check to make sure it specifies `http` or `https`.</span></span> <span data-ttu-id="6b871-173">Následující příklad používá k ověření, že je platný identifikátor Uri metody instance.</span><span class="sxs-lookup"><span data-stu-id="6b871-173">The following example uses instance methods to verify that the Uri is valid.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample4.cs)]

<span data-ttu-id="6b871-174">Před vykreslování uživatelský vstup ve formátu HTML nebo včetně uživatelského vstupu v dotazu SQL, kódování hodnoty tak, aby Ujistěte se, že škodlivý kód není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="6b871-174">Before rendering user input as HTML or including user input in a SQL query, encode the values to ensure malicious code is not included.</span></span>

<span data-ttu-id="6b871-175">HTML můžete kódovat hodnotu v kódu s &lt;%: %&gt; syntaxe, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="6b871-175">You can HTML encode the value in markup with the &lt;%: %&gt; syntax, as shown below.</span></span>

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample5.aspx?highlight=1)]

<span data-ttu-id="6b871-176">Nebo v syntaxi Razor HTML můžete kódovat s @, jak je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="6b871-176">Or, in Razor syntax, you can HTML encode with @, as shown below.</span></span>

[!code-cshtml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample6.cshtml?highlight=1)]

<span data-ttu-id="6b871-177">Další příklad ukazuje jak do formátu HTML kódovat hodnotu v kódu.</span><span class="sxs-lookup"><span data-stu-id="6b871-177">The next example shows how to HTML encode a value in code-behind.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample7.cs)]

<span data-ttu-id="6b871-178">Ke kódování bezpečně hodnotu pro příkazy SQL, použijte parametry příkazu [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx).</span><span class="sxs-lookup"><span data-stu-id="6b871-178">To safely encode a value for SQL commands, use command parameters such as the [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx).</span></span> <a id="cookieless"></a>

### <a name="cookieless-forms-authentication-and-session"></a><span data-ttu-id="6b871-179">Ověřování pomocí formulářů bez souborů cookie a relace</span><span class="sxs-lookup"><span data-stu-id="6b871-179">Cookieless Forms Authentication and Session</span></span>

<span data-ttu-id="6b871-180">Doporučení: Vyžaduje soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="6b871-180">Recommendation: Require cookies.</span></span>

<span data-ttu-id="6b871-181">Předání ověřovacích informací v řetězci dotazu není zabezpečený.</span><span class="sxs-lookup"><span data-stu-id="6b871-181">Passing authentication information in the query string is not secure.</span></span> <span data-ttu-id="6b871-182">Proto vyžaduje soubory cookie, pokud vaše aplikace obsahuje ověřování.</span><span class="sxs-lookup"><span data-stu-id="6b871-182">Therefore, require cookies when your application includes authentication.</span></span> <span data-ttu-id="6b871-183">Pokud vaše soubory cookie jsou uloženy citlivé informace, zvažte vyžadování SSL pro soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="6b871-183">If your cookie stores sensitive information, consider requiring SSL for the cookie.</span></span>

<span data-ttu-id="6b871-184">Následující příklad ukazuje, jak zadat v souboru Web.config, že ověřování pomocí formulářů vyžaduje soubor cookie, který se přenáší přes protokol SSL.</span><span class="sxs-lookup"><span data-stu-id="6b871-184">The following example shows how to specify in the Web.config file that Forms Authentication requires a cookie that is transmitted over SSL.</span></span>

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample8.xml)]

<a id="viewstatemac"></a>

### <a name="enableviewstatemac"></a><span data-ttu-id="6b871-185">EnableViewStateMac</span><span class="sxs-lookup"><span data-stu-id="6b871-185">EnableViewStateMac</span></span>

<span data-ttu-id="6b871-186">Doporučení: Nikdy nastaven na hodnotu false.</span><span class="sxs-lookup"><span data-stu-id="6b871-186">Recommendation: Never set to false.</span></span>

<span data-ttu-id="6b871-187">Ve výchozím nastavení, EnbableViewStateMac nastavena na hodnotu true.</span><span class="sxs-lookup"><span data-stu-id="6b871-187">By default, EnbableViewStateMac is set to true.</span></span> <span data-ttu-id="6b871-188">I v případě, že vaše aplikace nepoužívá stav zobrazení, nenastavujte EnableViewStateMac na hodnotu false.</span><span class="sxs-lookup"><span data-stu-id="6b871-188">Even if your application is not using view state, do not set EnableViewStateMac to false.</span></span> <span data-ttu-id="6b871-189">Tuto hodnotu nastavíte na hodnotu false bude ohrožovat zabezpečení aplikace pro skriptování napříč weby.</span><span class="sxs-lookup"><span data-stu-id="6b871-189">Setting this value to false will make your application vulnerable to cross-site scripting.</span></span>

<span data-ttu-id="6b871-190">Spouštění pomocí technologie ASP.NET 4.5.2, runtime modul vynucuje **EnableViewStateMac = true**.</span><span class="sxs-lookup"><span data-stu-id="6b871-190">Starting with ASP.NET 4.5.2, the runtime enforces **EnableViewStateMac=true**.</span></span> <span data-ttu-id="6b871-191">I v případě, že ji nastavíte na hodnotu false, modul runtime bude ignorovat tuto hodnotu a pokračuje s hodnotou nastavenou na hodnotu true.</span><span class="sxs-lookup"><span data-stu-id="6b871-191">Even if you set it to false, the runtime ignores this value and proceeds with the value set to true.</span></span> <span data-ttu-id="6b871-192">Další informace najdete v tématu [ASP.NET 4.5.2 a EnableViewStateMac](https://blogs.msdn.com/b/webdev/archive/2014/05/07/asp-net-4-5-2-and-enableviewstatemac.aspx).</span><span class="sxs-lookup"><span data-stu-id="6b871-192">For more information, see [ASP.NET 4.5.2 and EnableViewStateMac](https://blogs.msdn.com/b/webdev/archive/2014/05/07/asp-net-4-5-2-and-enableviewstatemac.aspx).</span></span>

<span data-ttu-id="6b871-193">Následující příklad ukazuje, jak nastavit EnableViewStateMac na hodnotu true.</span><span class="sxs-lookup"><span data-stu-id="6b871-193">The following example shows how to set EnableViewStateMac to true.</span></span> <span data-ttu-id="6b871-194">Nemusíte skutečně nastavte tuto hodnotu na true, protože je ve výchozím nastavení hodnotu true.</span><span class="sxs-lookup"><span data-stu-id="6b871-194">You do not need to actually set this value to true because it is true by default.</span></span> <span data-ttu-id="6b871-195">Nicméně pokud jste ji nastavíte na hodnotu false na libovolné stránce v aplikaci, musíte okamžitě opravit tuto hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6b871-195">However, if you have set it to false on any page in your application, you must immediately correct this value.</span></span>

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample9.aspx)]

<a id="medium"></a>

### <a name="medium-trust"></a><span data-ttu-id="6b871-196">Úrovni Medium Trust</span><span class="sxs-lookup"><span data-stu-id="6b871-196">Medium Trust</span></span>

<span data-ttu-id="6b871-197">Doporučení: Nezávisí na střední důvěryhodnosti (nebo jiné úroveň důvěryhodnosti) jako hranice zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="6b871-197">Recommendation: Do not depend on Medium Trust (or any other trust level) as a security boundary.</span></span>

<span data-ttu-id="6b871-198">Částečným vztahem důvěryhodnosti adekvátní ochranu aplikace by se neměl používat.</span><span class="sxs-lookup"><span data-stu-id="6b871-198">Partial trust does not adequately protect your application and should not be used.</span></span> <span data-ttu-id="6b871-199">Místo toho používat plnou důvěryhodnost a izolovat nedůvěryhodné aplikace v samostatných fondech aplikací.</span><span class="sxs-lookup"><span data-stu-id="6b871-199">Instead, use Full Trust, and isolate untrusted applications in separate application pools.</span></span> <span data-ttu-id="6b871-200">Navíc spouštět každý fond aplikací v rámci jedinečnou identitu.</span><span class="sxs-lookup"><span data-stu-id="6b871-200">Also, run each application pool under a unique identity.</span></span> <span data-ttu-id="6b871-201">Další informace najdete v tématu [ASP.NET částečném vztahu důvěryhodnosti nezaručuje izolace aplikací](https://support.microsoft.com/kb/2698981).</span><span class="sxs-lookup"><span data-stu-id="6b871-201">For more information, see [ASP.NET Partial Trust does not guarantee application isolation](https://support.microsoft.com/kb/2698981).</span></span>

<a id="appsettings"></a>

### <a name="ltappsettingsgt"></a><span data-ttu-id="6b871-202">&lt;appSettings&gt;</span><span class="sxs-lookup"><span data-stu-id="6b871-202">&lt;appSettings&gt;</span></span>

<span data-ttu-id="6b871-203">Doporučení: Nezakazujte nastavení zabezpečení v &lt;appSettings&gt; elementu.</span><span class="sxs-lookup"><span data-stu-id="6b871-203">Recommendation: Do not disable security settings in &lt;appSettings&gt; element.</span></span>

<span data-ttu-id="6b871-204">Prvek appSettings obsahuje mnoho hodnot, které jsou požadovány pro aktualizace zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="6b871-204">The appSettings element contains many values which are required for security updates.</span></span> <span data-ttu-id="6b871-205">Nesmí změnit nebo zakázat tyto hodnoty.</span><span class="sxs-lookup"><span data-stu-id="6b871-205">You should not change or disable these values.</span></span> <span data-ttu-id="6b871-206">Pokud tyto hodnoty je třeba zakázat při nasazování aktualizace, okamžitě znovu povolte po dokončení nasazení.</span><span class="sxs-lookup"><span data-stu-id="6b871-206">If you must disable these values when deploying an update, immediately re-enable after completing deployment.</span></span>

<span data-ttu-id="6b871-207">Podrobnosti najdete v tématu [ASP.NET appSettings Element](https://msdn.microsoft.com/library/hh975440.aspx).</span><span class="sxs-lookup"><span data-stu-id="6b871-207">For details, see [ASP.NET appSettings Element](https://msdn.microsoft.com/library/hh975440.aspx).</span></span>

<a id="urlpathencode"></a>

### <a name="urlpathencode"></a><span data-ttu-id="6b871-208">UrlPathEncode</span><span class="sxs-lookup"><span data-stu-id="6b871-208">UrlPathEncode</span></span>

<span data-ttu-id="6b871-209">Doporučení: Použijte [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx) místo.</span><span class="sxs-lookup"><span data-stu-id="6b871-209">Recommendation: Use [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx) instead.</span></span>

<span data-ttu-id="6b871-210">Metoda UrlPathEncode byl přidán do rozhraní .NET Framework, chcete-li vyřešit potíže s kompatibilitou velmi určitého webového prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="6b871-210">The UrlPathEncode method was added to the .NET Framework to resolve a very specific browser compatibility problem.</span></span> <span data-ttu-id="6b871-211">Odpovídajícím způsobem kódování adresy URL a není ochrana aplikací před skriptování napříč weby.</span><span class="sxs-lookup"><span data-stu-id="6b871-211">It does not adequately encode a URL, and does not protect your application from cross-site scripting.</span></span> <span data-ttu-id="6b871-212">Nikdy používejte ji v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6b871-212">You should never use it in your application.</span></span> <span data-ttu-id="6b871-213">Místo toho použijte [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx).</span><span class="sxs-lookup"><span data-stu-id="6b871-213">Instead, use [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx).</span></span>

<span data-ttu-id="6b871-214">Následující příklad ukazuje, jak předat adresu URL kódovaný jako parametru řetězce dotazu pro ovládací prvek hypertextového odkazu.</span><span class="sxs-lookup"><span data-stu-id="6b871-214">The following example shows how to pass an encoded URL as a query string parameter for a hyperlink control.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample10.cs)]

<a id="performance"></a>

## <a name="reliability-and-performance"></a><span data-ttu-id="6b871-215">Spolehlivost a výkon</span><span class="sxs-lookup"><span data-stu-id="6b871-215">Reliability and Performance</span></span>

<a id="presend"></a>

### <a name="presendrequestheaders-and-presendrequestcontent"></a><span data-ttu-id="6b871-216">PreSendRequestHeaders a PreSendRequestContent</span><span class="sxs-lookup"><span data-stu-id="6b871-216">PreSendRequestHeaders and PreSendRequestContent</span></span>

<span data-ttu-id="6b871-217">Doporučení: Nepoužívejte tyto události s spravované moduly.</span><span class="sxs-lookup"><span data-stu-id="6b871-217">Recommendation: Do not use these events with managed modules.</span></span> <span data-ttu-id="6b871-218">Místo toho napište nativní modul služby IIS k provedení požadované úlohy.</span><span class="sxs-lookup"><span data-stu-id="6b871-218">Instead, write a native IIS module to perform the required task.</span></span> <span data-ttu-id="6b871-219">Zobrazit [vytváření modulů HTTP v nativním kódu](https://msdn.microsoft.com/library/ms693629.aspx).</span><span class="sxs-lookup"><span data-stu-id="6b871-219">See [Creating Native-Code HTTP Modules](https://msdn.microsoft.com/library/ms693629.aspx).</span></span>

<span data-ttu-id="6b871-220">Můžete použít [PreSendRequestHeaders](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestheaders.aspx) a [PreSendRequestContent](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestcontent.aspx) události s nativní moduly služby IIS.</span><span class="sxs-lookup"><span data-stu-id="6b871-220">You can use the [PreSendRequestHeaders](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestheaders.aspx) and [PreSendRequestContent](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestcontent.aspx) events with native IIS modules.</span></span>
> [!WARNING]
> <span data-ttu-id="6b871-221">Nepoužívejte `PreSendRequestHeaders` a `PreSendRequestContent` s spravované moduly, které implementují `IHttpModule`.</span><span class="sxs-lookup"><span data-stu-id="6b871-221">Do not use `PreSendRequestHeaders` and `PreSendRequestContent` with managed modules that implement `IHttpModule`.</span></span> <span data-ttu-id="6b871-222">Nastavení těchto vlastností, může způsobit problémy s asynchronní požadavků.</span><span class="sxs-lookup"><span data-stu-id="6b871-222">Setting these properties can cause issues with asynchronous requests.</span></span> <span data-ttu-id="6b871-223">Kombinace požadovaný směrování žádostí na aplikace a protokoly websocket může vést k výjimky porušení přístupu, které může způsobit pád w3wp.</span><span class="sxs-lookup"><span data-stu-id="6b871-223">The combination of Application Requested Routing (ARR) and websockets might lead to access violation exceptions that can cause w3wp to crash.</span></span> <span data-ttu-id="6b871-224">Například iiscore! W3_CONTEXT_BASE::GetIsLastNotification + 68 v iiscore.dll způsobila výjimku narušení přístupu (0xC0000005).</span><span class="sxs-lookup"><span data-stu-id="6b871-224">For example, iiscore!W3_CONTEXT_BASE::GetIsLastNotification+68 in iiscore.dll has caused an access violation exception (0xC0000005).</span></span>

<a id="asyncevents"></a>

### <a name="asynchronous-page-events-with-web-forms"></a><span data-ttu-id="6b871-225">Události asynchronní stránky s webovými formuláři</span><span class="sxs-lookup"><span data-stu-id="6b871-225">Asynchronous Page Events with Web Forms</span></span>

<span data-ttu-id="6b871-226">Doporučení: Ve webových formulářů, vyhněte se zápis async void metody pro události životního cyklu stránky použijte raději [Page.RegisterAsyncTask](https://msdn.microsoft.com/library/system.web.ui.page.registerasynctask.aspx) pro asynchronní kód.</span><span class="sxs-lookup"><span data-stu-id="6b871-226">Recommendation: In Web Forms, avoid writing async void methods for Page lifecycle events, and instead use [Page.RegisterAsyncTask](https://msdn.microsoft.com/library/system.web.ui.page.registerasynctask.aspx) for asynchronous code.</span></span>

<span data-ttu-id="6b871-227">Pokud označíte událostí stránky s **asynchronní** a **void**, nelze určit dokončení asynchronního kódu.</span><span class="sxs-lookup"><span data-stu-id="6b871-227">When you mark a page event with **async** and **void**, you cannot determine when the asynchronous code has finished.</span></span> <span data-ttu-id="6b871-228">Místo toho použijte Page.RegisterAsyncTask ke spuštění asynchronního kódu způsobem, který umožňuje sledovat jeho dokončení.</span><span class="sxs-lookup"><span data-stu-id="6b871-228">Instead, use Page.RegisterAsyncTask to run the asynchronous code in a way that enables you to track its completion.</span></span>

<span data-ttu-id="6b871-229">Následující příklad ukazuje a tlačítko klikněte na obslužnou rutinu, která obsahuje asynchronní kód.</span><span class="sxs-lookup"><span data-stu-id="6b871-229">The following example shows a button click handler that contains asynchronous code.</span></span> <span data-ttu-id="6b871-230">Tento příklad zahrnuje čtení řetězcovou hodnotu asynchronně, který je k dispozici pouze jako zjednodušený příklad asynchronní úlohy a ne jako doporučený postup.</span><span class="sxs-lookup"><span data-stu-id="6b871-230">This example includes reading a string value asynchronously, which is provided only as a simplified example of an asynchronous task and not as a recommended practice.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample11.cs)]

<span data-ttu-id="6b871-231">Pokud používáte asynchronních úloh, nastavte cílovou architekturu Http runtime 4.5 v souboru Web.config.</span><span class="sxs-lookup"><span data-stu-id="6b871-231">If you are using asynchronous Tasks, set the Http runtime target framework to 4.5 in the Web.config file.</span></span> <span data-ttu-id="6b871-232">Nastavení cílové architektury na 4.5 změní na nový kontext synchronizace, který byl přidán v rozhraní .NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="6b871-232">Setting the target framework to 4.5 turns on the new synchronization context that was added in .NET 4.5.</span></span> <span data-ttu-id="6b871-233">Tato hodnota je nastaven ve výchozím nastavení v nových projektech v sadě Visual Studio 2012, ale není možné nastavit při práci s existující projekt.</span><span class="sxs-lookup"><span data-stu-id="6b871-233">This value is set by default in new projects in Visual Studio 2012, but is not be set if you are working with an existing project.</span></span>

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample12.xml)]

<a id="fire"></a>

### <a name="fire-and-forget-work"></a><span data-ttu-id="6b871-234">Oheň a zapomenout práce</span><span class="sxs-lookup"><span data-stu-id="6b871-234">Fire-and-Forget Work</span></span>

<span data-ttu-id="6b871-235">Doporučení: Při zpracovávání konkrétní žádosti v rámci technologie ASP.NET, zabránilo spouštění fire a zapomenout práce (odpovídající volání metody ThreadPool.QueueUserWorkItem nebo vytváření časovač, který opakovaně volání delegáta).</span><span class="sxs-lookup"><span data-stu-id="6b871-235">Recommendation: When handling a request within ASP.NET, avoid launching fire-and-forget work (such calling the ThreadPool.QueueUserWorkItem method or creating a timer that repeatedly calls a delegate).</span></span>

<span data-ttu-id="6b871-236">Pokud má vaše aplikace fire a zapomenout práce, na kterém běží v rámci technologie ASP.NET, aplikace můžete získat synchronizovaný. V každém okamžiku domény aplikace lze zničit to znamená, že vaše probíhající proces už neodpovídá aktuální stav aplikace.</span><span class="sxs-lookup"><span data-stu-id="6b871-236">If your application has fire-and-forget work that runs within ASP.NET, your application can get out of sync. At any time, the app domain can be destroyed which means your ongoing process may no longer match the current state of the application.</span></span>

<span data-ttu-id="6b871-237">Měli byste přejít tohoto typu práce mimo prostředí ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="6b871-237">You should move this type of work outside of ASP.NET.</span></span> <span data-ttu-id="6b871-238">Můžete provádět probíhající práce pomocí webových úloh, služba Windows nebo role pracovního procesu v Azure a spustit tento kód z jiného procesu.</span><span class="sxs-lookup"><span data-stu-id="6b871-238">You can use a Web Jobs, Windows Service or a Worker role in Azure to perform ongoing work, and run that code from another process.</span></span>

<span data-ttu-id="6b871-239">Pokud je třeba provést tuto práci v rámci technologie ASP.NET, můžete přidat balíček Nuget s názvem [WebBackgrounder](http://www.nuget.org/packages/webbackgrounder) spuštění kódu.</span><span class="sxs-lookup"><span data-stu-id="6b871-239">If you must perform this work within ASP.NET, you can add the Nuget package called [WebBackgrounder](http://www.nuget.org/packages/webbackgrounder) to run the code.</span></span>

<a id="requestentity"></a>

### <a name="request-entity-body"></a><span data-ttu-id="6b871-240">Obsah Entity žádosti</span><span class="sxs-lookup"><span data-stu-id="6b871-240">Request Entity Body</span></span>

<span data-ttu-id="6b871-241">Doporučení: Vyhněte se čtení Request.Form nebo Request.InputStream před obslužnou rutinu události.</span><span class="sxs-lookup"><span data-stu-id="6b871-241">Recommendation: Avoid reading Request.Form or Request.InputStream before the handler's execute event.</span></span>

<span data-ttu-id="6b871-242">Nejdřívější, které byste si měli přečíst z Request.Form nebo Request.InputStream je během obslužnou rutinu spusťte událost.</span><span class="sxs-lookup"><span data-stu-id="6b871-242">The earliest you should read from Request.Form or Request.InputStream is during the handler's execute event.</span></span> <span data-ttu-id="6b871-243">V aplikaci MVC Kontroleru je obslužná rutina a spouštění událostí je při spuštění metody akce.</span><span class="sxs-lookup"><span data-stu-id="6b871-243">In MVC, the Controller is the handler and the execute event is when the action method runs.</span></span> <span data-ttu-id="6b871-244">Ve webových formulářů na stránce je obslužná rutina a je execute událost, když se aktivuje událost Page.Init.</span><span class="sxs-lookup"><span data-stu-id="6b871-244">In Web Forms, the Page is the handler and the execute event is when the Page.Init event fires.</span></span> <span data-ttu-id="6b871-245">Pokud načtete starší než událost execute obsah entity žádosti, ovlivňovat zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="6b871-245">If you read the request entity body earlier than the execute event, you interfere with the processing of the request.</span></span>

<span data-ttu-id="6b871-246">Pokud budete potřebovat číst obsah entity žádosti před událostí spouštění, použijte buď [Request.GetBufferlessInputStream](https://msdn.microsoft.com/library/ff406798.aspx) nebo [Request.GetBufferedInputStream](https://msdn.microsoft.com/library/system.web.httprequest.getbufferedinputstream.aspx).</span><span class="sxs-lookup"><span data-stu-id="6b871-246">If you need to read the request entity body before the execute event, use either [Request.GetBufferlessInputStream](https://msdn.microsoft.com/library/ff406798.aspx) or [Request.GetBufferedInputStream](https://msdn.microsoft.com/library/system.web.httprequest.getbufferedinputstream.aspx).</span></span> <span data-ttu-id="6b871-247">Při použití GetBufferlessInputStream získat nezpracovaný datový proud z požadavku a převzít odpovědnost za zpracování celé požadavku.</span><span class="sxs-lookup"><span data-stu-id="6b871-247">When you use GetBufferlessInputStream, you get the raw stream from the request, and assume responsibility for processing the entire request.</span></span> <span data-ttu-id="6b871-248">Po volání GetBufferlessInputStream, Request.Form a Request.InputStream nejsou k dispozici, protože nebyly byl vyplněn technologie ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="6b871-248">After calling GetBufferlessInputStream, Request.Form and Request.InputStream are not available because they have not been populated by ASP.NET.</span></span> <span data-ttu-id="6b871-249">Když použijete GetBufferedInputStream, získáte kopii datového proudu z požadavku.</span><span class="sxs-lookup"><span data-stu-id="6b871-249">When you use GetBufferedInputStream, you get a copy of the stream from the request.</span></span> <span data-ttu-id="6b871-250">Request.Form a Request.InputStream jsou stále k dispozici později v požadavku, protože technologie ASP.NET naplní druhou kopii.</span><span class="sxs-lookup"><span data-stu-id="6b871-250">Request.Form and Request.InputStream are still available later in the request because ASP.NET populates the other copy.</span></span>

<a id="redirect"></a>

### <a name="responseredirect-and-responseend"></a><span data-ttu-id="6b871-251">Response.Redirect a metody Response.End</span><span class="sxs-lookup"><span data-stu-id="6b871-251">Response.Redirect and Response.End</span></span>

<span data-ttu-id="6b871-252">Doporučení: Znát rozdíly ve zpracování vlákna po volání [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx).</span><span class="sxs-lookup"><span data-stu-id="6b871-252">Recommendation: Be aware of differences in how thread is handled after calling [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx).</span></span>

<span data-ttu-id="6b871-253">[Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx) metoda volá metodu metody Response.End.</span><span class="sxs-lookup"><span data-stu-id="6b871-253">The [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx) method calls the Response.End method.</span></span> <span data-ttu-id="6b871-254">V synchronní zpracování volání Request.Redirect způsobí, že aktuální vlákno, okamžitě zrušit.</span><span class="sxs-lookup"><span data-stu-id="6b871-254">In a synchronous process, calling Request.Redirect causes the current thread to immediately abort.</span></span> <span data-ttu-id="6b871-255">Ale u asynchronního procesu, metoda Response.Redirect není přerušení aktuálního vlákna, tak pokračuje v provádění kódu pro daný požadavek.</span><span class="sxs-lookup"><span data-stu-id="6b871-255">However, in an asynchronous process, calling Response.Redirect does not abort the current thread, so code execution continues for the request.</span></span> <span data-ttu-id="6b871-256">U asynchronního procesu musíte se vrátit úlohu z metody na svém vyvolání zastaví provádění kódu.</span><span class="sxs-lookup"><span data-stu-id="6b871-256">In an asynchronous process, you must return the Task from the method to stop the code execution.</span></span>

<span data-ttu-id="6b871-257">V projektu aplikace MVC neměli by jste volat Response.Redirect.</span><span class="sxs-lookup"><span data-stu-id="6b871-257">In an MVC project, you should not call Response.Redirect.</span></span> <span data-ttu-id="6b871-258">Místo toho vrátí RedirectResult.</span><span class="sxs-lookup"><span data-stu-id="6b871-258">Instead, return a RedirectResult.</span></span>

<a id="viewstatemode"></a>

### <a name="enableviewstate-and-viewstatemode"></a><span data-ttu-id="6b871-259">EnableViewState a ViewStateMode</span><span class="sxs-lookup"><span data-stu-id="6b871-259">EnableViewState and ViewStateMode</span></span>

<span data-ttu-id="6b871-260">Doporučení: ViewStateMode použijte místo EnableViewState zajistit detailní kontrolu nad tím, které pomocí ovládacích prvků stavu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6b871-260">Recommendation: Use ViewStateMode, instead of EnableViewState, to provide granular control over which controls use view state.</span></span>

<span data-ttu-id="6b871-261">EnableViewState nastavená na hodnotu false v direktivě stránky stav zobrazení je zakázaná pro všechny ovládací prvky v rámci stránky a není možné.</span><span class="sxs-lookup"><span data-stu-id="6b871-261">When you set EnableViewState to false in the Page directive, view state is disabled for all controls within the page and cannot be enabled.</span></span> <span data-ttu-id="6b871-262">Pokud chcete povolit stav zobrazení pro pouze některé ovládací prvky na stránce, nastavte pro stránku ViewStateMode zakázáno.</span><span class="sxs-lookup"><span data-stu-id="6b871-262">If you want to enable view state for only certain controls in your page, set ViewStateMode to Disabled for the Page.</span></span>

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample13.aspx)]

<span data-ttu-id="6b871-263">Potom nastavte ViewStateMode povoleno na pouze ovládací prvky, které skutečně potřebují stavu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6b871-263">Then, set ViewStateMode to Enabled on only the controls that actually need view state.</span></span>

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample14.aspx)]

<span data-ttu-id="6b871-264">Tím, že stav zobrazení pro pouze ovládací prvky, které potřebujete, můžete zmenšit velikost zobrazení stavu pro vaše webové stránky.</span><span class="sxs-lookup"><span data-stu-id="6b871-264">By enabling view state for only the controls that need it, you can shrink the size of the view state for your web pages.</span></span>

<a id="sqlprovider"></a>

### <a name="sqlmembershipprovider"></a><span data-ttu-id="6b871-265">SqlMembershipProvider</span><span class="sxs-lookup"><span data-stu-id="6b871-265">SqlMembershipProvider</span></span>

<span data-ttu-id="6b871-266">Doporučení: Použijte balíček Universal Providers.</span><span class="sxs-lookup"><span data-stu-id="6b871-266">Recommendation: Use Universal Providers.</span></span>

<span data-ttu-id="6b871-267">V aktuální šablony projektu se nahradil SqlMembershipProvider [balíčku ASP.NET Universal Providers](http://www.nuget.org/packages/Microsoft.AspNet.Providers), která je k dispozici jako balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="6b871-267">In the current project templates, SqlMembershipProvider has been replaced by [ASP.NET Universal Providers](http://www.nuget.org/packages/Microsoft.AspNet.Providers), which is available as a NuGet package.</span></span> <span data-ttu-id="6b871-268">Pokud používáte SqlMembershipProvider v projektu, který byl vytvořen v dřívější verzi šablon, byste měli přejít na Universal Providers.</span><span class="sxs-lookup"><span data-stu-id="6b871-268">If you are using SqlMembershipProvider in a project that was built with an earlier version of the templates, you should switch to Universal Providers.</span></span> <span data-ttu-id="6b871-269">Balíček Universal Providers pracovat všechny databáze, které jsou podporovány rozhraním Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="6b871-269">The Universal Providers work with all databases that are supported by Entity Framework.</span></span>

<span data-ttu-id="6b871-270">Další informace najdete v tématu [Úvod do ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx).</span><span class="sxs-lookup"><span data-stu-id="6b871-270">For more information, see [Introducing ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx).</span></span>

<a id="long"></a>

### <a name="long-running-requests-110-seconds"></a><span data-ttu-id="6b871-271">Dlouho běžící požadavky (> 110 sekund)</span><span class="sxs-lookup"><span data-stu-id="6b871-271">Long-running Requests (>110 seconds)</span></span>

<span data-ttu-id="6b871-272">Doporučení: Použijte [objekty Websocket](https://msdn.microsoft.com/library/system.net.websockets.websocket.aspx) nebo [SignalR](../../../signalr/index.md) pro připojené klienty a použití asynchronní vstupně-výstupních operací.</span><span class="sxs-lookup"><span data-stu-id="6b871-272">Recommendation: Use [WebSockets](https://msdn.microsoft.com/library/system.net.websockets.websocket.aspx) or [SignalR](../../../signalr/index.md) for connected clients, and use asynchronous I/O operations.</span></span>

<span data-ttu-id="6b871-273">Dlouhodobé požadavky může způsobit nepředvídatelné výsledky a slabým výkonem ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6b871-273">Long-running requests can cause unpredictable results and poor performance in your web application.</span></span> <span data-ttu-id="6b871-274">Výchozí nastavení časového limitu pro žádost je 110 sekund.</span><span class="sxs-lookup"><span data-stu-id="6b871-274">The default timeout setting for a request is 110 seconds.</span></span> <span data-ttu-id="6b871-275">Pokud používáte stav relace se dlouho běžící žádostí, vydá ASP.NET po sekundách 110 zámek na objekt relace.</span><span class="sxs-lookup"><span data-stu-id="6b871-275">If you are using session state with a long-running request, ASP.NET will release the lock on the Session object after 110 seconds.</span></span> <span data-ttu-id="6b871-276">Však může být vaše aplikace provádí operaci u objektu relace, pokud zámek je uvolněn a operace nemusí dokončit úspěšně.</span><span class="sxs-lookup"><span data-stu-id="6b871-276">However, your application might be in the middle of an operation on the Session object when the lock is released, and the operation might not complete successfully.</span></span> <span data-ttu-id="6b871-277">Pokud druhou žádost od uživatele se zablokuje při spuštění prvního požadavku, druhou žádost může získat přístup k objektu Session v nekonzistentním stavu.</span><span class="sxs-lookup"><span data-stu-id="6b871-277">If a second request from the user is blocked while the first request is running, the second request might access the Session object in an inconsistent state.</span></span>

<span data-ttu-id="6b871-278">Pokud vaše aplikace obsahuje blokování (nebo asynchronní) vstupně-výstupních operací, bude aplikace reagovat.</span><span class="sxs-lookup"><span data-stu-id="6b871-278">If your application includes blocking (or synchronous) I/O operations, the application will be unresponsive.</span></span>

<span data-ttu-id="6b871-279">Ke zlepšení výkonu, použijte asynchronní vstupně-výstupních operací v rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="6b871-279">To improve performance, use the asynchronous I/O operations in the .NET Framework.</span></span> <span data-ttu-id="6b871-280">Také můžete použijte objekty Websocket nebo SignalR pro připojení klientů k serveru.</span><span class="sxs-lookup"><span data-stu-id="6b871-280">Also, use WebSockets or SignalR for connecting clients to the server.</span></span> <span data-ttu-id="6b871-281">Tyto funkce jsou navržené pro efektivní zpracování dlouhodobé požadavky.</span><span class="sxs-lookup"><span data-stu-id="6b871-281">These features are designed to efficiently handle long-running requests.</span></span>
