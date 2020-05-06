---
title: Ověřování uživatelů pomocí WS-Federation v ASP.NET Core
author: chlowell
description: Tento kurz ukazuje, jak používat WS-Federation v aplikaci ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/ws-federation
ms.openlocfilehash: ce0c484e84bc2ddb4a1d287246c63663f3875924
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768426"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="103fa-103">Ověřování uživatelů pomocí WS-Federation v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="103fa-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="103fa-104">V tomto kurzu se dozvíte, jak uživatelům povolit, aby se přihlásili pomocí zprostředkovatele ověřování WS-Federation, jako je Active Directory Federation Services (AD FS) (ADFS) nebo [Azure Active Directory](/azure/active-directory/) (AAD).</span><span class="sxs-lookup"><span data-stu-id="103fa-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="103fa-105">Používá ukázkovou aplikaci ASP.NET Core 2,0 popsanou v [Facebooku, Google a v ověřování externích poskytovatelů](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="103fa-105">It uses the ASP.NET Core 2.0 sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="103fa-106">Pro aplikace ASP.NET Core 2,0 je podpora protokolu WS-Federation poskytována pomocí [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span><span class="sxs-lookup"><span data-stu-id="103fa-106">For ASP.NET Core 2.0 apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="103fa-107">Tato součást je přepravovaná z [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) a sdílí mnoho prvků této součásti.</span><span class="sxs-lookup"><span data-stu-id="103fa-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="103fa-108">Komponenty se ale liší v několika důležitých způsobech.</span><span class="sxs-lookup"><span data-stu-id="103fa-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="103fa-109">Ve výchozím nastavení nový middleware:</span><span class="sxs-lookup"><span data-stu-id="103fa-109">By default, the new middleware:</span></span>

* <span data-ttu-id="103fa-110">Nepovoluje nevyžádaná přihlášení.</span><span class="sxs-lookup"><span data-stu-id="103fa-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="103fa-111">Tato funkce protokolu WS-Federation je zranitelná vůči útokům XSRF.</span><span class="sxs-lookup"><span data-stu-id="103fa-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="103fa-112">Dá se ale povolit s `AllowUnsolicitedLogins` možností.</span><span class="sxs-lookup"><span data-stu-id="103fa-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="103fa-113">Nekontroluje každý příspěvek na formuláři pro zprávy o přihlášení.</span><span class="sxs-lookup"><span data-stu-id="103fa-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="103fa-114">Pouze požadavky na `CallbackPath` jsou kontrolovány pro přihlášení. `CallbackPath` výchozí hodnota je, `/signin-wsfed` ale lze ji změnit prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) .</span><span class="sxs-lookup"><span data-stu-id="103fa-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="103fa-115">Tuto cestu můžete sdílet s dalšími zprostředkovateli ověřování povolením možnosti [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .</span><span class="sxs-lookup"><span data-stu-id="103fa-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="103fa-116">Registrace aplikace ve službě Active Directory</span><span class="sxs-lookup"><span data-stu-id="103fa-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="103fa-117">Active Directory Federation Services</span><span class="sxs-lookup"><span data-stu-id="103fa-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="103fa-118">V konzole pro správu služby ADFS otevřete **Průvodce přidáním vztahu důvěryhodnosti předávající strany** serveru:</span><span class="sxs-lookup"><span data-stu-id="103fa-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![Průvodce přidáním vztahu důvěryhodnosti předávající strany: Vítejte](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="103fa-120">Vyberte, že chcete zadat data ručně:</span><span class="sxs-lookup"><span data-stu-id="103fa-120">Choose to enter data manually:</span></span>

