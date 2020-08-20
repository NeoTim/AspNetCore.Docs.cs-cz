---
title: Povolit generování kódu QR pro aplikace TOTP Authenticator v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak povolit generování kódu QR pro aplikace TOTP Authenticator, které fungují s ASP.NET Core dvojúrovňové ověřování.
ms.author: riande
ms.date: 08/14/2018
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
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: e61aa925262fc9fe25c7bb2d37958cfaa308aeaf
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630793"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a>Povolit generování kódu QR pro aplikace TOTP Authenticator v ASP.NET Core

::: moniker range="<= aspnetcore-2.0"

Kódy QR vyžadují ASP.NET Core 2,0 nebo novější.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

ASP.NET Core lodí s podporou ověřovacích aplikací pro individuální ověřování. TOTP (2FA) ověřovací aplikace pro ověřování pomocí času založeného na čase () jsou doporučeným oborem přístupu pro 2FA. 2FA pomocí TOTP se upřednostňuje pro SMS 2FA. Ověřovací aplikace poskytuje kód o 6 až 8 číslic, který musí uživatelé zadat po potvrzení uživatelského jména a hesla. Obvykle je aplikace ověřovatele nainstalovaná na inteligentním telefonu.

Šablony webové aplikace ASP.NET Core podporují ověřovací moduly, ale neposkytují podporu pro generování QRCode. QRCode generátory usnadňují nastavení 2FA. Tento dokument vás provede přidáním generování [kódu QR](https://wikipedia.org/wiki/QR_code) do konfigurační stránky 2FA.

K dvojúrovňovému ověřování nedochází pomocí externího poskytovatele ověřování, jako je [Google](xref:security/authentication/google-logins) nebo [Facebook](xref:security/authentication/facebook-logins). Externí přihlášení jsou chráněná jakýmkoli mechanismem, který poskytuje externí poskytovatel přihlášení. Předpokládejme například, že zprostředkovatel ověřování od [společnosti Microsoft](xref:security/authentication/microsoft-logins) vyžaduje hardwarový klíč nebo jiný 2FA přístup. Pokud výchozí šablony vynutily "místní" 2FA, pak uživatelé budou muset splnit dva 2FA přístupy, což není běžně používaný scénář.

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a>Přidávání kódů QR do konfigurační stránky 2FA

Tyto pokyny používají *qrcode.js* z https://davidshimjs.github.io/qrcodejs/ úložiště.

* Stáhněte [ knihovnuqrcode.js JavaScript](https://davidshimjs.github.io/qrcodejs/) do `wwwroot\lib` složky v projektu.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* Podle [ Identity pokynů v části](xref:security/authentication/scaffold-identity) generátory generujte */areas/ Identity /Pages/Account/Manage/EnableAuthenticator.cshtml*.
* V */areas/ Identity /Pages/Account/Manage/EnableAuthenticator.cshtml*vyhledejte `Scripts` část na konci souboru:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* Na *stránce stránky/účet/spravovat/EnableAuthenticator. cshtml* ( Razor stránky) nebo *zobrazení/Správa/EnableAuthenticator. cshtml* (MVC) vyhledejte `Scripts` část na konci souboru:

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* Aktualizujte `Scripts` oddíl a přidejte odkaz na `qrcodejs` knihovnu, kterou jste přidali, a volání vygenerujte kód QR. Měl by vypadat takto:

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* Odstraňte odstavec s odkazem na tyto pokyny.

Spusťte aplikaci a ujistěte se, že je možné kontrolovat kód QR a ověřit kód, který ověřovatel prokáže.

## <a name="change-the-site-name-in-the-qr-code"></a>Změna názvu lokality v kódu QR

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Název lokality v kódu QR je pořízen z názvu projektu, který jste zvolili při počátečním vytváření projektu. Můžete ji změnit hledáním `GenerateQrCodeUri(string email, string unformattedKey)` metody v */areas/ Identity /Pages/Account/Manage/EnableAuthenticator.cshtml.cs*.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Název lokality v kódu QR je pořízen z názvu projektu, který jste zvolili při počátečním vytváření projektu. Můžete ji změnit hledáním `GenerateQrCodeUri(string email, string unformattedKey)` metody v souboru *Pages/Account/Manage/EnableAuthenticator. cshtml. cs* ( Razor Pages) nebo *Controller/ManageController. cs* (MVC).

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

Výchozí kód ze šablony vypadá takto:

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

Druhý parametr ve volání `string.Format` je název vašeho webu, který je pořízen z názvu vašeho řešení. Dá se změnit na libovolnou hodnotu, ale musí být vždycky zakódovaný URL.

## <a name="using-a-different-qr-code-library"></a>Použití jiné knihovny kódu QR

Knihovnu kódu QR můžete nahradit preferovanou knihovnou. Kód jazyka HTML obsahuje `qrCode` element, do kterého můžete umístit kód QR jakýmkoli mechanismem, který vaše knihovna poskytuje.

Správně formátovaná adresa URL pro kód QR je k dispozici v:

* `AuthenticatorUri` vlastnost modelu
* `data-url` vlastnost v `qrCodeData` elementu.

## <a name="totp-client-and-server-time-skew"></a>TOTP a časový posun serveru

Ověřování TOTP (jednorázové heslo založené na čase) závisí na serveru i na ověřovacím zařízení s přesným časem. Tokeny jsou pouze poslední po dobu 30 sekund. Pokud se nedaří přihlášení TOTP 2FA, ověřte, že je čas serveru přesný a nejlépe se synchronizuje s přesnou službou NTP.

::: moniker-end
