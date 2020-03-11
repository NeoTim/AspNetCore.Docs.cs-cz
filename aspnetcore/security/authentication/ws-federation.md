---
title: Ověřování uživatelů pomocí WS-Federation v ASP.NET Core
author: chlowell
description: Tento kurz ukazuje, jak používat WS-Federation v aplikaci ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
uid: security/authentication/ws-federation
ms.openlocfilehash: d82421a14ede6cb6b01ef59f233bb2eba6b56aec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655426"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>Ověřování uživatelů pomocí WS-Federation v ASP.NET Core

V tomto kurzu se dozvíte, jak uživatelům povolit, aby se přihlásili pomocí zprostředkovatele ověřování WS-Federation, jako je Active Directory Federation Services (AD FS) (ADFS) nebo [Azure Active Directory](/azure/active-directory/) (AAD). Používá ukázkovou aplikaci ASP.NET Core 2,0 popsanou v [Facebooku, Google a v ověřování externích poskytovatelů](xref:security/authentication/social/index).

Pro aplikace ASP.NET Core 2,0 je podpora protokolu WS-Federation poskytována pomocí [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation). Tato součást je přepravovaná z [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) a sdílí mnoho prvků této součásti. Komponenty se ale liší v několika důležitých způsobech.

Ve výchozím nastavení nový middleware:

* Nepovoluje nevyžádaná přihlášení. Tato funkce protokolu WS-Federation je zranitelná vůči útokům XSRF. Dá se ale povolit s možností `AllowUnsolicitedLogins`.
* Nekontroluje každý příspěvek na formuláři pro zprávy o přihlášení. Pouze požadavky na `CallbackPath` jsou kontrolovány pro přihlášení. `CallbackPath` výchozím nastavením `/signin-wsfed`, ale lze je změnit prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) . Tuto cestu můžete sdílet s dalšími zprostředkovateli ověřování povolením možnosti [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .

## <a name="register-the-app-with-active-directory"></a>Registrace aplikace ve službě Active Directory

### <a name="active-directory-federation-services"></a>Active Directory Federation Services

* V konzole pro správu služby ADFS otevřete **Průvodce přidáním vztahu důvěryhodnosti předávající strany** serveru:

![Průvodce přidáním vztahu důvěryhodnosti předávající strany: Vítejte](ws-federation/_static/AdfsAddTrust.png)

* Vyberte, že chcete zadat data ručně:

![Průvodce přidáním vztahu důvěryhodnosti předávající strany: vybrat zdroj dat](ws-federation/_static/AdfsSelectDataSource.png)

* Zadejte zobrazovaný název předávající strany. Název není pro aplikaci ASP.NET Core důležitý.

* U [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) chybí podpora pro šifrování tokenů, proto nekonfigurujte šifrovací certifikát tokenu:

![Průvodce přidáním vztahu důvěryhodnosti předávající strany: Konfigurace certifikátu](ws-federation/_static/AdfsConfigureCert.png)

* Povolte podporu pasivního protokolu WS-Federation pomocí adresy URL aplikace. Ověřte, jestli je port pro aplikaci správný:

![Průvodce přidáním vztahu důvěryhodnosti předávající strany: konfigurace adresy URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> Musí se jednat o adresu URL HTTPS. IIS Express může při vývoji při hostování aplikace poskytnout certifikát podepsaný svým držitelem. Kestrel vyžaduje ruční konfiguraci certifikátu. Další podrobnosti najdete v [dokumentaci k Kestrel](xref:fundamentals/servers/kestrel) .

* Klikněte na tlačítko **Další** v části průvodce a **zavřete** na konci.

* ASP.NET Core identity vyžaduje deklaraci **ID názvu** . Přidejte ho z dialogu **upravit pravidla deklarací identity** :

![Upravit pravidla deklarace identity](ws-federation/_static/EditClaimRules.png)

* V **Průvodci přidáním pravidla deklarace identity transformace**ponechte výchozí možnost **Odeslat atributy LDAP jako šablonu deklarací** a klikněte na **Další**. Přidání pravidla mapování atributu **Sam-Account-Name** LDAP na **název** odchozí deklarace identity:

![Průvodce přidáním pravidla deklarace identity transformace: Konfigurace pravidla deklarace identity](ws-federation/_static/AddTransformClaimRule.png)

* V okně **upravit pravidla deklarací** klikněte na **Dokončit** > **OK** .

### <a name="azure-active-directory"></a>Azure Active Directory

* Přejděte do okna registrace aplikací tenanta AAD. Klikněte na **Registrace nové aplikace**:

![Azure Active Directory: Registrace aplikací](ws-federation/_static/AadNewAppRegistration.png)

* Zadejte název pro registraci aplikace. To není důležité pro aplikaci ASP.NET Core.
* Zadejte adresu URL, na které aplikace naslouchá, jako **přihlašovací adresu URL**:

![Azure Active Directory: Vytvoření registrace aplikace](ws-federation/_static/AadCreateAppRegistration.png)

* Klikněte na **koncové body** a poznamenejte si adresu URL **dokumentu federačních metadat** . Toto je `MetadataAddress`middlewaru WS-Federation:

![Azure Active Directory: koncové body](ws-federation/_static/AadFederationMetadataDocument.png)

* Přejděte k nové registraci aplikace. Klikněte na **nastavení** > **vlastnosti** a poznamenejte si **identifikátor URI ID aplikace**. Toto je `Wtrealm`middlewaru WS-Federation:

![Azure Active Directory: vlastnosti registrace aplikace](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a>Použití protokolu WS-Federation bez ASP.NET Core identity

Middleware WS-Federation se dá použít bez identity. Příklad:
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a>Přidání služby WS-Federation jako externího poskytovatele přihlášení pro ASP.NET Core identity

* Přidejte do projektu závislost na [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) .
* Přidat WS-Federation do `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a>Přihlášení pomocí WS-Federation

Přejděte do aplikace a klikněte na odkaz **Přihlásit** se v záhlaví navigace. K dispozici je možnost přihlásit se pomocí WsFederation: ![přihlašovací stránky](ws-federation/_static/WsFederationButton.png)

Pomocí služby ADFS jako poskytovatele přesměruje tlačítko na přihlašovací stránku ADFS: ![přihlašovací stránku ADFS](ws-federation/_static/AdfsLoginPage.png)

Když se Azure Active Directory jako poskytovatel, tlačítko přesměruje na přihlašovací stránku AAD: ![přihlašovací stránka AAD](ws-federation/_static/AadSignIn.png)

Úspěšné přihlášení pro nového uživatele přesměruje na stránku registrace uživatele aplikace: ![zaregistrovat stránku](ws-federation/_static/Register.png)