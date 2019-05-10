---
title: Povolit generování kódu QR pro TOTP aplikace v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak povolit generování kódu QR pro aplikace TOTP, které pracují s ASP.NET Core dvojúrovňového ověřování.
ms.author: riande
ms.date: 08/14/2018
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: a7fdc86b3fe94e714e5147c89a32fce13757d1c1
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902712"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a>Povolit generování kódu QR pro TOTP aplikace v ASP.NET Core

::: moniker range="<= aspnetcore-2.0"

Kódy QR vyžaduje ASP.NET Core 2.0 nebo novější.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

ASP.NET Core se dodává s podporou pro aplikace authenticator pro jednotlivé ověřování. Dva faktoru ověřování (2FA), pomocí časovou synchronizací jednorázové heslo algoritmus (TOTP), jsou tyto aplikace v oboru doporučenému přístupu pro 2FA. 2FA pomocí TOTP je upřednostňována před SMS 2FA. Aplikace authenticator poskytuje 6 až 8 číselným kódem, který uživatelé musí zadat po potvrzení uživatelského jména a hesla. Ověřovací aplikace se obvykle instaluje na smartphonu.

Šablony ASP.NET Core webové aplikace podporovat ověřovací data, ale neposkytuje podporu pro generování QRCode. Generátory QRCode usnadnění instalace 2FA. Tento dokument vás provede přidáním [kód QR](https://wikipedia.org/wiki/QR_code) generování ke konfigurační stránce 2FA.

Dvoufaktorové ověřování neproběhne pomocí zprostředkovatele externího ověřování, například [Google](xref:security/authentication/google-logins) nebo [Facebook](xref:security/authentication/facebook-logins). Externí přihlášení jsou chráněny libovolné mechanismem poskytuje na externího zprostředkovatele přihlášení. Zvažte například [Microsoft](xref:security/authentication/microsoft-logins) zprostředkovatele ověřování vyžaduje klíč hardware nebo další 2FA přístup. Pokud výchozí šablony vynucují "local" 2FA by uživatelé vyžadovány k uspokojení dva přístupy 2FA, to není často používaný scénář.

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a>Přidání na stránku konfigurace 2FA kódy QR

Tyto pokyny používají *qrcode.js* z https://davidshimjs.github.io/qrcodejs/ úložiště.

* Stáhněte si [knihovny javascript qrcode.js](https://davidshimjs.github.io/qrcodejs/) k `wwwroot\lib` složku ve vašem projektu.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* Postupujte podle pokynů v [vygenerované uživatelské rozhraní Identity](xref:security/authentication/scaffold-identity) ke generování */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.
* V */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, vyhledejte `Scripts` části na konci souboru:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* V *Pages/Account/Manage/EnableAuthenticator.cshtml* (stránky Razor) nebo *Views/Manage/EnableAuthenticator.cshtml* (MVC), vyhledejte `Scripts` části na konci souboru:

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* Aktualizace `Scripts` části a přidejte odkaz na `qrcodejs` knihovny, které jste přidali a volání pro generování kódu QR. Měl by vypadat takto:

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

* Odstranění odstavce, který slouží k propojení k těmto pokynům.

Spusťte aplikaci a ujistěte se, že můžete naskenovat kód QR a ověřit kód, který prokáže, ověřovací data.

## <a name="change-the-site-name-in-the-qr-code"></a>Změna názvu serveru v kódu QR

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Název webu v kód QR je převzat z názvu projektu, který si zvolíte při prvotním vytvoření projektu. Můžete ji změnit tím, že hledají `GenerateQrCodeUri(string email, string unformattedKey)` metodu */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Název webu v kód QR je převzat z názvu projektu, který si zvolíte při prvotním vytvoření projektu. Můžete ho změnit tím, že hledají `GenerateQrCodeUri(string email, string unformattedKey)` metoda v *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (stránky Razor) souboru nebo *Controllers/ManageController.cs* souboru (MVC).

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

Výchozí kód ze šablony by měl vypadat takto:

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

Druhý parametr ve volání `string.Format` je název vašeho webu, na základě název řešení. Lze jej změnit na libovolnou hodnotu, ale musí být vždy kódování URL.

## <a name="using-a-different-qr-code-library"></a>Použití knihovny jiný kód QR

Knihovny kódu QR můžete nahradit své upřednostňované knihovny. Obsahuje kód HTML `qrCode` elementu, do kterého můžete umístit kód QR libovolné mechanismem vaše knihovna poskytuje.

Správně formátované adresy URL pro kód QR je k dispozici v:

* `AuthenticatorUri` Vlastnost modelu.
* `data-url` Vlastnost `qrCodeData` elementu.

## <a name="totp-client-and-server-time-skew"></a>TOTP klientských a serverových nerovnoměrné rozdělení času

Ověření TOTP (podle času jednorázového hesla) závisí na serveru a ověřovací zařízení s přesnému času. Tokeny pouze posledních 30 sekund. Pokud se nedaří přihlášení TOTP 2FA, zkontrolujte, zda čas serveru přesné a pokud možno synchronizované do služby přesné NTP.

::: moniker-end
