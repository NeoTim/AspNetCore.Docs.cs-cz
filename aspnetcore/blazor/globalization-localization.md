---
title: ASP.NET Core Blazor globalizace a lokalizace
author: guardrex
description: Naučte se, jak zpřístupnit Razor součásti uživatelům v různých jazykových verzích a jazycích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 2b8820acba564bdfb85f8338ed5482573960fbb4
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080274"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a><span data-ttu-id="536f2-103">ASP.NET Core Blazor globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="536f2-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="536f2-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="536f2-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="536f2-105">Razor komponenty lze zpřístupnit uživatelům v různých jazykových verzích a jazycích.</span><span class="sxs-lookup"><span data-stu-id="536f2-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="536f2-106">K dispozici jsou následující scénáře globalizace a lokalizace rozhraní .NET:</span><span class="sxs-lookup"><span data-stu-id="536f2-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="536f2-107">. Systém prostředků netto</span><span class="sxs-lookup"><span data-stu-id="536f2-107">.NET's resources system</span></span>
* <span data-ttu-id="536f2-108">Formátování čísla a data specifické pro jazykovou verzi</span><span class="sxs-lookup"><span data-stu-id="536f2-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="536f2-109">V současné době se podporuje omezená sada scénářů lokalizace ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="536f2-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="536f2-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> a <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *jsou podporované* v Blazor aplikacích.</span><span class="sxs-lookup"><span data-stu-id="536f2-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in Blazor apps.</span></span>
* <span data-ttu-id="536f2-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer><xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>lokalizace datových poznámek, a jsou ASP.NET Core scénáře MVC a **nejsou** v Blazor aplikacích podporovány.</span><span class="sxs-lookup"><span data-stu-id="536f2-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="536f2-112">Další informace naleznete v tématu <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="536f2-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="536f2-113">Globalizace</span><span class="sxs-lookup"><span data-stu-id="536f2-113">Globalization</span></span>