![Průvodce přidáním vztahu důvěryhodnosti předávající strany: vybrat zdroj dat](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="103fa-122">Zadejte zobrazovaný název předávající strany.</span><span class="sxs-lookup"><span data-stu-id="103fa-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="103fa-123">Název není pro aplikaci ASP.NET Core důležitý.</span><span class="sxs-lookup"><span data-stu-id="103fa-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="103fa-124">U [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) chybí podpora pro šifrování tokenů, proto nekonfigurujte šifrovací certifikát tokenu:</span><span class="sxs-lookup"><span data-stu-id="103fa-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![Průvodce přidáním vztahu důvěryhodnosti předávající strany: Konfigurace certifikátu](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="103fa-126">Povolte podporu pasivního protokolu WS-Federation pomocí adresy URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="103fa-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="103fa-127">Ověřte, jestli je port pro aplikaci správný:</span><span class="sxs-lookup"><span data-stu-id="103fa-127">Verify the port is correct for the app:</span></span>

![Průvodce přidáním vztahu důvěryhodnosti předávající strany: konfigurace adresy URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="103fa-129">Musí se jednat o adresu URL HTTPS.</span><span class="sxs-lookup"><span data-stu-id="103fa-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="103fa-130">IIS Express může při vývoji při hostování aplikace poskytnout certifikát podepsaný svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="103fa-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="103fa-131">Kestrel vyžaduje ruční konfiguraci certifikátu.</span><span class="sxs-lookup"><span data-stu-id="103fa-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="103fa-132">Další podrobnosti najdete v [dokumentaci k Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="103fa-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="103fa-133">Klikněte na tlačítko **Další** v části průvodce a **zavřete** na konci.</span><span class="sxs-lookup"><span data-stu-id="103fa-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="103fa-134">ASP.NET Core Identity vyžaduje deklaraci **ID názvu** .</span><span class="sxs-lookup"><span data-stu-id="103fa-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="103fa-135">Přidejte ho z dialogu **upravit pravidla deklarací identity** :</span><span class="sxs-lookup"><span data-stu-id="103fa-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![Upravit pravidla deklarací identity](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="103fa-137">V **Průvodci přidáním pravidla deklarace identity transformace**ponechte výchozí možnost **Odeslat atributy LDAP jako šablonu deklarací** a klikněte na **Další**.</span><span class="sxs-lookup"><span data-stu-id="103fa-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="103fa-138">Přidání pravidla mapování atributu **Sam-Account-Name** LDAP na **název** odchozí deklarace identity:</span><span class="sxs-lookup"><span data-stu-id="103fa-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![Průvodce přidáním pravidla deklarace identity transformace: Konfigurace pravidla deklarace identity](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="103fa-140">V okně **upravit pravidla deklarací** klikněte na **Dokončit** > **OK** .</span><span class="sxs-lookup"><span data-stu-id="103fa-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="103fa-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="103fa-141">Azure Active Directory</span></span>

* <span data-ttu-id="103fa-142">Přejděte do okna registrace aplikací tenanta AAD.</span><span class="sxs-lookup"><span data-stu-id="103fa-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="103fa-143">Klikněte na **Registrace nové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="103fa-143">Click **New application registration**:</span></span>

![Azure Active Directory: Registrace aplikací](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="103fa-145">Zadejte název pro registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="103fa-145">Enter a name for the app registration.</span></span> <span data-ttu-id="103fa-146">To není důležité pro aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="103fa-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="103fa-147">Zadejte adresu URL, na které aplikace naslouchá, jako **přihlašovací adresu URL**:</span><span class="sxs-lookup"><span data-stu-id="103fa-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory: Vytvoření registrace aplikace](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="103fa-149">Klikněte na **koncové body** a poznamenejte si adresu URL **dokumentu federačních metadat** .</span><span class="sxs-lookup"><span data-stu-id="103fa-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="103fa-150">Toto je middleware ve službě WS- `MetadataAddress`Federation:</span><span class="sxs-lookup"><span data-stu-id="103fa-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: koncové body](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="103fa-152">Přejděte k nové registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="103fa-152">Navigate to the new app registration.</span></span> <span data-ttu-id="103fa-153">Klikněte na **Nastavení** > **vlastnosti** a poznamenejte si **identifikátor URI ID aplikace**.</span><span class="sxs-lookup"><span data-stu-id="103fa-153">Click **Settings** > **Properties** and make note of the **App ID URI**.</span></span> <span data-ttu-id="103fa-154">Toto je middleware ve službě WS- `Wtrealm`Federation:</span><span class="sxs-lookup"><span data-stu-id="103fa-154">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: vlastnosti registrace aplikace](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a><span data-ttu-id="103fa-156">Použití WS-Federation bez ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="103fa-156">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="103fa-157">Middleware WS-Federation se dá použít bez Identity.</span><span class="sxs-lookup"><span data-stu-id="103fa-157">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="103fa-158">Příklad:</span><span class="sxs-lookup"><span data-stu-id="103fa-158">For example:</span></span>
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a><span data-ttu-id="103fa-159">Přidání protokolu WS-Federation jako externího poskytovatele přihlášení pro ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="103fa-159">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="103fa-160">Přidejte do projektu závislost na [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) .</span><span class="sxs-lookup"><span data-stu-id="103fa-160">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="103fa-161">Přidat WS-Federation do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="103fa-161">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="103fa-162">Přihlášení pomocí WS-Federation</span><span class="sxs-lookup"><span data-stu-id="103fa-162">Log in with WS-Federation</span></span>

<span data-ttu-id="103fa-163">Přejděte do aplikace a klikněte na odkaz **Přihlásit** se v záhlaví navigace.</span><span class="sxs-lookup"><span data-stu-id="103fa-163">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="103fa-164">K dispozici je možnost přihlásit se pomocí WsFederation: ![přihlašovací stránka](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="103fa-164">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="103fa-165">Pomocí služby ADFS jako poskytovatele se tlačítko přesměruje na přihlašovací stránku ADFS: přihlašovací stránka ADFS. ![](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="103fa-165">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="103fa-166">Když se Azure Active Directory jako poskytovatel, tlačítko přesměruje na přihlašovací stránku AAD: ![přihlašovací stránka AAD](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="103fa-166">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="103fa-167">Úspěšné přihlášení pro nového uživatele přesměruje na stránku pro registraci uživatele aplikace: ![zaregistrovat stránku](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="103fa-167">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>