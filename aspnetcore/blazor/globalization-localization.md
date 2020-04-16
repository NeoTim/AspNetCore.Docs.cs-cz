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
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a>ASP.NET Blazor Core globalizace a lokalizace

[Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)

Komponenty razor mohou být přístupné uživatelům ve více kulturách a jazycích. K dispozici jsou následující scénáře globalizace a lokalizace rozhraní .NET:

* . NET je zdroje systému
* Formátování čísla a data specifické pro jazykovou verzi

V současné době je podporována omezená sada scénářů lokalizace ASP.NET jádra:

* `IStringLocalizer<>`*je podporována* v Blazor aplikacích.
* `IHtmlLocalizer<>`, `IViewLocalizer<>`a data poznámky lokalizace jsou ASP.NET scénáře Core Blazor MVC a **není podporovánv** aplikacích.

Další informace naleznete v tématu <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalizace

BlazorFunkce `@bind` společnosti provádí formáty a analyzuje hodnoty pro zobrazení na základě aktuální jazykové verze uživatele.

Aktuální jazyková verze je <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> přístupná z vlastnosti.

[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) se používá pro následující`<input type="{TYPE}" />`typy polí ( ):

* `date`
* `number`

Předchozí typy polí:

* Jsou zobrazeny pomocí příslušných pravidel formátování založených na prohlížeči.
* Nesmí obsahovat volný text.
* Poskytněte charakteristiky interakce uživatele na základě implementace prohlížeče.

Následující typy polí mají specifické požadavky na formátování a Blazor nejsou aktuálně podporovány, protože nejsou podporovány všemi hlavními prohlížeči:

* `datetime-local`
* `month`
* `week`

`@bind`podporuje `@bind:culture` parametr pro <xref:System.Globalization.CultureInfo?displayProperty=fullName> analýzu a formátování hodnoty. Určení jazykové verze se nedoporučuje při `date` `number` použití typů polí a. `date`a `number` mají integrovanou Blazor podporu, která poskytuje požadovanou jazykovou verzi.

## <a name="localization"></a>Lokalizace

### <a name="opno-locblazor-webassembly"></a>BlazorWebová sestava

Ve výchozím Blazornastavení konfigurace propojovacího programu pro aplikace Blazor WebAssembly prokládá internacionalizaci s výjimkou explicitně požadovaných národních prostředí. Další informace a pokyny k řízení chování propojovacího systému naleznete v tématu <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

<!-- HOLD FOR 3.2 PREVIEW 4: Replace prior paragraph with ...

Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).

To explicitly configure the culture, set `CultureInfo.DefaultThreadCurrentCulture` and `CultureInfo.DefaultThreadCurrentUICulture` in `Program.Main`.

By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested. For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale. For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.

-->

### <a name="opno-locblazor-server"></a>BlazorServer

BlazorServerové aplikace jsou lokalizovány pomocí [middlewaru lokalizace](xref:fundamentals/localization#localization-middleware). Middleware vybere příslušnou jazykovou verzi pro uživatele požadující prostředky z aplikace.

Jazykovou verzi lze nastavit pomocí jednoho z následujících přístupů:

* [Soubory cookie](#cookies)
* [Poskytnout uI zvolit jazykovou verzi](#provide-ui-to-choose-the-culture)

Další informace a příklady <xref:fundamentals/localization>naleznete v tématu .

#### <a name="cookies"></a>Soubory cookie

Soubor cookie jazykové verze lokalizace může zachovat jazykovou verzi uživatele. Soubor cookie je `OnGet` vytvořen metodou hostitelské stránky aplikace (*Pages/Host.cshtml.cs*). Middleware lokalizace přečte soubor cookie na následné požadavky na nastavení jazykové verze uživatele. 

Použití souboru cookie zajišťuje, že připojení WebSocket může správně šířit jazykovou verzi. Pokud jsou lokalizační schémata založena na cestě url nebo řetězci dotazu, nemusí být schéma schopno pracovat s websockety, a proto se nepodaří zachovat jazykovou verzi. Proto použití souboru cookie jazykové verze lokalizace je doporučený přístup.

Libovolnou techniku lze použít k přiřazení jazykové verze, pokud je jazyková verze trvalá v lokalizačním souboru cookie. Pokud aplikace již má zavedené schéma lokalizace pro ASP.NET core na straně serveru, pokračujte v používání stávající infrastruktury lokalizace aplikace a nastavte soubor cookie jazykové verze lokalizace v rámci schématu aplikace.

Následující příklad ukazuje, jak nastavit aktuální jazykovou verzi v souboru cookie, který lze číst middlewaru lokalizace. Vytvořte soubor *Pages/_Host.cshtml.cs* s následujícím Blazor obsahem v aplikaci Server:

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

Lokalizace je zpracována aplikací v následujícím pořadí událostí:

1. Prohlížeč odešle do aplikace počáteční požadavek HTTP.
1. Jazyková verze je přiřazena middlewaru lokalizace.
1. Metoda `OnGet` v *_Host.cshtml.cs* přetrvává jazykovou verzi v souboru cookie jako součást odpovědi.
1. Prohlížeč otevře připojení WebSocket a Blazor vytvoří interaktivní relaci serveru.
1. Middleware lokalizace přečte soubor cookie a přiřadí jazykovou verzi.
1. Relace Blazor serveru začíná správnou jazykovou verzí.

#### <a name="provide-ui-to-choose-the-culture"></a>Poskytnout uI zvolit jazykovou verzi

Chcete-li poskytnout uživatelské rozhraní, které uživateli umožní vybrat jazykovou verzi, doporučuje se *přístup založený na přesměrování.* Tento proces je podobný tomu, co se děje ve webové aplikaci, když se uživatel pokusí o přístup k zabezpečenému prostředku. Uživatel je přesměrován na přihlašovací stránku a poté přesměrován zpět na původní prostředek. 

Aplikace zachová vybranou jazykovou verzi uživatele prostřednictvím přesměrování na řadič. Kontroler nastaví vybranou jazykovou verzi uživatele do souboru cookie a přesměruje uživatele zpět na původní identifikátor URI.

Vytvořte koncový bod HTTP na serveru, který nastaví vybranou jazykovou verzi uživatele v souboru cookie a provede přesměrování zpět na původní identifikátor URI:

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
> Výsledek `LocalRedirect` akce použijte k zabránění útokům otevřeného přesměrování. Další informace naleznete v tématu <xref:security/preventing-open-redirects>.

Následující komponenta ukazuje příklad, jak provést počáteční přesměrování, když uživatel vybere jazykovou verzi:

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

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/localization>
