---
title: ASP.NET Blazor Core globalizace a lokalizace
author: guardrex
description: Naučte se, jak zpřístupnit komponenty Razor uživatelům ve více kulturách a jazycích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 0883a67e0129590f7a3fb68689eaba8d85e5523f
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440711"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a><span data-ttu-id="be22d-103">ASP.NET Blazor Core globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="be22d-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="be22d-104">[Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="be22d-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="be22d-105">Komponenty razor mohou být přístupné uživatelům ve více kulturách a jazycích.</span><span class="sxs-lookup"><span data-stu-id="be22d-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="be22d-106">K dispozici jsou následující scénáře globalizace a lokalizace rozhraní .NET:</span><span class="sxs-lookup"><span data-stu-id="be22d-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="be22d-107">. NET je zdroje systému</span><span class="sxs-lookup"><span data-stu-id="be22d-107">.NET's resources system</span></span>
* <span data-ttu-id="be22d-108">Formátování čísla a data specifické pro jazykovou verzi</span><span class="sxs-lookup"><span data-stu-id="be22d-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="be22d-109">V současné době je podporována omezená sada scénářů lokalizace ASP.NET jádra:</span><span class="sxs-lookup"><span data-stu-id="be22d-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="be22d-110">`IStringLocalizer<>`*je podporována* v Blazor aplikacích.</span><span class="sxs-lookup"><span data-stu-id="be22d-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="be22d-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`a data poznámky lokalizace jsou ASP.NET scénáře Core Blazor MVC a **není podporovánv** aplikacích.</span><span class="sxs-lookup"><span data-stu-id="be22d-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="be22d-112">Další informace naleznete v tématu <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="be22d-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="be22d-113">Globalizace</span><span class="sxs-lookup"><span data-stu-id="be22d-113">Globalization</span></span>

Blazor<span data-ttu-id="be22d-114">Funkce `@bind` společnosti provádí formáty a analyzuje hodnoty pro zobrazení na základě aktuální jazykové verze uživatele.</span><span class="sxs-lookup"><span data-stu-id="be22d-114">'s `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="be22d-115">Aktuální jazyková verze je <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> přístupná z vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="be22d-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="be22d-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) se používá pro následující`<input type="{TYPE}" />`typy polí ( ):</span><span class="sxs-lookup"><span data-stu-id="be22d-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="be22d-117">Předchozí typy polí:</span><span class="sxs-lookup"><span data-stu-id="be22d-117">The preceding field types:</span></span>

* <span data-ttu-id="be22d-118">Jsou zobrazeny pomocí příslušných pravidel formátování založených na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="be22d-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="be22d-119">Nesmí obsahovat volný text.</span><span class="sxs-lookup"><span data-stu-id="be22d-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="be22d-120">Poskytněte charakteristiky interakce uživatele na základě implementace prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="be22d-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="be22d-121">Následující typy polí mají specifické požadavky na formátování a Blazor nejsou aktuálně podporovány, protože nejsou podporovány všemi hlavními prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="be22d-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="be22d-122">`@bind`podporuje `@bind:culture` parametr pro <xref:System.Globalization.CultureInfo?displayProperty=fullName> analýzu a formátování hodnoty.</span><span class="sxs-lookup"><span data-stu-id="be22d-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="be22d-123">Určení jazykové verze se nedoporučuje při `date` `number` použití typů polí a.</span><span class="sxs-lookup"><span data-stu-id="be22d-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="be22d-124">`date`a `number` mají integrovanou Blazor podporu, která poskytuje požadovanou jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="be22d-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="be22d-125">Lokalizace</span><span class="sxs-lookup"><span data-stu-id="be22d-125">Localization</span></span>

### <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="be22d-126">Webová sestava</span><span class="sxs-lookup"><span data-stu-id="be22d-126"> WebAssembly</span></span>

<span data-ttu-id="be22d-127">Ve výchozím Blazornastavení konfigurace propojovacího programu pro aplikace Blazor WebAssembly prokládá internacionalizaci s výjimkou explicitně požadovaných národních prostředí.</span><span class="sxs-lookup"><span data-stu-id="be22d-127">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="be22d-128">Další informace a pokyny k řízení chování propojovacího systému naleznete v tématu <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="be22d-128">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<!-- HOLD FOR 3.2 PREVIEW 4: Replace prior paragraph with ...

Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).

To explicitly configure the culture, set `CultureInfo.DefaultThreadCurrentCulture` and `CultureInfo.DefaultThreadCurrentUICulture` in `Program.Main`.

By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested. For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale. For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.

-->

### <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="be22d-129">Server</span><span class="sxs-lookup"><span data-stu-id="be22d-129"> Server</span></span>

