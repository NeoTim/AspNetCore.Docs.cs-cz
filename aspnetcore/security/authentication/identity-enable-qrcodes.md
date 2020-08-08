---
title: Povolit generování kódu QR pro aplikace TOTP Authenticator v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak povolit generování kódu QR pro aplikace TOTP Authenticator, které fungují s ASP.NET Core dvojúrovňové ověřování.
ms.author: riande
ms.date: 08/14/2018
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
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: 4ccfd83c273f7179ac26b075eb33f138e724b967
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019557"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="89515-103">Povolit generování kódu QR pro aplikace TOTP Authenticator v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="89515-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="89515-104">Kódy QR vyžadují ASP.NET Core 2,0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="89515-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="89515-105">ASP.NET Core lodí s podporou ověřovacích aplikací pro individuální ověřování.</span><span class="sxs-lookup"><span data-stu-id="89515-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="89515-106">TOTP (2FA) ověřovací aplikace pro ověřování pomocí času založeného na čase () jsou doporučeným oborem přístupu pro 2FA.</span><span class="sxs-lookup"><span data-stu-id="89515-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="89515-107">2FA pomocí TOTP se upřednostňuje pro SMS 2FA.</span><span class="sxs-lookup"><span data-stu-id="89515-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="89515-108">Ověřovací aplikace poskytuje kód o 6 až 8 číslic, který musí uživatelé zadat po potvrzení uživatelského jména a hesla.</span><span class="sxs-lookup"><span data-stu-id="89515-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="89515-109">Obvykle je aplikace ověřovatele nainstalovaná na inteligentním telefonu.</span><span class="sxs-lookup"><span data-stu-id="89515-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="89515-110">Šablony webové aplikace ASP.NET Core podporují ověřovací moduly, ale neposkytují podporu pro generování QRCode.</span><span class="sxs-lookup"><span data-stu-id="89515-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="89515-111">QRCode generátory usnadňují nastavení 2FA.</span><span class="sxs-lookup"><span data-stu-id="89515-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="89515-112">Tento dokument vás provede přidáním generování [kódu QR](https://wikipedia.org/wiki/QR_code) do konfigurační stránky 2FA.</span><span class="sxs-lookup"><span data-stu-id="89515-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

<span data-ttu-id="89515-113">K dvojúrovňovému ověřování nedochází pomocí externího poskytovatele ověřování, jako je [Google](xref:security/authentication/google-logins) nebo [Facebook](xref:security/authentication/facebook-logins).</span><span class="sxs-lookup"><span data-stu-id="89515-113">Two factor authentication does not happen using an external authentication provider, such as [Google](xref:security/authentication/google-logins) or [Facebook](xref:security/authentication/facebook-logins).</span></span> <span data-ttu-id="89515-114">Externí přihlášení jsou chráněná jakýmkoli mechanismem, který poskytuje externí poskytovatel přihlášení.</span><span class="sxs-lookup"><span data-stu-id="89515-114">External logins are protected by whatever mechanism the external login provider provides.</span></span> <span data-ttu-id="89515-115">Předpokládejme například, že zprostředkovatel ověřování od [společnosti Microsoft](xref:security/authentication/microsoft-logins) vyžaduje hardwarový klíč nebo jiný 2FA přístup.</span><span class="sxs-lookup"><span data-stu-id="89515-115">Consider, for example, the [Microsoft](xref:security/authentication/microsoft-logins) authentication provider requires a hardware key or another 2FA approach.</span></span> <span data-ttu-id="89515-116">Pokud výchozí šablony vynutily "místní" 2FA, pak uživatelé budou muset splnit dva 2FA přístupy, což není běžně používaný scénář.</span><span class="sxs-lookup"><span data-stu-id="89515-116">If the default templates enforced "local" 2FA then users would be required to satisfy two 2FA approaches, which is not a commonly used scenario.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="89515-117">Přidávání kódů QR do konfigurační stránky 2FA</span><span class="sxs-lookup"><span data-stu-id="89515-117">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="89515-118">Tyto pokyny používají *qrcode.js* z https://davidshimjs.github.io/qrcodejs/ úložiště.</span><span class="sxs-lookup"><span data-stu-id="89515-118">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="89515-119">Stáhněte [knihovnuqrcode.js JavaScript](https://davidshimjs.github.io/qrcodejs/) do `wwwroot\lib` složky v projektu.</span><span class="sxs-lookup"><span data-stu-id="89515-119">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="89515-120">Podle [ Identity pokynů v části](xref:security/authentication/scaffold-identity) generátory generujte */areas/ Identity /Pages/Account/Manage/EnableAuthenticator.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="89515-120">Follow the instructions in [Scaffold Identity](xref:security/authentication/scaffold-identity) to generate */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>
* <span data-ttu-id="89515-121">V */areas/ Identity /Pages/Account/Manage/EnableAuthenticator.cshtml*vyhledejte `Scripts` část na konci souboru:</span><span class="sxs-lookup"><span data-stu-id="89515-121">In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="89515-122">Na *stránce stránky/účet/spravovat/EnableAuthenticator. cshtml* ( Razor stránky) nebo *zobrazení/Správa/EnableAuthenticator. cshtml* (MVC) vyhledejte `Scripts` část na konci souboru:</span><span class="sxs-lookup"><span data-stu-id="89515-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="89515-123">Aktualizujte `Scripts` oddíl a přidejte odkaz na `qrcodejs` knihovnu, kterou jste přidali, a volání vygenerujte kód QR.</span><span class="sxs-lookup"><span data-stu-id="89515-123">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="89515-124">Měl by vypadat takto:</span><span class="sxs-lookup"><span data-stu-id="89515-124">It should look as follows:</span></span>

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

* <span data-ttu-id="89515-125">Odstraňte odstavec s odkazem na tyto pokyny.</span><span class="sxs-lookup"><span data-stu-id="89515-125">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="89515-126">Spusťte aplikaci a ujistěte se, že je možné kontrolovat kód QR a ověřit kód, který ověřovatel prokáže.</span><span class="sxs-lookup"><span data-stu-id="89515-126">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="89515-127">Změna názvu lokality v kódu QR</span><span class="sxs-lookup"><span data-stu-id="89515-127">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="89515-128">Název lokality v kódu QR je pořízen z názvu projektu, který jste zvolili při počátečním vytváření projektu.</span><span class="sxs-lookup"><span data-stu-id="89515-128">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="89515-129">Můžete ji změnit hledáním `GenerateQrCodeUri(string email, string unformattedKey)` metody v */areas/ Identity /Pages/Account/Manage/EnableAuthenticator.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="89515-129">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="89515-130">Název lokality v kódu QR je pořízen z názvu projektu, který jste zvolili při počátečním vytváření projektu.</span><span class="sxs-lookup"><span data-stu-id="89515-130">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="89515-131">Můžete ji změnit hledáním `GenerateQrCodeUri(string email, string unformattedKey)` metody v souboru *Pages/Account/Manage/EnableAuthenticator. cshtml. cs* ( Razor Pages) nebo *Controller/ManageController. cs* (MVC).</span><span class="sxs-lookup"><span data-stu-id="89515-131">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="89515-132">Výchozí kód ze šablony vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="89515-132">The default code from the template looks as follows:</span></span>

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

<span data-ttu-id="89515-133">Druhý parametr ve volání `string.Format` je název vašeho webu, který je pořízen z názvu vašeho řešení.</span><span class="sxs-lookup"><span data-stu-id="89515-133">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="89515-134">Dá se změnit na libovolnou hodnotu, ale musí být vždycky zakódovaný URL.</span><span class="sxs-lookup"><span data-stu-id="89515-134">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="89515-135">Použití jiné knihovny kódu QR</span><span class="sxs-lookup"><span data-stu-id="89515-135">Using a different QR Code library</span></span>

<span data-ttu-id="89515-136">Knihovnu kódu QR můžete nahradit preferovanou knihovnou.</span><span class="sxs-lookup"><span data-stu-id="89515-136">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="89515-137">Kód jazyka HTML obsahuje `qrCode` element, do kterého můžete umístit kód QR jakýmkoli mechanismem, který vaše knihovna poskytuje.</span><span class="sxs-lookup"><span data-stu-id="89515-137">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="89515-138">Správně formátovaná adresa URL pro kód QR je k dispozici v:</span><span class="sxs-lookup"><span data-stu-id="89515-138">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="89515-139">`AuthenticatorUri`vlastnost modelu</span><span class="sxs-lookup"><span data-stu-id="89515-139">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="89515-140">`data-url`vlastnost v `qrCodeData` elementu.</span><span class="sxs-lookup"><span data-stu-id="89515-140">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="89515-141">TOTP a časový posun serveru</span><span class="sxs-lookup"><span data-stu-id="89515-141">TOTP client and server time skew</span></span>

<span data-ttu-id="89515-142">Ověřování TOTP (jednorázové heslo založené na čase) závisí na serveru i na ověřovacím zařízení s přesným časem.</span><span class="sxs-lookup"><span data-stu-id="89515-142">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="89515-143">Tokeny jsou pouze poslední po dobu 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="89515-143">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="89515-144">Pokud se nedaří přihlášení TOTP 2FA, ověřte, že je čas serveru přesný a nejlépe se synchronizuje s přesnou službou NTP.</span><span class="sxs-lookup"><span data-stu-id="89515-144">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end
