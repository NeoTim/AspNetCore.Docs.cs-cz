---
title: Ověřování uživatelů pomocí WS-Federation v ASP.NET Core
author: chlowell
description: Tento kurz ukazuje, jak použít v aplikaci ASP.NET Core WS-Federation.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
uid: security/authentication/ws-federation
ms.openlocfilehash: 7967410686da0e59742b721c0154e143bf19ba01
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64903087"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>Ověřování uživatelů pomocí WS-Federation v ASP.NET Core

Tento kurz ukazuje, jak povolit uživatelům přihlašovat se pomocí zprostředkovatele ověřování WS-Federation, jako je Active Directory Federation Services (ADFS) nebo [Azure Active Directory](/azure/active-directory/) (AAD). Používá ukázkovou aplikaci ASP.NET Core 2.0 je popsáno v [Facebook, Google a externí zprostředkovatel ověřování](xref:security/authentication/social/index).

Pro aplikace ASP.NET Core 2.0, WS-Federation podporu poskytuje [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation). Tato součást se přenáší z [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) a celá řada mechanics tuto součást. Komponenty se však liší v několika důležitých směrech –.

Ve výchozím nastavení nový middleware:

* Neumožňuje nevyžádané přihlášení. Tato funkce protokol WS-Federation je ohrožen útoky XSRF. Ale lze je aktivovat pomocí `AllowUnsolicitedLogins` možnost.
* Neprovádí kontrolu každých odeslaného formuláře pro přihlašování zprávy. Jenom požadavky na `CallbackPath` kontroluje sign in `CallbackPath` výchozí hodnota je `/signin-wsfed` lze ji však změnit prostřednictvím zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [ WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) třídy. Tato cesta je sdílet s další zprostředkovatelé ověřování tím, že [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) možnost.

## <a name="register-the-app-with-active-directory"></a>Registrace aplikace v Active Directory

### <a name="active-directory-federation-services"></a>Active Directory Federation Services

* Otevřete serveru **předávající strana Průvodce přidáním vztahu důvěryhodnosti** z konzoly pro správu služby AD FS:

![Přidáte průvodce předávající strany vztahu důvěryhodnosti: Vítej](ws-federation/_static/AdfsAddTrust.png)

* Zvolte zadat údaje ručně:

![Přidáte průvodce předávající strany vztahu důvěryhodnosti: Vyberte zdroj dat](ws-federation/_static/AdfsSelectDataSource.png)

* Zadejte zobrazovaný název pro předávající stranu. Název není důležité, abyste aplikace ASP.NET Core.

* [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) schází podpora šifrování tokenů, takže nekonfigurujte certifikát šifrování tokenů:

![Přidáte průvodce předávající strany vztahu důvěryhodnosti: Konfigurace certifikátu](ws-federation/_static/AdfsConfigureCert.png)

* Povolte podporu protokolu WS-Federation Passive, pomocí adresy URL aplikace. Ověřte, že port je správný pro aplikaci:

![Přidáte průvodce předávající strany vztahu důvěryhodnosti: Konfigurovat adresy URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> Musí se jednat adresu URL HTTPS. Služba IIS Express můžete zadat certifikát podepsaný svým držitelem, při hostování aplikace během vývoje. Kestrel vyžaduje certifikáty ručně konfiguraci. Najdete v článku [Kestrel dokumentaci](xref:fundamentals/servers/kestrel) další podrobnosti.

* Klikněte na tlačítko **Další** postupujte podle zbývajících kroků průvodce a **Zavřít** na konci.

* ASP.NET Core Identity vyžaduje **ID názvu** deklarací identity. Přidejte jeden z **upravit pravidla deklarací identity** dialogové okno:

![Upravit pravidla deklarace identity](ws-federation/_static/EditClaimRules.png)

* V **průvodci Přidat transformované deklarace identity pravidlo**, ponechte výchozí nastavení **odesílat atributy LDAP jako deklarace identity** zvolenou šablonu a klikněte na tlačítko **Další**. Přidat pravidlo mapování **název účtu SAM** atributu LDAP **ID názvu** odchozí deklarace identity:

![Přidáte Průvodce vytvořením pravidla transformace deklarací identity: Konfigurace pravidla deklarace identity](ws-federation/_static/AddTransformClaimRule.png)

* Klikněte na tlačítko **Dokončit** > **OK** v **upravit pravidla deklarací identity** okna.

### <a name="azure-active-directory"></a>Azure Active Directory

* Přejděte do okna registrace klienta AAD aplikace. Klikněte na tlačítko **registrace nové aplikace**:

![Azure Active Directory: Registrace aplikací](ws-federation/_static/AadNewAppRegistration.png)

* Zadejte název pro registraci aplikace. To není důležité, abyste aplikace ASP.NET Core.
* Zadejte adresu URL aplikace naslouchá na jako **přihlašovací adresa URL**:

![Azure Active Directory: Vytvoření registrace aplikace](ws-federation/_static/AadCreateAppRegistration.png)

* Klikněte na tlačítko **koncové body** a poznamenejte si **dokument metadat federace** adresy URL. Toto je WS-Federation middleware `MetadataAddress`:

![Azure Active Directory: Koncové body](ws-federation/_static/AadFederationMetadataDocument.png)

* Přejděte do registrace nové aplikace. Klikněte na tlačítko **nastavení** > **vlastnosti** a poznamenejte si **identifikátor ID URI aplikace**. Toto je WS-Federation middleware `Wtrealm`:

![Azure Active Directory: Vlastnosti registrace aplikace](ws-federation/_static/AadAppIdUri.png)

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a>Přidat WS-Federation jako externího zprostředkovatele přihlášení pro ASP.NET Core Identity

* Přidat závislost na [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) do projektu.
* Přidat WS-Federation a `Startup.ConfigureServices`:

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

### <a name="log-in-with-ws-federation"></a>Přihlaste se pomocí WS-Federation

Přejděte na aplikace a kliknutím **přihlášení** odkaz v záhlaví navigace. Nabízí možnost přihlásit se prostřednictvím WsFederation: ![S přihlašovací stránkou](ws-federation/_static/WsFederationButton.png)

S využitím AD FS jako zprostředkovatel tlačítko přesměruje na přihlašovací stránku služby AD FS: ![Přihlašovací stránku služby AD FS](ws-federation/_static/AdfsLoginPage.png)

S Azure Active Directory jako zprostředkovatele tlačítko přesměruje na přihlašovací stránku AAD: ![AAD přihlašovací stránky](ws-federation/_static/AadSignIn.png)

U úspěšné přihlášení pro nového uživatele přesměruje na stránce registrace aplikace uživatele: ![Stránka pro registraci](ws-federation/_static/Register.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a>Použití WS-Federation bez ASP.NET Core Identity

Middleware WS-Federation, je možné bez Identity. Příklad:

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
