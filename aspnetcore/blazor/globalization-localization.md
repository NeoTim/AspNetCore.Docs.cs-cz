---
title: ASP.NET Core Blazor globalizace a lokalizace
author: guardrex
description: Naučte se, Razor jak zpřístupnit součásti uživatelům v různých jazykových verzích a jazycích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: b39695f8b506744b4af27a1d7e09bfac9594d7ca
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772488"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a><span data-ttu-id="6cb88-103">ASP.NET Core Blazor globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="6cb88-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="6cb88-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="6cb88-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="6cb88-105">komponenty lze zpřístupnit uživatelům v různých jazykových verzích a jazycích.</span><span class="sxs-lookup"><span data-stu-id="6cb88-105"> components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="6cb88-106">K dispozici jsou následující scénáře globalizace a lokalizace rozhraní .NET:</span><span class="sxs-lookup"><span data-stu-id="6cb88-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="6cb88-107">. Systém prostředků netto</span><span class="sxs-lookup"><span data-stu-id="6cb88-107">.NET's resources system</span></span>
* <span data-ttu-id="6cb88-108">Formátování čísla a data specifické pro jazykovou verzi</span><span class="sxs-lookup"><span data-stu-id="6cb88-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="6cb88-109">V současné době se podporuje omezená sada scénářů lokalizace ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6cb88-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="6cb88-110">`IStringLocalizer<>`*je podporováno* v Blazor aplikacích.</span><span class="sxs-lookup"><span data-stu-id="6cb88-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="6cb88-111">`IHtmlLocalizer<>`lokalizace datových poznámek, `IViewLocalizer<>`a jsou ASP.NET Core scénáře MVC a **nejsou** v Blazor aplikacích podporovány.</span><span class="sxs-lookup"><span data-stu-id="6cb88-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="6cb88-112">Další informace naleznete v tématu <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="6cb88-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="6cb88-113">Globalizace</span><span class="sxs-lookup"><span data-stu-id="6cb88-113">Globalization</span></span>

Blazor<span data-ttu-id="6cb88-114">`@bind` funkce provádí formáty a analyzuje hodnoty pro zobrazení na základě aktuální jazykové verze uživatele.</span><span class="sxs-lookup"><span data-stu-id="6cb88-114">'s `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="6cb88-115">K aktuální jazykové verzi je možné přistupovat z <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="6cb88-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="6cb88-116">[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) se používá pro následující typy polí (`<input type="{TYPE}" />`):</span><span class="sxs-lookup"><span data-stu-id="6cb88-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="6cb88-117">Předchozí typy polí:</span><span class="sxs-lookup"><span data-stu-id="6cb88-117">The preceding field types:</span></span>

* <span data-ttu-id="6cb88-118">Se zobrazují pomocí příslušných pravidel formátování založených na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="6cb88-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="6cb88-119">Text s volným formulářem nemůže obsahovat.</span><span class="sxs-lookup"><span data-stu-id="6cb88-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="6cb88-120">Poskytněte charakteristiky interakce s uživatelem na základě implementace prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="6cb88-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="6cb88-121">Následující typy polí mají specifické požadavky na formátování a aktuálně nejsou podporovány, Blazor protože nejsou podporovány ve všech hlavních prohlížečích:</span><span class="sxs-lookup"><span data-stu-id="6cb88-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="6cb88-122">`@bind`podporuje `@bind:culture` parametr <xref:System.Globalization.CultureInfo?displayProperty=fullName> pro zajištění analýzy a formátování hodnoty.</span><span class="sxs-lookup"><span data-stu-id="6cb88-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="6cb88-123">Specifikace jazykové verze není doporučena při použití `date` typů `number` polí a.</span><span class="sxs-lookup"><span data-stu-id="6cb88-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="6cb88-124">`date`a `number` mají integrovanou Blazor podporu, která poskytuje požadovanou jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="6cb88-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="6cb88-125">Lokalizace</span><span class="sxs-lookup"><span data-stu-id="6cb88-125">Localization</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="6cb88-126">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="6cb88-126"> WebAssembly</span></span>