Blazor<span data-ttu-id="be22d-130">Serverové aplikace jsou lokalizovány pomocí [middlewaru lokalizace](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="be22d-130"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="be22d-131">Middleware vybere příslušnou jazykovou verzi pro uživatele požadující prostředky z aplikace.</span><span class="sxs-lookup"><span data-stu-id="be22d-131">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="be22d-132">Jazykovou verzi lze nastavit pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="be22d-132">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="be22d-133">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="be22d-133">Cookies</span></span>](#cookies)
* [<span data-ttu-id="be22d-134">Poskytnout uI zvolit jazykovou verzi</span><span class="sxs-lookup"><span data-stu-id="be22d-134">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="be22d-135">Další informace a příklady <xref:fundamentals/localization>naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="be22d-135">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="be22d-136">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="be22d-136">Cookies</span></span>

<span data-ttu-id="be22d-137">Soubor cookie jazykové verze lokalizace může zachovat jazykovou verzi uživatele.</span><span class="sxs-lookup"><span data-stu-id="be22d-137">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="be22d-138">Soubor cookie je `OnGet` vytvořen metodou hostitelské stránky aplikace (*Pages/Host.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="be22d-138">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="be22d-139">Middleware lokalizace přečte soubor cookie na následné požadavky na nastavení jazykové verze uživatele.</span><span class="sxs-lookup"><span data-stu-id="be22d-139">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="be22d-140">Použití souboru cookie zajišťuje, že připojení WebSocket může správně šířit jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="be22d-140">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="be22d-141">Pokud jsou lokalizační schémata založena na cestě url nebo řetězci dotazu, nemusí být schéma schopno pracovat s websockety, a proto se nepodaří zachovat jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="be22d-141">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="be22d-142">Proto použití souboru cookie jazykové verze lokalizace je doporučený přístup.</span><span class="sxs-lookup"><span data-stu-id="be22d-142">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="be22d-143">Libovolnou techniku lze použít k přiřazení jazykové verze, pokud je jazyková verze trvalá v lokalizačním souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="be22d-143">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="be22d-144">Pokud aplikace již má zavedené schéma lokalizace pro ASP.NET core na straně serveru, pokračujte v používání stávající infrastruktury lokalizace aplikace a nastavte soubor cookie jazykové verze lokalizace v rámci schématu aplikace.</span><span class="sxs-lookup"><span data-stu-id="be22d-144">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="be22d-145">Následující příklad ukazuje, jak nastavit aktuální jazykovou verzi v souboru cookie, který lze číst middlewaru lokalizace.</span><span class="sxs-lookup"><span data-stu-id="be22d-145">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="be22d-146">Vytvořte soubor *Pages/_Host.cshtml.cs* s následujícím Blazor obsahem v aplikaci Server:</span><span class="sxs-lookup"><span data-stu-id="be22d-146">Create a *Pages/_Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="be22d-147">Lokalizace je zpracována aplikací v následujícím pořadí událostí:</span><span class="sxs-lookup"><span data-stu-id="be22d-147">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="be22d-148">Prohlížeč odešle do aplikace počáteční požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="be22d-148">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="be22d-149">Jazyková verze je přiřazena middlewaru lokalizace.</span><span class="sxs-lookup"><span data-stu-id="be22d-149">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="be22d-150">Metoda `OnGet` v *_Host.cshtml.cs* přetrvává jazykovou verzi v souboru cookie jako součást odpovědi.</span><span class="sxs-lookup"><span data-stu-id="be22d-150">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="be22d-151">Prohlížeč otevře připojení WebSocket a Blazor vytvoří interaktivní relaci serveru.</span><span class="sxs-lookup"><span data-stu-id="be22d-151">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="be22d-152">Middleware lokalizace přečte soubor cookie a přiřadí jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="be22d-152">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="be22d-153">Relace Blazor serveru začíná správnou jazykovou verzí.</span><span class="sxs-lookup"><span data-stu-id="be22d-153">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="be22d-154">Poskytnout uI zvolit jazykovou verzi</span><span class="sxs-lookup"><span data-stu-id="be22d-154">Provide UI to choose the culture</span></span>

<span data-ttu-id="be22d-155">Chcete-li poskytnout uživatelské rozhraní, které uživateli umožní vybrat jazykovou verzi, doporučuje se *přístup založený na přesměrování.*</span><span class="sxs-lookup"><span data-stu-id="be22d-155">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="be22d-156">Tento proces je podobný tomu, co se děje ve webové aplikaci, když se uživatel pokusí o přístup k zabezpečenému prostředku.</span><span class="sxs-lookup"><span data-stu-id="be22d-156">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="be22d-157">Uživatel je přesměrován na přihlašovací stránku a poté přesměrován zpět na původní prostředek.</span><span class="sxs-lookup"><span data-stu-id="be22d-157">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="be22d-158">Aplikace zachová vybranou jazykovou verzi uživatele prostřednictvím přesměrování na řadič.</span><span class="sxs-lookup"><span data-stu-id="be22d-158">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="be22d-159">Kontroler nastaví vybranou jazykovou verzi uživatele do souboru cookie a přesměruje uživatele zpět na původní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="be22d-159">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="be22d-160">Vytvořte koncový bod HTTP na serveru, který nastaví vybranou jazykovou verzi uživatele v souboru cookie a provede přesměrování zpět na původní identifikátor URI:</span><span class="sxs-lookup"><span data-stu-id="be22d-160">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="be22d-161">Výsledek `LocalRedirect` akce použijte k zabránění útokům otevřeného přesměrování.</span><span class="sxs-lookup"><span data-stu-id="be22d-161">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="be22d-162">Další informace naleznete v tématu <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="be22d-162">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="be22d-163">Následující komponenta ukazuje příklad, jak provést počáteční přesměrování, když uživatel vybere jazykovou verzi:</span><span class="sxs-lookup"><span data-stu-id="be22d-163">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="be22d-164">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="be22d-164">Additional resources</span></span>

* <xref:fundamentals/localization>