<span data-ttu-id="536f2-114">Blazor[`@bind`](xref:mvc/views/razor#bind)funkce provádí formáty a analyzuje hodnoty pro zobrazení na základě aktuální jazykové verze uživatele.</span><span class="sxs-lookup"><span data-stu-id="536f2-114">Blazor's [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="536f2-115">K aktuální jazykové verzi je možné přistupovat z <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="536f2-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="536f2-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> se používá pro následující typy polí ( `<input type="{TYPE}" />` ):</span><span class="sxs-lookup"><span data-stu-id="536f2-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="536f2-117">Předchozí typy polí:</span><span class="sxs-lookup"><span data-stu-id="536f2-117">The preceding field types:</span></span>

* <span data-ttu-id="536f2-118">Se zobrazují pomocí příslušných pravidel formátování založených na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="536f2-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="536f2-119">Text s volným formulářem nemůže obsahovat.</span><span class="sxs-lookup"><span data-stu-id="536f2-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="536f2-120">Poskytněte charakteristiky interakce s uživatelem na základě implementace prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="536f2-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="536f2-121">Následující typy polí mají specifické požadavky na formátování a aktuálně nejsou podporovány, Blazor protože nejsou podporovány ve všech hlavních prohlížečích:</span><span class="sxs-lookup"><span data-stu-id="536f2-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="536f2-122">[`@bind`](xref:mvc/views/razor#bind) podporuje `@bind:culture` parametr <xref:System.Globalization.CultureInfo?displayProperty=fullName> pro zajištění analýzy a formátování hodnoty.</span><span class="sxs-lookup"><span data-stu-id="536f2-122">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="536f2-123">Specifikace jazykové verze není doporučena při použití `date` `number` typů polí a.</span><span class="sxs-lookup"><span data-stu-id="536f2-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="536f2-124">`date` a `number` mají integrovanou Blazor podporu, která poskytuje požadovanou jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="536f2-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="536f2-125">Lokalizace</span><span class="sxs-lookup"><span data-stu-id="536f2-125">Localization</span></span>

### Blazor WebAssembly

<span data-ttu-id="536f2-126">Blazor WebAssembly aplikace nastavily jazykovou verzi pomocí [Předvolby jazyka](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)uživatele.</span><span class="sxs-lookup"><span data-stu-id="536f2-126">Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="536f2-127">Chcete-li explicitně konfigurovat jazykovou verzi, nastavte <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> a <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> v `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="536f2-127">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="536f2-128">Ve výchozím nastavení obsahuje Blazor WebAssembly prostředky globalizace vyžadované k zobrazení hodnot, jako jsou například data a měna, v jazykové verzi uživatele.</span><span class="sxs-lookup"><span data-stu-id="536f2-128">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="536f2-129">Pokud aplikace nevyžaduje lokalizaci, můžete aplikaci nakonfigurovat tak, aby podporovala invariantní jazykovou verzi, která je založena na `en-US` jazykové verzi:</span><span class="sxs-lookup"><span data-stu-id="536f2-129">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="536f2-130">Ve výchozím nastavení konfigurace linkeru pro Blazor WebAssembly aplikace vyřadí informace o mezinárodním prostředí, s výjimkou výslovně požadovaných místních hodnot.</span><span class="sxs-lookup"><span data-stu-id="536f2-130">By default, the Intermediate Language (IL) Linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="536f2-131">Další informace naleznete v tématu <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="536f2-131">For more information, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

::: moniker-end

<span data-ttu-id="536f2-132">I když jazyková verze, která je standardně Blazor vybrána, může být pro většinu uživatelů dostačující, doporučujeme nabídnout uživatelům možnost zadat své upřednostňované národní prostředí.</span><span class="sxs-lookup"><span data-stu-id="536f2-132">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="536f2-133">Blazor WebAssemblyUkázkovou aplikaci s použitím výběru jazykové verze najdete v [`LocSample`](https://github.com/pranavkm/LocSample) ukázkové aplikaci Localization.</span><span class="sxs-lookup"><span data-stu-id="536f2-133">For a Blazor WebAssembly sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### Blazor Server

<span data-ttu-id="536f2-134">Blazor Server aplikace jsou lokalizovány pomocí [middleware lokalizace](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="536f2-134">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="536f2-135">Middleware vybere vhodnou jazykovou verzi pro uživatele, kteří žádají o prostředky z aplikace.</span><span class="sxs-lookup"><span data-stu-id="536f2-135">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="536f2-136">Tuto jazykovou verzi lze nastavit pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="536f2-136">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="536f2-137">[Cookiepracují](#cookies)</span><span class="sxs-lookup"><span data-stu-id="536f2-137">[Cookies](#cookies)</span></span>
* [<span data-ttu-id="536f2-138">Poskytnutí uživatelského rozhraní pro výběr jazykové verze</span><span class="sxs-lookup"><span data-stu-id="536f2-138">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="536f2-139">Další informace a příklady naleznete v tématu <xref:fundamentals/localization> .</span><span class="sxs-lookup"><span data-stu-id="536f2-139">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="no-loccookies"></a><span data-ttu-id="536f2-140">Cookiepracují</span><span class="sxs-lookup"><span data-stu-id="536f2-140">Cookies</span></span>

<span data-ttu-id="536f2-141">Jazyková verze lokalizace cookie může zachovat jazykovou verzi uživatele.</span><span class="sxs-lookup"><span data-stu-id="536f2-141">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="536f2-142">Middleware lokalizace přečte cookie při následujících požadavcích na nastavení jazykové verze uživatele.</span><span class="sxs-lookup"><span data-stu-id="536f2-142">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="536f2-143">Použití nástroje cookie zajistí, že připojení protokolu WebSocket dokáže správně rozšířit jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="536f2-143">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="536f2-144">Pokud jsou schémata lokalizací založena na cestě URL nebo řetězci dotazu, nemusí být schopná pracovat s objekty WebSockets, takže nepůjde zachovat jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="536f2-144">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="536f2-145">Proto je doporučený přístup použití jazykové verze lokalizace cookie .</span><span class="sxs-lookup"><span data-stu-id="536f2-145">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="536f2-146">Pokud je jazyková verze trvale v lokalizaci, lze použít jakoukoli techniku k přiřazení jazykové verze cookie .</span><span class="sxs-lookup"><span data-stu-id="536f2-146">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="536f2-147">Pokud už aplikace má zavedené lokalizační schéma pro ASP.NET Core na straně serveru, pokračujte v používání stávající infrastruktury lokalizace a nastavte jazykovou verzi lokalizace cookie v rámci schématu aplikace.</span><span class="sxs-lookup"><span data-stu-id="536f2-147">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="536f2-148">Následující příklad ukazuje, jak nastavit aktuální jazykovou verzi v cookie , kterou lze přečíst pomocí middleware Localization.</span><span class="sxs-lookup"><span data-stu-id="536f2-148">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="536f2-149">Vytvořte Razor v `Pages/_Host.cshtml` souboru výraz hned v otevírací `<body>` značce:</span><span class="sxs-lookup"><span data-stu-id="536f2-149">Create a Razor expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

<span data-ttu-id="536f2-150">Lokalizace je zpracována aplikací v následující posloupnosti událostí:</span><span class="sxs-lookup"><span data-stu-id="536f2-150">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="536f2-151">Prohlížeč pošle do aplikace počáteční požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="536f2-151">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="536f2-152">Jazyková verze je přiřazena pomocí middleware Localization.</span><span class="sxs-lookup"><span data-stu-id="536f2-152">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="536f2-153">RazorVýraz na `_Host` stránce ( `_Host.cshtml` ) zachovává jazykovou verzi v rámci cookie odpovědi.</span><span class="sxs-lookup"><span data-stu-id="536f2-153">The Razor expression in the `_Host` page (`_Host.cshtml`) persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="536f2-154">Prohlížeč otevře připojení pomocí protokolu WebSocket a vytvoří interaktivní Blazor Server relaci.</span><span class="sxs-lookup"><span data-stu-id="536f2-154">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="536f2-155">Middleware lokalizace přečte cookie a přiřadí jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="536f2-155">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="536f2-156">Blazor ServerRelace začíná správnou jazykovou verzí.</span><span class="sxs-lookup"><span data-stu-id="536f2-156">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="536f2-157">Poskytnutí uživatelského rozhraní pro výběr jazykové verze</span><span class="sxs-lookup"><span data-stu-id="536f2-157">Provide UI to choose the culture</span></span>

<span data-ttu-id="536f2-158">K poskytnutí uživatelského rozhraní, které uživateli umožní vybrat jazykovou verzi, se doporučuje *přístup založený na přesměrování* .</span><span class="sxs-lookup"><span data-stu-id="536f2-158">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="536f2-159">Proces se podobá tomu, co se stane ve webové aplikaci, když se uživatel pokusí o přístup k zabezpečenému prostředku.</span><span class="sxs-lookup"><span data-stu-id="536f2-159">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="536f2-160">Uživatel se přesměruje na přihlašovací stránku a pak se přesměruje zpátky na původní prostředek.</span><span class="sxs-lookup"><span data-stu-id="536f2-160">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="536f2-161">Aplikace zachovává vybranou jazykovou verzi uživatele prostřednictvím přesměrování na kontroler.</span><span class="sxs-lookup"><span data-stu-id="536f2-161">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="536f2-162">Řadič Nastaví vybranou jazykovou verzi uživatele na cookie a přesměruje uživatele zpět na původní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="536f2-162">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="536f2-163">Vytvořte koncový bod HTTP na serveru pro nastavení vybrané jazykové verze uživatele v cookie a proveďte přesměrování zpátky na původní identifikátor URI:</span><span class="sxs-lookup"><span data-stu-id="536f2-163">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="536f2-164">Pomocí <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> výsledku akce zabráníte otevírání útoků přes přesměrování.</span><span class="sxs-lookup"><span data-stu-id="536f2-164">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="536f2-165">Další informace naleznete v tématu <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="536f2-165">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="536f2-166">Pokud aplikace není nakonfigurovaná na zpracování akcí kontroleru:</span><span class="sxs-lookup"><span data-stu-id="536f2-166">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="536f2-167">Přidat služby MVC do kolekce služeb v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="536f2-167">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="536f2-168">Přidat směrování koncových bodů řadiče v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="536f2-168">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="536f2-169">Následující komponenta ukazuje příklad, jak provést počáteční přesměrování, když uživatel vybere jazykovou verzi:</span><span class="sxs-lookup"><span data-stu-id="536f2-169">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="536f2-170">Další materiály</span><span class="sxs-lookup"><span data-stu-id="536f2-170">Additional resources</span></span>

* <xref:fundamentals/localization>
