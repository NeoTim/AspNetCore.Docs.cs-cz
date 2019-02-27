---
title: Ověřování uživatelů pomocí WS-Federation v ASP.NET Core
author: chlowell
description: Tento kurz ukazuje, jak použít v aplikaci ASP.NET Core WS-Federation.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
uid: security/authentication/ws-federation
ms.openlocfilehash: 7967410686da0e59742b721c0154e143bf19ba01
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833537"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="d8243-103">Ověřování uživatelů pomocí WS-Federation v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8243-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="d8243-104">Tento kurz ukazuje, jak povolit uživatelům přihlašovat se pomocí zprostředkovatele ověřování WS-Federation, jako je Active Directory Federation Services (ADFS) nebo [Azure Active Directory](/azure/active-directory/) (AAD).</span><span class="sxs-lookup"><span data-stu-id="d8243-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="d8243-105">Používá ukázkovou aplikaci ASP.NET Core 2.0 je popsáno v [Facebook, Google a externí zprostředkovatel ověřování](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d8243-105">It uses the ASP.NET Core 2.0 sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="d8243-106">Pro aplikace ASP.NET Core 2.0, WS-Federation podporu poskytuje [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span><span class="sxs-lookup"><span data-stu-id="d8243-106">For ASP.NET Core 2.0 apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="d8243-107">Tato součást se přenáší z [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) a celá řada mechanics tuto součást.</span><span class="sxs-lookup"><span data-stu-id="d8243-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="d8243-108">Komponenty se však liší v několika důležitých směrech –.</span><span class="sxs-lookup"><span data-stu-id="d8243-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="d8243-109">Ve výchozím nastavení nový middleware:</span><span class="sxs-lookup"><span data-stu-id="d8243-109">By default, the new middleware:</span></span>

* <span data-ttu-id="d8243-110">Neumožňuje nevyžádané přihlášení.</span><span class="sxs-lookup"><span data-stu-id="d8243-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="d8243-111">Tato funkce protokol WS-Federation je ohrožen útoky XSRF.</span><span class="sxs-lookup"><span data-stu-id="d8243-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="d8243-112">Ale lze je aktivovat pomocí `AllowUnsolicitedLogins` možnost.</span><span class="sxs-lookup"><span data-stu-id="d8243-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="d8243-113">Neprovádí kontrolu každých odeslaného formuláře pro přihlašování zprávy.</span><span class="sxs-lookup"><span data-stu-id="d8243-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="d8243-114">Jenom požadavky na `CallbackPath` kontroluje sign in `CallbackPath` výchozí hodnota je `/signin-wsfed` lze ji však změnit prostřednictvím zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [ WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) třídy.</span><span class="sxs-lookup"><span data-stu-id="d8243-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="d8243-115">Tato cesta je sdílet s další zprostředkovatelé ověřování tím, že [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) možnost.</span><span class="sxs-lookup"><span data-stu-id="d8243-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="d8243-116">Registrace aplikace v Active Directory</span><span class="sxs-lookup"><span data-stu-id="d8243-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="d8243-117">Active Directory Federation Services</span><span class="sxs-lookup"><span data-stu-id="d8243-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="d8243-118">Otevřete serveru **předávající strana Průvodce přidáním vztahu důvěryhodnosti** z konzoly pro správu služby AD FS:</span><span class="sxs-lookup"><span data-stu-id="d8243-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![Přidáte průvodce předávající strany vztahu důvěryhodnosti: Vítej](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="d8243-120">Zvolte zadat údaje ručně:</span><span class="sxs-lookup"><span data-stu-id="d8243-120">Choose to enter data manually:</span></span>

![Přidáte průvodce předávající strany vztahu důvěryhodnosti: Vyberte zdroj dat](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="d8243-122">Zadejte zobrazovaný název pro předávající stranu.</span><span class="sxs-lookup"><span data-stu-id="d8243-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="d8243-123">Název není důležité, abyste aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8243-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="d8243-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) schází podpora šifrování tokenů, takže nekonfigurujte certifikát šifrování tokenů:</span><span class="sxs-lookup"><span data-stu-id="d8243-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![Přidáte průvodce předávající strany vztahu důvěryhodnosti: Konfigurace certifikátu](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="d8243-126">Povolte podporu protokolu WS-Federation Passive, pomocí adresy URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8243-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="d8243-127">Ověřte, že port je správný pro aplikaci:</span><span class="sxs-lookup"><span data-stu-id="d8243-127">Verify the port is correct for the app:</span></span>

![Přidáte průvodce předávající strany vztahu důvěryhodnosti: Konfigurovat adresy URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="d8243-129">Musí se jednat adresu URL HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d8243-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="d8243-130">Služba IIS Express můžete zadat certifikát podepsaný svým držitelem, při hostování aplikace během vývoje.</span><span class="sxs-lookup"><span data-stu-id="d8243-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="d8243-131">Kestrel vyžaduje certifikáty ručně konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="d8243-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="d8243-132">Najdete v článku [Kestrel dokumentaci](xref:fundamentals/servers/kestrel) další podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="d8243-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="d8243-133">Klikněte na tlačítko **Další** postupujte podle zbývajících kroků průvodce a **Zavřít** na konci.</span><span class="sxs-lookup"><span data-stu-id="d8243-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="d8243-134">ASP.NET Core Identity vyžaduje **ID názvu** deklarací identity.</span><span class="sxs-lookup"><span data-stu-id="d8243-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="d8243-135">Přidejte jeden z **upravit pravidla deklarací identity** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="d8243-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![Upravit pravidla deklarace identity](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="d8243-137">V **průvodci Přidat transformované deklarace identity pravidlo**, ponechte výchozí nastavení **odesílat atributy LDAP jako deklarace identity** zvolenou šablonu a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="d8243-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="d8243-138">Přidat pravidlo mapování **název účtu SAM** atributu LDAP **ID názvu** odchozí deklarace identity:</span><span class="sxs-lookup"><span data-stu-id="d8243-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![Přidáte Průvodce vytvořením pravidla transformace deklarací identity: Konfigurace pravidla deklarace identity](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="d8243-140">Klikněte na tlačítko **Dokončit** > **OK** v **upravit pravidla deklarací identity** okna.</span><span class="sxs-lookup"><span data-stu-id="d8243-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="d8243-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="d8243-141">Azure Active Directory</span></span>

* <span data-ttu-id="d8243-142">Přejděte do okna registrace klienta AAD aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8243-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="d8243-143">Klikněte na tlačítko **registrace nové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="d8243-143">Click **New application registration**:</span></span>

![Azure Active Directory: Registrace aplikací](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="d8243-145">Zadejte název pro registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8243-145">Enter a name for the app registration.</span></span> <span data-ttu-id="d8243-146">To není důležité, abyste aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d8243-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="d8243-147">Zadejte adresu URL aplikace naslouchá na jako **přihlašovací adresa URL**:</span><span class="sxs-lookup"><span data-stu-id="d8243-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory: Vytvoření registrace aplikace](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="d8243-149">Klikněte na tlačítko **koncové body** a poznamenejte si **dokument metadat federace** adresy URL.</span><span class="sxs-lookup"><span data-stu-id="d8243-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="d8243-150">Toto je WS-Federation middleware `MetadataAddress`:</span><span class="sxs-lookup"><span data-stu-id="d8243-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: Koncové body](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="d8243-152">Přejděte do registrace nové aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8243-152">Navigate to the new app registration.</span></span> <span data-ttu-id="d8243-153">Klikněte na tlačítko **nastavení** > **vlastnosti** a poznamenejte si **identifikátor ID URI aplikace**.</span><span class="sxs-lookup"><span data-stu-id="d8243-153">Click **Settings** > **Properties** and make note of the **App ID URI**.</span></span> <span data-ttu-id="d8243-154">Toto je WS-Federation middleware `Wtrealm`:</span><span class="sxs-lookup"><span data-stu-id="d8243-154">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: Vlastnosti registrace aplikace](ws-federation/_static/AadAppIdUri.png)

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a><span data-ttu-id="d8243-156">Přidat WS-Federation jako externího zprostředkovatele přihlášení pro ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="d8243-156">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="d8243-157">Přidat závislost na [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) do projektu.</span><span class="sxs-lookup"><span data-stu-id="d8243-157">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="d8243-158">Přidat WS-Federation a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d8243-158">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

    ```csharp
    services.AddIdentity<IdentityUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddAuthentication()
        .AddWsFederation(options =>
        {
            // MetadataAddress represents the Active Directory instance used to authenticate users.
            options.MetadataAddress = "https://<ADFS FQDN or AAD tenant>/FederationMetadata/2007-06/FederationMetadata.xml";

            // Wtrealm is the app's identifier in the Active Directory instance.
            // For ADFS, use the relying party's identifier, its WS-Federation Passive protocol URL:
            options.Wtrealm = "https://localhost:44307/";

            // For AAD, use the App ID URI from the app registration's Properties blade:
            options.Wtrealm = "https://wsfedsample.onmicrosoft.com/bf0e7e6d-056e-4e37-b9a6-2c36797b9f01";
        });

    services.AddMvc()
     // ...
    ```

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="d8243-159">Přihlaste se pomocí WS-Federation</span><span class="sxs-lookup"><span data-stu-id="d8243-159">Log in with WS-Federation</span></span>

<span data-ttu-id="d8243-160">Přejděte na aplikace a kliknutím **přihlášení** odkaz v záhlaví navigace.</span><span class="sxs-lookup"><span data-stu-id="d8243-160">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="d8243-161">Nabízí možnost přihlásit se prostřednictvím WsFederation: ![S přihlašovací stránkou](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="d8243-161">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="d8243-162">S využitím AD FS jako zprostředkovatel tlačítko přesměruje na přihlašovací stránku služby AD FS: ![Přihlašovací stránku služby AD FS](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="d8243-162">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="d8243-163">S Azure Active Directory jako zprostředkovatele tlačítko přesměruje na přihlašovací stránku AAD: ![AAD přihlašovací stránky](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="d8243-163">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="d8243-164">U úspěšné přihlášení pro nového uživatele přesměruje na stránce registrace aplikace uživatele: ![Stránka pro registraci](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="d8243-164">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>

## <a name="use-ws-federation-without-aspnet-core-identity"></a><span data-ttu-id="d8243-165">Použití WS-Federation bez ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="d8243-165">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="d8243-166">Middleware WS-Federation, je možné bez Identity.</span><span class="sxs-lookup"><span data-stu-id="d8243-166">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="d8243-167">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d8243-167">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultSignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = WsFederationDefaults.AuthenticationScheme;
    })
    .AddWsFederation(options =>
    {
        options.Wtrealm = Configuration["wsfed:realm"];
        options.MetadataAddress = Configuration["wsfed:metadata"];
    })
    .AddCookie();
}

public void Configure(IApplicationBuilder app)
{
    app.UseAuthentication();
        // …
}
```
