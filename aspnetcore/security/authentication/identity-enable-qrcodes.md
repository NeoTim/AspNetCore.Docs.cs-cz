---
title: Povolit generování kódu QR pro TOTP aplikace v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak povolit generování kódu QR pro aplikace TOTP, které pracují s ASP.NET Core dvojúrovňového ověřování.
ms.author: riande
ms.date: 08/14/2018
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: 437f354f71128a98bae9abdced291e04efc9f48e
ms.sourcegitcommit: fc7eb4243188950ae1f1b52669edc007e9d0798d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51225379"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="f74fd-103">Povolit generování kódu QR pro TOTP aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f74fd-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="f74fd-104">Kódy QR vyžaduje ASP.NET Core 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="f74fd-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f74fd-105">ASP.NET Core se dodává s podporou pro aplikace authenticator pro jednotlivé ověřování.</span><span class="sxs-lookup"><span data-stu-id="f74fd-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="f74fd-106">Dva faktoru ověřování (2FA), pomocí časovou synchronizací jednorázové heslo algoritmus (TOTP), jsou tyto aplikace v oboru doporučenému přístupu pro 2FA.</span><span class="sxs-lookup"><span data-stu-id="f74fd-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="f74fd-107">2FA pomocí TOTP je upřednostňována před SMS 2FA.</span><span class="sxs-lookup"><span data-stu-id="f74fd-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="f74fd-108">Aplikace authenticator poskytuje 6 až 8 číselným kódem, který uživatelé musí zadat po potvrzení uživatelského jména a hesla.</span><span class="sxs-lookup"><span data-stu-id="f74fd-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="f74fd-109">Ověřovací aplikace se obvykle instaluje na smartphonu.</span><span class="sxs-lookup"><span data-stu-id="f74fd-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="f74fd-110">Šablony ASP.NET Core webové aplikace podporovat ověřovací data, ale neposkytuje podporu pro generování QRCode.</span><span class="sxs-lookup"><span data-stu-id="f74fd-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="f74fd-111">Generátory QRCode usnadnění instalace 2FA.</span><span class="sxs-lookup"><span data-stu-id="f74fd-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="f74fd-112">Tento dokument vás provede přidáním [kód QR](https://wikipedia.org/wiki/QR_code) generování ke konfigurační stránce 2FA.</span><span class="sxs-lookup"><span data-stu-id="f74fd-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="f74fd-113">Přidání na stránku konfigurace 2FA kódy QR</span><span class="sxs-lookup"><span data-stu-id="f74fd-113">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="f74fd-114">Tyto pokyny používají *qrcode.js* z https://davidshimjs.github.io/qrcodejs/ úložiště.</span><span class="sxs-lookup"><span data-stu-id="f74fd-114">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="f74fd-115">Stáhněte si [knihovny javascript qrcode.js](https://davidshimjs.github.io/qrcodejs/) k `wwwroot\lib` složku ve vašem projektu.</span><span class="sxs-lookup"><span data-stu-id="f74fd-115">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="f74fd-116">Postupujte podle pokynů v [vygenerované uživatelské rozhraní Identity](xref:security/authentication/scaffold-identity) ke generování */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f74fd-116">Follow the instructions in [Scaffold Identity](xref:security/authentication/scaffold-identity) to generate */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>
* <span data-ttu-id="f74fd-117">V */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, vyhledejte `Scripts` části na konci souboru:</span><span class="sxs-lookup"><span data-stu-id="f74fd-117">In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="f74fd-118">V *Pages/Account/Manage/EnableAuthenticator.cshtml* (stránky Razor) nebo *Views/Manage/EnableAuthenticator.cshtml* (MVC), vyhledejte `Scripts` části na konci souboru:</span><span class="sxs-lookup"><span data-stu-id="f74fd-118">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="f74fd-119">Aktualizace `Scripts` části a přidejte odkaz na `qrcodejs` knihovny, které jste přidali a volání pro generování kódu QR.</span><span class="sxs-lookup"><span data-stu-id="f74fd-119">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="f74fd-120">Měl by vypadat takto:</span><span class="sxs-lookup"><span data-stu-id="f74fd-120">It should look as follows:</span></span>

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

* <span data-ttu-id="f74fd-121">Odstranění odstavce, který slouží k propojení k těmto pokynům.</span><span class="sxs-lookup"><span data-stu-id="f74fd-121">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="f74fd-122">Spusťte aplikaci a ujistěte se, že můžete naskenovat kód QR a ověřit kód, který prokáže, ověřovací data.</span><span class="sxs-lookup"><span data-stu-id="f74fd-122">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="f74fd-123">Změna názvu serveru v kódu QR</span><span class="sxs-lookup"><span data-stu-id="f74fd-123">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f74fd-124">Název webu v kód QR je převzat z názvu projektu, který si zvolíte při prvotním vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="f74fd-124">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="f74fd-125">Můžete ji změnit tím, že hledají `GenerateQrCodeUri(string email, string unformattedKey)` metodu */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f74fd-125">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="f74fd-126">Název webu v kód QR je převzat z názvu projektu, který si zvolíte při prvotním vytvoření projektu.</span><span class="sxs-lookup"><span data-stu-id="f74fd-126">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="f74fd-127">Můžete ho změnit tím, že hledají `GenerateQrCodeUri(string email, string unformattedKey)` metoda v *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (stránky Razor) souboru nebo *Controllers/ManageController.cs* souboru (MVC).</span><span class="sxs-lookup"><span data-stu-id="f74fd-127">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f74fd-128">Výchozí kód ze šablony by měl vypadat takto:</span><span class="sxs-lookup"><span data-stu-id="f74fd-128">The default code from the template looks as follows:</span></span>

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenicatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="f74fd-129">Druhý parametr ve volání `string.Format` je název vašeho webu, na základě název řešení.</span><span class="sxs-lookup"><span data-stu-id="f74fd-129">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="f74fd-130">Lze jej změnit na libovolnou hodnotu, ale musí být vždy kódování URL.</span><span class="sxs-lookup"><span data-stu-id="f74fd-130">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="f74fd-131">Použití knihovny jiný kód QR</span><span class="sxs-lookup"><span data-stu-id="f74fd-131">Using a different QR Code library</span></span>

<span data-ttu-id="f74fd-132">Knihovny kódu QR můžete nahradit své upřednostňované knihovny.</span><span class="sxs-lookup"><span data-stu-id="f74fd-132">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="f74fd-133">Obsahuje kód HTML `qrCode` elementu, do kterého můžete umístit kód QR libovolné mechanismem vaše knihovna poskytuje.</span><span class="sxs-lookup"><span data-stu-id="f74fd-133">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="f74fd-134">Správně formátované adresy URL pro kód QR je k dispozici v:</span><span class="sxs-lookup"><span data-stu-id="f74fd-134">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="f74fd-135">`AuthenticatorUri` Vlastnost modelu.</span><span class="sxs-lookup"><span data-stu-id="f74fd-135">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="f74fd-136">`data-url` Vlastnost `qrCodeData` elementu.</span><span class="sxs-lookup"><span data-stu-id="f74fd-136">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="f74fd-137">TOTP klientských a serverových nerovnoměrné rozdělení času</span><span class="sxs-lookup"><span data-stu-id="f74fd-137">TOTP client and server time skew</span></span>

<span data-ttu-id="f74fd-138">Ověření TOTP (podle času jednorázového hesla) závisí na serveru a ověřovací zařízení s přesnému času.</span><span class="sxs-lookup"><span data-stu-id="f74fd-138">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="f74fd-139">Tokeny pouze posledních 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="f74fd-139">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="f74fd-140">Pokud se nedaří přihlášení TOTP 2FA, zkontrolujte, zda čas serveru přesné a pokud možno synchronizované do služby přesné NTP.</span><span class="sxs-lookup"><span data-stu-id="f74fd-140">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end
