---
title: ASP.NET Core Blazor globalizace a lokalizace
author: guardrex
description: Naučte se, jak zpřístupnit součásti Razor uživatelům v různých jazykových verzích a jazycích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: aba62fa7b6285c8ba884652694f1ea3e3a66ed18
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655489"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a>ASP.NET Core Blazor globalizace a lokalizace

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

Komponenty Razor lze zpřístupnit uživatelům v různých jazykových verzích a jazycích. K dispozici jsou následující scénáře globalizace a lokalizace rozhraní .NET:

* . Systém prostředků netto
* Formátování čísla a data specifické pro jazykovou verzi

V současné době se podporuje omezená sada scénářů lokalizace ASP.NET Core:

* `IStringLocalizer<>` *se* v aplikacích Blazor podporuje.
* lokalizace `IHtmlLocalizer<>`, `IViewLocalizer<>`a datových poznámek jsou ASP.NET Core scénáře MVC a **nejsou podporovány** v aplikacích Blazor.

Další informace naleznete v tématu <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalizace

funkce `@bind` Blazorprovádí formáty a analyzuje hodnoty pro zobrazení na základě aktuální jazykové verze uživatele.

K aktuální jazykové verzi je možné přistupovat z vlastnosti <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.

[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) se používá pro následující typy polí (`<input type="{TYPE}" />`):

* `date`
* `number`

Předchozí typy polí:

* Se zobrazují pomocí příslušných pravidel formátování založených na prohlížeči.
* Text s volným formulářem nemůže obsahovat.
* Poskytněte charakteristiky interakce s uživatelem na základě implementace prohlížeče.

Následující typy polí mají specifické požadavky na formátování a aktuálně nejsou podporovány Blazor, protože nejsou podporovány ve všech hlavních prohlížečích:

* `datetime-local`
* `month`
* `week`

`@bind` podporuje parametr `@bind:culture` k poskytnutí <xref:System.Globalization.CultureInfo?displayProperty=fullName> pro analýzu a formátování hodnoty. Při použití typů polí `date` a `number` se nedoporučuje zadat jazykovou verzi. `date` a `number` mají integrovanou Blazor podporu, která poskytuje požadovanou jazykovou verzi.

## <a name="localization"></a>Lokalizace

aplikace Blazor serveru jsou lokalizovány pomocí [middlewaru pro lokalizaci](xref:fundamentals/localization#localization-middleware). Middleware vybere vhodnou jazykovou verzi pro uživatele, kteří žádají o prostředky z aplikace.

Tuto jazykovou verzi lze nastavit pomocí jednoho z následujících přístupů:

* [Soubory cookie](#cookies)
* [Poskytnutí uživatelského rozhraní pro výběr jazykové verze](#provide-ui-to-choose-the-culture)

Další informace a příklady naleznete v tématu <xref:fundamentals/localization>.

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a>Konfigurace linkeru pro mezinárodní navýšení (Blazor WebAssembly)

Ve výchozím nastavení Blazorkonfigurace linkeru pro Blazor aplikace pro WebAssembly odříznout informace o mezinárodním prostředí s výjimkou výslovně požadovaných místních hodnot. Další informace a pokyny k řízení chování linkeru naleznete v tématu <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

### <a name="cookies"></a>Soubory cookie

Soubor cookie lokalizační kultury může zachovat jazykovou verzi uživatele. Soubor cookie je vytvořen metodou `OnGet` stránky hostitele aplikace (*Pages/Host. cshtml. cs*). Middleware lokalizace přečte soubor cookie při následných požadavcích na nastavení jazykové verze uživatele. 

Použití souboru cookie zajistí, že připojení protokolu WebSocket dokáže správně rozšířit jazykovou verzi. Pokud jsou schémata lokalizací založena na cestě URL nebo řetězci dotazu, nemusí být schopná pracovat s objekty WebSockets, takže nepůjde zachovat jazykovou verzi. Proto je doporučený přístup použití souboru cookie lokalizační kultury.

Pokud je jazyková verze uložena v souboru cookie lokalizace, je možné použít jakoukoli techniku k přiřazení jazykové verze. Pokud už aplikace má zavedené lokalizační schéma pro ASP.NET Core na straně serveru, pokračujte v používání stávající infrastruktury lokalizace a nastavte soubor cookie lokalizační kultury v rámci schématu aplikace.

Následující příklad ukazuje, jak nastavit aktuální jazykovou verzi v souboru cookie, který lze přečíst pomocí middleware Localization. Vytvořte soubor *Pages/Host. cshtml. cs* s následujícím obsahem v aplikaci Blazor Server:

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

Lokalizace je zpracována aplikací v následující posloupnosti událostí:

1. Prohlížeč pošle do aplikace počáteční požadavek HTTP.
1. Jazyková verze je přiřazena pomocí middleware Localization.
1. Metoda `OnGet` v *_Host. cshtml. cs* uchovává v rámci odpovědi jazykovou verzi v souboru cookie.
1. Prohlížeč otevře připojení pomocí protokolu WebSocket a vytvoří interaktivní relaci serveru Blazor.
1. Middleware lokalizace přečte soubor cookie a přiřadí jazykovou verzi.
1. Relace Blazor serveru začíná správnou jazykovou verzí.

### <a name="provide-ui-to-choose-the-culture"></a>Poskytnutí uživatelského rozhraní pro výběr jazykové verze

K poskytnutí uživatelského rozhraní, které uživateli umožní vybrat jazykovou verzi, se doporučuje *přístup založený na přesměrování* . Proces se podobá tomu, co se stane ve webové aplikaci, když se uživatel pokusí o přístup k zabezpečenému prostředku&mdash;uživatel je přesměrován na přihlašovací stránku a pak se přesměruje zpátky na původní prostředek. 

Aplikace zachovává vybranou jazykovou verzi uživatele prostřednictvím přesměrování na kontroler. Řadič Nastaví vybranou jazykovou verzi uživatele na soubor cookie a přesměruje uživatele zpět na původní identifikátor URI.

Vytvořte koncový bod HTTP na serveru pro nastavení vybrané jazykové verze uživatele v souboru cookie a proveďte přesměrování zpátky na původní identifikátor URI:

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
> Použijte `LocalRedirect` výsledek akce, aby se zabránilo otevřeným útokům přes přesměrování. Další informace naleznete v tématu <xref:security/preventing-open-redirects>.

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
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/localization>
