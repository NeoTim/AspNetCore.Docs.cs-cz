---
title: Zabezpečení Blazor WebAssembly samostatné aplikace v ASP.NET Core s využitím Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 5a461c73b6e157ebb9fa165aa4ba298e1f9e99ab
ms.sourcegitcommit: ec41ab354952b75557240923756a8c2ac79b49f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202703"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="14ea3-102">Zabezpečení Blazor WebAssembly samostatné aplikace v ASP.NET Core s využitím Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="14ea3-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="14ea3-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="14ea3-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="14ea3-104">Pokud chcete vytvořit [samostatnou Blazor WebAssembly aplikaci](xref:blazor/hosting-models#blazor-webassembly) , která pro ověřování používá [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) :</span><span class="sxs-lookup"><span data-stu-id="14ea3-104">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="14ea3-105">Při vytváření tenanta a registraci webové aplikace na webu Azure Portal postupujte podle pokynů v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="14ea3-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="14ea3-106">Vytvoření tenanta AAD B2C</span><span class="sxs-lookup"><span data-stu-id="14ea3-106">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="14ea3-107">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="14ea3-107">Record the following information:</span></span>

* <span data-ttu-id="14ea3-108">AAD B2C instance (například `https://contoso.b2clogin.com/` , která zahrnuje koncové lomítko): instance je schéma a hostitel registrace aplikace Azure B2C, který lze najít otevřením okna **koncových bodů** ze stránky **Registrace aplikací** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="14ea3-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash): The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>
* <span data-ttu-id="14ea3-109">AAD B2C primární/Vydavatel/doména klienta (například `contoso.onmicrosoft.com` ): doména je k dispozici jako **doména vydavatele** v okně **značky** Azure Portal pro registrovanou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="14ea3-109">AAD B2C Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="14ea3-110">Postupujte podle pokynů v [kurzu: znovu zaregistrujte aplikaci v Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) k registraci aplikace AAD pro *klientskou aplikaci* a pak postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="14ea3-110">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="14ea3-111">V **Azure Active Directory**  >  **Registrace aplikací**vyberte možnost **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="14ea3-111">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="14ea3-112">Zadejte **název** aplikace (například \*\* Blazor samostatné AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="14ea3-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="14ea3-113">U **podporovaných typů účtů**vyberte možnost více tenantů: **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="14ea3-113">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="14ea3-114">Ponechte rozevírací seznam **URI přesměrování** nastavenou na **Web** a zadejte následující identifikátor URI pro přesměrování: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="14ea3-114">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="14ea3-115">Výchozí port pro aplikaci běžící na Kestrel je 5001.</span><span class="sxs-lookup"><span data-stu-id="14ea3-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="14ea3-116">Pokud je aplikace spuštěná na jiném Kestrel portu, použijte port aplikace.</span><span class="sxs-lookup"><span data-stu-id="14ea3-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="14ea3-117">Pro IIS Express se náhodně generovaný port pro aplikaci dá najít ve vlastnostech aplikace na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="14ea3-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="14ea3-118">Vzhledem k tomu, že aplikace v tomto okamžiku neexistuje a port IIS Express není znám, vraťte se k tomuto kroku po vytvoření aplikace a aktualizaci identifikátoru URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="14ea3-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="14ea3-119">Po přeznačení se objeví dál v tomto tématu, které připomínat IIS Express uživatelům aktualizovat identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="14ea3-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="14ea3-120">Potvrďte, že **oprávnění**  >  **udělují správcům oprávnění k OpenID a offline_access** je povolené.</span><span class="sxs-lookup"><span data-stu-id="14ea3-120">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="14ea3-121">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="14ea3-121">Select **Register**.</span></span>

<span data-ttu-id="14ea3-122">Poznamenejte si ID aplikace (klienta) (například `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="14ea3-122">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="14ea3-123">Na webu konfigurace **ověřovacích**  >  **platforem**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="14ea3-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="14ea3-124">Ověřte, zda je **identifikátor URI přesměrování** k `https://localhost:{PORT}/authentication/login-callback` dispozici.</span><span class="sxs-lookup"><span data-stu-id="14ea3-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="14ea3-125">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="14ea3-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="14ea3-126">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="14ea3-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="14ea3-127">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="14ea3-127">Select the **Save** button.</span></span>

<span data-ttu-id="14ea3-128">V **Home**  >  **Azure AD B2C**  >  **toky pro uživatele**doma:</span><span class="sxs-lookup"><span data-stu-id="14ea3-128">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="14ea3-129">Vytvoření uživatelského toku pro registraci a přihlašování</span><span class="sxs-lookup"><span data-stu-id="14ea3-129">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="14ea3-130">**Application claims**  >  Aby bylo možné vyplnit součást (), vyberte alespoň atribut uživatelské**jméno zobrazení** deklarací identit aplikace `context.User.Identity.Name` `LoginDisplay` `Shared/LoginDisplay.razor` .</span><span class="sxs-lookup"><span data-stu-id="14ea3-130">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="14ea3-131">Zaznamenejte si název uživatelského toku pro registraci a přihlašování vytvořený pro aplikaci (například `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="14ea3-131">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="14ea3-132">V prázdné složce Nahraďte zástupné symboly v následujícím příkazu informacemi zaznamenanými dříve a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="14ea3-132">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="14ea3-133">Zástupný symbol</span><span class="sxs-lookup"><span data-stu-id="14ea3-133">Placeholder</span></span>                   | <span data-ttu-id="14ea3-134">Název Azure Portal</span><span class="sxs-lookup"><span data-stu-id="14ea3-134">Azure portal name</span></span>               | <span data-ttu-id="14ea3-135">Příklad</span><span class="sxs-lookup"><span data-stu-id="14ea3-135">Example</span></span>                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="14ea3-136">Instance</span><span class="sxs-lookup"><span data-stu-id="14ea3-136">Instance</span></span>                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `BlazorSample`                         |
| `{CLIENT ID}`                 | <span data-ttu-id="14ea3-137">ID aplikace (klienta)</span><span class="sxs-lookup"><span data-stu-id="14ea3-137">Application (client) ID</span></span>         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="14ea3-138">Tok uživatele pro registraci nebo přihlášení</span><span class="sxs-lookup"><span data-stu-id="14ea3-138">Sign-up/sign-in user flow</span></span>       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="14ea3-139">Primární/Vydavatel/doména tenanta</span><span class="sxs-lookup"><span data-stu-id="14ea3-139">Primary/Publisher/Tenant domain</span></span> | `contoso.onmicrosoft.com`              |

<span data-ttu-id="14ea3-140">Umístění výstupu zadané s `-o|--output` možností vytvoří složku projektu, pokud neexistuje a bude součástí názvu aplikace.</span><span class="sxs-lookup"><span data-stu-id="14ea3-140">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="14ea3-141">V Azure Portal **Authentication**  >  **Platform configurations**  >  **Web**  >  pro aplikace, které běží na serveru Kestrel s výchozími nastaveními, je pro port 5001 nakonfigurován**identifikátor URI webového přesměrování** konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="14ea3-141">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="14ea3-142">Pokud je aplikace spuštěná na náhodném IIS Expressm portu, můžete port pro aplikaci najít ve vlastnostech aplikace na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="14ea3-142">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="14ea3-143">Pokud port nebyl dříve nakonfigurovaný se známým portem aplikace, vraťte se k registraci aplikace v Azure Portal a aktualizujte identifikátor URI přesměrování pomocí správného portu.</span><span class="sxs-lookup"><span data-stu-id="14ea3-143">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="14ea3-144">Po vytvoření aplikace byste měli mít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="14ea3-144">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="14ea3-145">Přihlaste se k aplikaci pomocí uživatelského účtu AAD.</span><span class="sxs-lookup"><span data-stu-id="14ea3-145">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="14ea3-146">Vyžádá přístupové tokeny pro rozhraní API Microsoftu.</span><span class="sxs-lookup"><span data-stu-id="14ea3-146">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="14ea3-147">Další informace:</span><span class="sxs-lookup"><span data-stu-id="14ea3-147">For more information, see:</span></span>
  * [<span data-ttu-id="14ea3-148">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="14ea3-148">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="14ea3-149">[Rychlý Start: Konfigurace aplikace k vystavení webových rozhraní API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)</span><span class="sxs-lookup"><span data-stu-id="14ea3-149">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="14ea3-150">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="14ea3-150">Authentication package</span></span>

<span data-ttu-id="14ea3-151">Když je aplikace vytvořená tak, aby používala individuální účet B2C ( `IndividualB2C` ), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) ).</span><span class="sxs-lookup"><span data-stu-id="14ea3-151">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="14ea3-152">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="14ea3-152">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="14ea3-153">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="14ea3-153">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="14ea3-154">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)Balíček [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) do této aplikace přidá balíček.</span><span class="sxs-lookup"><span data-stu-id="14ea3-154">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="14ea3-155">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="14ea3-155">Authentication service support</span></span>

<span data-ttu-id="14ea3-156">Podpora ověřování uživatelů je registrovaná v kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodou rozšíření poskytovanou [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) balíčkem.</span><span class="sxs-lookup"><span data-stu-id="14ea3-156">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="14ea3-157">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s Identity poskytovatelem (IP).</span><span class="sxs-lookup"><span data-stu-id="14ea3-157">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="14ea3-158">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="14ea3-158">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="14ea3-159"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="14ea3-159">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="14ea3-160">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="14ea3-160">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="14ea3-161">Soubor zadal konfiguraci `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="14ea3-161">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="14ea3-162">Příklad:</span><span class="sxs-lookup"><span data-stu-id="14ea3-162">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="14ea3-163">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="14ea3-163">Access token scopes</span></span>

<span data-ttu-id="14ea3-164">Tato Blazor WebAssembly Šablona automaticky nekonfiguruje aplikaci tak, aby požadovala přístupový token pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="14ea3-164">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="14ea3-165">Pokud chcete jako součást toku přihlášení zřídit přístupový token, přidejte obor do výchozích oborů přístupového tokenu <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="14ea3-165">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="14ea3-166">Další informace najdete v následujících částech článku o *dalších scénářích* :</span><span class="sxs-lookup"><span data-stu-id="14ea3-166">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="14ea3-167">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="14ea3-167">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="14ea3-168">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="14ea3-168">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="14ea3-169">Režim přihlášení</span><span class="sxs-lookup"><span data-stu-id="14ea3-169">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="14ea3-170">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="14ea3-170">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="14ea3-171">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="14ea3-171">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="14ea3-172">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="14ea3-172">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="14ea3-173">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="14ea3-173">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="14ea3-174">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="14ea3-174">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="14ea3-175">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="14ea3-175">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="14ea3-176">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="14ea3-176">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="14ea3-177">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="14ea3-177">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="14ea3-178">Kurz: Vytvoření tenanta Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="14ea3-178">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="14ea3-179">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="14ea3-179">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
