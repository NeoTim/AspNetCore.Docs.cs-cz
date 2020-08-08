---
title: ASP.NET Core Blazor globalizace a lokalizace
author: guardrex
description: Naučte se, jak zpřístupnit Razor součásti uživatelům v různých jazykových verzích a jazycích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
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
ms.openlocfilehash: 59b6e4cb2f466594d8a105a239e175e9c7b37ad8
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014241"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a>ASP.NET Core Blazor globalizace a lokalizace

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

Razorkomponenty lze zpřístupnit uživatelům v různých jazykových verzích a jazycích. K dispozici jsou následující scénáře globalizace a lokalizace rozhraní .NET:

* . Systém prostředků netto
* Formátování čísla a data specifické pro jazykovou verzi

V současné době se podporuje omezená sada scénářů lokalizace ASP.NET Core:

* <xref:Microsoft.Extensions.Localization.IStringLocalizer>a <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *jsou podporované* v Blazor aplikacích.
* <xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer><xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>lokalizace datových poznámek, a jsou ASP.NET Core scénáře MVC a **nejsou** v Blazor aplikacích podporovány.

Další informace naleznete v tématu <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalizace

Blazor[`@bind`](xref:mvc/views/razor#bind)funkce provádí formáty a analyzuje hodnoty pro zobrazení na základě aktuální jazykové verze uživatele.

K aktuální jazykové verzi je možné přistupovat z <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> Vlastnosti.

<xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType>se používá pro následující typy polí ( `<input type="{TYPE}" />` ):

* `date`
* `number`

Předchozí typy polí:

* Se zobrazují pomocí příslušných pravidel formátování založených na prohlížeči.
* Text s volným formulářem nemůže obsahovat.
* Poskytněte charakteristiky interakce s uživatelem na základě implementace prohlížeče.

Následující typy polí mají specifické požadavky na formátování a aktuálně nejsou podporovány, Blazor protože nejsou podporovány ve všech hlavních prohlížečích:

* `datetime-local`
* `month`
* `week`

[`@bind`](xref:mvc/views/razor#bind)podporuje `@bind:culture` parametr <xref:System.Globalization.CultureInfo?displayProperty=fullName> pro zajištění analýzy a formátování hodnoty. Specifikace jazykové verze není doporučena při použití `date` `number` typů polí a. `date`a `number` mají integrovanou Blazor podporu, která poskytuje požadovanou jazykovou verzi.

## <a name="localization"></a>Lokalizace

### Blazor WebAssembly

Blazor WebAssemblyaplikace nastavily jazykovou verzi pomocí [Předvolby jazyka](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)uživatele.

Chcete-li explicitně konfigurovat jazykovou verzi, nastavte <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> a <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> v `Program.Main` .

Ve výchozím nastavení Blazor je konfigurace linkeru pro Blazor WebAssembly aplikace odříznout informace o mezinárodním prostředí s výjimkou výslovně požadovaných místních hodnot. Další informace a pokyny k řízení chování linkeru naleznete v tématu <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization> .

I když jazyková verze, která je standardně Blazor vybrána, může být pro většinu uživatelů dostačující, doporučujeme nabídnout uživatelům možnost zadat své upřednostňované národní prostředí. Blazor WebAssemblyUkázkovou aplikaci s použitím výběru jazykové verze najdete v [`LocSample`](https://github.com/pranavkm/LocSample) ukázkové aplikaci Localization.

### Blazor Server

Blazor Serveraplikace jsou lokalizovány pomocí [middleware lokalizace](xref:fundamentals/localization#localization-middleware). Middleware vybere vhodnou jazykovou verzi pro uživatele, kteří žádají o prostředky z aplikace.

Tuto jazykovou verzi lze nastavit pomocí jednoho z následujících přístupů:

* [Cookiepracují](#cookies)
* [Poskytnutí uživatelského rozhraní pro výběr jazykové verze](#provide-ui-to-choose-the-culture)

Další informace a příklady naleznete v tématu <xref:fundamentals/localization> .

#### <a name="no-loccookies"></a>Cookiepracují

Jazyková verze lokalizace cookie může zachovat jazykovou verzi uživatele. Middleware lokalizace přečte cookie při následujících požadavcích na nastavení jazykové verze uživatele. 

Použití nástroje cookie zajistí, že připojení protokolu WebSocket dokáže správně rozšířit jazykovou verzi. Pokud jsou schémata lokalizací založena na cestě URL nebo řetězci dotazu, nemusí být schopná pracovat s objekty WebSockets, takže nepůjde zachovat jazykovou verzi. Proto je doporučený přístup použití jazykové verze lokalizace cookie .

Pokud je jazyková verze trvale v lokalizaci, lze použít jakoukoli techniku k přiřazení jazykové verze cookie . Pokud už aplikace má zavedené lokalizační schéma pro ASP.NET Core na straně serveru, pokračujte v používání stávající infrastruktury lokalizace a nastavte jazykovou verzi lokalizace cookie v rámci schématu aplikace.

Následující příklad ukazuje, jak nastavit aktuální jazykovou verzi v cookie , kterou lze přečíst pomocí middleware Localization. Vytvořte Razor v `Pages/_Host.cshtml` souboru výraz hned v otevírací `<body>` značce:

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

Lokalizace je zpracována aplikací v následující posloupnosti událostí:

1. Prohlížeč pošle do aplikace počáteční požadavek HTTP.
1. Jazyková verze je přiřazena pomocí middleware Localization.
1. RazorVýraz na `_Host` stránce ( `_Host.cshtml` ) zachovává jazykovou verzi v rámci cookie odpovědi.
1. Prohlížeč otevře připojení pomocí protokolu WebSocket a vytvoří interaktivní Blazor Server relaci.
1. Middleware lokalizace přečte cookie a přiřadí jazykovou verzi.
1. Blazor ServerRelace začíná správnou jazykovou verzí.

#### <a name="provide-ui-to-choose-the-culture"></a>Poskytnutí uživatelského rozhraní pro výběr jazykové verze

K poskytnutí uživatelského rozhraní, které uživateli umožní vybrat jazykovou verzi, se doporučuje *přístup založený na přesměrování* . Proces se podobá tomu, co se stane ve webové aplikaci, když se uživatel pokusí o přístup k zabezpečenému prostředku. Uživatel se přesměruje na přihlašovací stránku a pak se přesměruje zpátky na původní prostředek. 

Aplikace zachovává vybranou jazykovou verzi uživatele prostřednictvím přesměrování na kontroler. Řadič Nastaví vybranou jazykovou verzi uživatele na cookie a přesměruje uživatele zpět na původní identifikátor URI.

Vytvořte koncový bod HTTP na serveru pro nastavení vybrané jazykové verze uživatele v cookie a proveďte přesměrování zpátky na původní identifikátor URI:

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
> Pomocí <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> výsledku akce zabráníte otevírání útoků přes přesměrování. Další informace naleznete v tématu <xref:security/preventing-open-redirects>.

Pokud aplikace není nakonfigurovaná na zpracování akcí kontroleru:

* Přidat služby MVC do kolekce služeb v `Startup.ConfigureServices` :

  ```csharp
  services.AddControllers();
  ```

* Přidat směrování koncových bodů řadiče v `Startup.Configure` :

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

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

## <a name="additional-resources"></a>Další materiály

* <xref:fundamentals/localization>