Blazor<span data-ttu-id="6cb88-127">Aplikace WebAssembly nastavily jazykovou verzi pomocí [Předvolby jazyka](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)uživatele.</span><span class="sxs-lookup"><span data-stu-id="6cb88-127"> WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="6cb88-128">Chcete-li explicitně konfigurovat jazykovou `CultureInfo.DefaultThreadCurrentCulture` verzi `CultureInfo.DefaultThreadCurrentUICulture` , `Program.Main`nastavte a v.</span><span class="sxs-lookup"><span data-stu-id="6cb88-128">To explicitly configure the culture, set `CultureInfo.DefaultThreadCurrentCulture` and `CultureInfo.DefaultThreadCurrentUICulture` in `Program.Main`.</span></span>

<span data-ttu-id="6cb88-129">Ve výchozím nastavení Blazorse konfigurace linkeru Blazor pro aplikace pro WebAssembly odříznout informace o mezinárodním prostředí s výjimkou výslovně požadovaných místních hodnot.</span><span class="sxs-lookup"><span data-stu-id="6cb88-129">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="6cb88-130">Další informace a pokyny k řízení chování linkeru naleznete v tématu <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="6cb88-130">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<span data-ttu-id="6cb88-131">I když jazyková verze Blazor , která je standardně vybrána, může být pro většinu uživatelů dostačující, doporučujeme nabídnout uživatelům možnost zadat své upřednostňované národní prostředí.</span><span class="sxs-lookup"><span data-stu-id="6cb88-131">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="6cb88-132">Blazor Ukázkovou aplikaci pro WebAssembly s použitím výběru jazykové verze najdete v ukázkové aplikaci [LocSample](https://github.com/pranavkm/LocSample) Localization.</span><span class="sxs-lookup"><span data-stu-id="6cb88-132">For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### <a name="blazor-server"></a>Blazor<span data-ttu-id="6cb88-133">WebServer</span><span class="sxs-lookup"><span data-stu-id="6cb88-133"> Server</span></span>

Blazor<span data-ttu-id="6cb88-134">Serverové aplikace jsou lokalizovány pomocí [middlewaru lokalizace](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="6cb88-134"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="6cb88-135">Middleware vybere vhodnou jazykovou verzi pro uživatele, kteří žádají o prostředky z aplikace.</span><span class="sxs-lookup"><span data-stu-id="6cb88-135">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="6cb88-136">Tuto jazykovou verzi lze nastavit pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="6cb88-136">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="6cb88-137">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="6cb88-137">Cookies</span></span>](#cookies)
* [<span data-ttu-id="6cb88-138">Poskytnutí uživatelského rozhraní pro výběr jazykové verze</span><span class="sxs-lookup"><span data-stu-id="6cb88-138">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="6cb88-139">Další informace a příklady naleznete v tématu <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="6cb88-139">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="6cb88-140">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="6cb88-140">Cookies</span></span>

<span data-ttu-id="6cb88-141">Soubor cookie lokalizační kultury může zachovat jazykovou verzi uživatele.</span><span class="sxs-lookup"><span data-stu-id="6cb88-141">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="6cb88-142">Soubor cookie je vytvořen `OnGet` metodou stránky hostitele aplikace (*stránky/Host. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="6cb88-142">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="6cb88-143">Middleware lokalizace přečte soubor cookie při následných požadavcích na nastavení jazykové verze uživatele.</span><span class="sxs-lookup"><span data-stu-id="6cb88-143">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="6cb88-144">Použití souboru cookie zajistí, že připojení protokolu WebSocket dokáže správně rozšířit jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="6cb88-144">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="6cb88-145">Pokud jsou schémata lokalizací založena na cestě URL nebo řetězci dotazu, nemusí být schopná pracovat s objekty WebSockets, takže nepůjde zachovat jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="6cb88-145">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="6cb88-146">Proto je doporučený přístup použití souboru cookie lokalizační kultury.</span><span class="sxs-lookup"><span data-stu-id="6cb88-146">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="6cb88-147">Pokud je jazyková verze uložena v souboru cookie lokalizace, je možné použít jakoukoli techniku k přiřazení jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="6cb88-147">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="6cb88-148">Pokud už aplikace má zavedené lokalizační schéma pro ASP.NET Core na straně serveru, pokračujte v používání stávající infrastruktury lokalizace a nastavte soubor cookie lokalizační kultury v rámci schématu aplikace.</span><span class="sxs-lookup"><span data-stu-id="6cb88-148">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="6cb88-149">Následující příklad ukazuje, jak nastavit aktuální jazykovou verzi v souboru cookie, který lze přečíst pomocí middleware Localization.</span><span class="sxs-lookup"><span data-stu-id="6cb88-149">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="6cb88-150">Vytvořte soubor *Pages/_Host. cshtml. cs* s následujícím obsahem v Blazor serverové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="6cb88-150">Create a *Pages/_Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="6cb88-151">Lokalizace je zpracována aplikací v následující posloupnosti událostí:</span><span class="sxs-lookup"><span data-stu-id="6cb88-151">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="6cb88-152">Prohlížeč pošle do aplikace počáteční požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="6cb88-152">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="6cb88-153">Jazyková verze je přiřazena pomocí middleware Localization.</span><span class="sxs-lookup"><span data-stu-id="6cb88-153">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="6cb88-154">`OnGet` Metoda v *_Host. cshtml. cs* uchovává v souboru cookie jazykovou verzi jako součást odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6cb88-154">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="6cb88-155">Prohlížeč otevře připojení pomocí protokolu WebSocket a vytvoří interaktivní Blazor relaci serveru.</span><span class="sxs-lookup"><span data-stu-id="6cb88-155">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="6cb88-156">Middleware lokalizace přečte soubor cookie a přiřadí jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="6cb88-156">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="6cb88-157">Relace Blazor serveru začíná správnou jazykovou verzí.</span><span class="sxs-lookup"><span data-stu-id="6cb88-157">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="6cb88-158">Poskytnutí uživatelského rozhraní pro výběr jazykové verze</span><span class="sxs-lookup"><span data-stu-id="6cb88-158">Provide UI to choose the culture</span></span>

<span data-ttu-id="6cb88-159">K poskytnutí uživatelského rozhraní, které uživateli umožní vybrat jazykovou verzi, se doporučuje *přístup založený na přesměrování* .</span><span class="sxs-lookup"><span data-stu-id="6cb88-159">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="6cb88-160">Proces se podobá tomu, co se stane ve webové aplikaci, když se uživatel pokusí o přístup k zabezpečenému prostředku.</span><span class="sxs-lookup"><span data-stu-id="6cb88-160">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="6cb88-161">Uživatel se přesměruje na přihlašovací stránku a pak se přesměruje zpátky na původní prostředek.</span><span class="sxs-lookup"><span data-stu-id="6cb88-161">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="6cb88-162">Aplikace zachovává vybranou jazykovou verzi uživatele prostřednictvím přesměrování na kontroler.</span><span class="sxs-lookup"><span data-stu-id="6cb88-162">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="6cb88-163">Řadič Nastaví vybranou jazykovou verzi uživatele na soubor cookie a přesměruje uživatele zpět na původní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="6cb88-163">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="6cb88-164">Vytvořte koncový bod HTTP na serveru pro nastavení vybrané jazykové verze uživatele v souboru cookie a proveďte přesměrování zpátky na původní identifikátor URI:</span><span class="sxs-lookup"><span data-stu-id="6cb88-164">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="6cb88-165">Pomocí výsledku `LocalRedirect` akce zabráníte otevírání útoků přes přesměrování.</span><span class="sxs-lookup"><span data-stu-id="6cb88-165">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="6cb88-166">Další informace naleznete v tématu <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="6cb88-166">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="6cb88-167">Následující komponenta ukazuje příklad, jak provést počáteční přesměrování, když uživatel vybere jazykovou verzi:</span><span class="sxs-lookup"><span data-stu-id="6cb88-167">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="6cb88-168">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6cb88-168">Additional resources</span></span>

* <xref:fundamentals/localization>
