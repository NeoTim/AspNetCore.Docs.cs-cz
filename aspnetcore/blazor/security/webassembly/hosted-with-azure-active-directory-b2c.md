---
title: Zabezpečení Blazor WebAssembly hostované aplikace v ASP.NET Core s využitím Azure Active Directory B2C
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 31a48ba6dc48b7279e8f3d71cf1e7e20dd1390fe
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504720"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="4219c-102">Zabezpečení Blazor WebAssembly hostované aplikace v ASP.NET Core s využitím Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="4219c-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="4219c-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4219c-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4219c-104">Tento článek popisuje, jak vytvořit [hostovanou Blazor WebAssembly aplikaci](xref:blazor/hosting-models#blazor-webassembly) , která pro ověřování používá [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) .</span><span class="sxs-lookup"><span data-stu-id="4219c-104">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="4219c-105">Registrace aplikací v AAD B2C a vytvoření řešení</span><span class="sxs-lookup"><span data-stu-id="4219c-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="4219c-106">Vytvoření tenanta</span><span class="sxs-lookup"><span data-stu-id="4219c-106">Create a tenant</span></span>

<span data-ttu-id="4219c-107">Postupujte podle pokynů v [kurzu: vytvoření tenanta Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) pro vytvoření tenanta AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="4219c-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="4219c-108">Poznamenejte si instanci AAD B2C (například `https://contoso.b2clogin.com/` , která obsahuje koncové lomítko).</span><span class="sxs-lookup"><span data-stu-id="4219c-108">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="4219c-109">Instance je schéma a hostitel registrace aplikace Azure B2C, který najdete otevřením okna **koncové body** na stránce **Registrace aplikací** v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="4219c-109">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="4219c-110">Registrace aplikace API serveru</span><span class="sxs-lookup"><span data-stu-id="4219c-110">Register a server API app</span></span>

<span data-ttu-id="4219c-111">Postupujte podle pokynů v [kurzu: Zaregistrujte aplikaci v Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) k registraci aplikace AAD pro *aplikaci API serveru* a pak postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="4219c-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="4219c-112">V **Azure Active Directory**  >  **Registrace aplikací**vyberte možnost **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="4219c-112">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="4219c-113">Zadejte **název** aplikace (například \*\* Blazor Server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="4219c-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="4219c-114">U **podporovaných typů účtů**vyberte možnost více tenantů: **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="4219c-114">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="4219c-115">*Aplikace API serveru* v tomto scénáři nevyžaduje **identifikátor URI přesměrování** , proto nechejte rozevírací seznam nastavený na **Web** a nezadávejte identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="4219c-115">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="4219c-116">Potvrďte, že **oprávnění**  >  **udělují správcům oprávnění k OpenID a offline_access** je povolené.</span><span class="sxs-lookup"><span data-stu-id="4219c-116">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="4219c-117">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="4219c-117">Select **Register**.</span></span>

<span data-ttu-id="4219c-118">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="4219c-118">Record the following information:</span></span>

* <span data-ttu-id="4219c-119">*Aplikace API serveru* ID aplikace (klienta) (například `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="4219c-119">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="4219c-120">Primární/Vydavatel nebo doména klienta služby AAD (například `contoso.onmicrosoft.com` ): doména je k dispozici jako **doména vydavatele** v okně **značky** Azure Portal pro registrovanou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4219c-120">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="4219c-121">Ve **vystavení rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="4219c-121">In **Expose an API**:</span></span>

1. <span data-ttu-id="4219c-122">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="4219c-122">Select **Add a scope**.</span></span>
1. <span data-ttu-id="4219c-123">Vyberte **Uložit a pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="4219c-123">Select **Save and continue**.</span></span>
1. <span data-ttu-id="4219c-124">Zadejte **název oboru** (například `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="4219c-124">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="4219c-125">Zadejte **Zobrazovaný název souhlasu správce** (například `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="4219c-125">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="4219c-126">Zadejte **Popis souhlasu správce** (například `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="4219c-126">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="4219c-127">Potvrďte, že je **stav** nastavený na **povoleno**.</span><span class="sxs-lookup"><span data-stu-id="4219c-127">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="4219c-128">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="4219c-128">Select **Add scope**.</span></span>

<span data-ttu-id="4219c-129">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="4219c-129">Record the following information:</span></span>

* <span data-ttu-id="4219c-130">Identifikátor URI ID aplikace (například, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` , `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` nebo vlastní hodnota, kterou jste zadali)</span><span class="sxs-lookup"><span data-stu-id="4219c-130">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="4219c-131">Název oboru (například `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="4219c-131">Scope name (for example, `API.Access`)</span></span>

<span data-ttu-id="4219c-132">Identifikátor URI ID aplikace může vyžadovat zvláštní konfiguraci v klientské aplikaci, která je popsaná v části [obory přístupového tokenu](#access-token-scopes) dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="4219c-132">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="4219c-133">Registrace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="4219c-133">Register a client app</span></span>

<span data-ttu-id="4219c-134">Postupujte podle pokynů v [kurzu: znovu zaregistrujte aplikaci v Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) k registraci aplikace AAD pro *klientskou aplikaci* a pak postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="4219c-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="4219c-135">V **Azure Active Directory**  >  **Registrace aplikací**vyberte možnost **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="4219c-135">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="4219c-136">Zadejte **název** aplikace (například \*\* Blazor AAD B2C klienta\*\*).</span><span class="sxs-lookup"><span data-stu-id="4219c-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="4219c-137">U **podporovaných typů účtů**vyberte možnost více tenantů: **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="4219c-137">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="4219c-138">Ponechte rozevírací seznam **URI přesměrování** nastavenou na **Web** a zadejte následující identifikátor URI pro přesměrování: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="4219c-138">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="4219c-139">Výchozí port pro aplikaci běžící na Kestrel je 5001.</span><span class="sxs-lookup"><span data-stu-id="4219c-139">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="4219c-140">Pokud je aplikace spuštěná na jiném Kestrel portu, použijte port aplikace.</span><span class="sxs-lookup"><span data-stu-id="4219c-140">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="4219c-141">Pro IIS Express se náhodně generovaný port pro aplikaci dá najít ve vlastnostech serverové aplikace na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="4219c-141">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="4219c-142">Vzhledem k tomu, že aplikace v tomto okamžiku neexistuje a port IIS Express není znám, vraťte se k tomuto kroku po vytvoření aplikace a aktualizaci identifikátoru URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="4219c-142">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="4219c-143">V části [Vytvoření aplikace](#create-the-app) se zobrazí zpráva s upozorněním, že IIS Express uživatelé chtějí aktualizovat identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="4219c-143">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="4219c-144">Potvrďte, že **oprávnění**  >  **udělují správcům oprávnění k OpenID a offline_access** je povolené.</span><span class="sxs-lookup"><span data-stu-id="4219c-144">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="4219c-145">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="4219c-145">Select **Register**.</span></span>

<span data-ttu-id="4219c-146">Poznamenejte si ID aplikace (klienta) (například `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="4219c-146">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="4219c-147">Na webu konfigurace **ověřovacích**  >  **platforem**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="4219c-147">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="4219c-148">Ověřte, zda je **identifikátor URI přesměrování** k `https://localhost:{PORT}/authentication/login-callback` dispozici.</span><span class="sxs-lookup"><span data-stu-id="4219c-148">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="4219c-149">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="4219c-149">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="4219c-150">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="4219c-150">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="4219c-151">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="4219c-151">Select the **Save** button.</span></span>

<span data-ttu-id="4219c-152">V **oprávněních rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="4219c-152">In **API permissions**:</span></span>

1. <span data-ttu-id="4219c-153">Vyberte **Přidat oprávnění** a potom **Moje rozhraní API**.</span><span class="sxs-lookup"><span data-stu-id="4219c-153">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="4219c-154">Ve sloupci **název** vyberte *aplikace API serveru* (například \*\* Blazor Server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="4219c-154">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="4219c-155">Otevřete seznam **rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="4219c-155">Open the **API** list.</span></span>
1. <span data-ttu-id="4219c-156">Povolte přístup k rozhraní API (například `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="4219c-156">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="4219c-157">Vyberte **Přidat oprávnění**.</span><span class="sxs-lookup"><span data-stu-id="4219c-157">Select **Add permissions**.</span></span>
1. <span data-ttu-id="4219c-158">Klikněte na tlačítko **udělení souhlasu správce pro {Name}** .</span><span class="sxs-lookup"><span data-stu-id="4219c-158">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="4219c-159">Akci potvrďte výběrem **Ano**.</span><span class="sxs-lookup"><span data-stu-id="4219c-159">Select **Yes** to confirm.</span></span>

<span data-ttu-id="4219c-160">V **Home**  >  **Azure AD B2C**  >  **toky pro uživatele**doma:</span><span class="sxs-lookup"><span data-stu-id="4219c-160">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="4219c-161">Vytvoření uživatelského toku pro registraci a přihlašování</span><span class="sxs-lookup"><span data-stu-id="4219c-161">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="4219c-162">**Application claims**  >  Aby bylo možné vyplnit součást (), vyberte alespoň atribut uživatelské**jméno zobrazení** deklarací identit aplikace `context.User.Identity.Name` `LoginDisplay` `Shared/LoginDisplay.razor` .</span><span class="sxs-lookup"><span data-stu-id="4219c-162">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="4219c-163">Zaznamenejte si název uživatelského toku pro registraci a přihlašování vytvořený pro aplikaci (například `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="4219c-163">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="4219c-164">Vytvoření aplikace</span><span class="sxs-lookup"><span data-stu-id="4219c-164">Create the app</span></span>

<span data-ttu-id="4219c-165">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="4219c-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="4219c-166">Zástupný symbol</span><span class="sxs-lookup"><span data-stu-id="4219c-166">Placeholder</span></span>                   | <span data-ttu-id="4219c-167">Název Azure Portal</span><span class="sxs-lookup"><span data-stu-id="4219c-167">Azure portal name</span></span>                                     | <span data-ttu-id="4219c-168">Příklad</span><span class="sxs-lookup"><span data-stu-id="4219c-168">Example</span></span>                                |
| ----------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="4219c-169">Instance</span><span class="sxs-lookup"><span data-stu-id="4219c-169">Instance</span></span>                                              | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="4219c-170">ID aplikace (klienta) pro *klientskou aplikaci*</span><span class="sxs-lookup"><span data-stu-id="4219c-170">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="4219c-171">Název oboru</span><span class="sxs-lookup"><span data-stu-id="4219c-171">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="4219c-172">ID aplikace (klienta) pro *aplikaci Server API*</span><span class="sxs-lookup"><span data-stu-id="4219c-172">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="4219c-173">Identifikátor URI ID aplikace ([Viz Poznámka](#access-token-scopes))</span><span class="sxs-lookup"><span data-stu-id="4219c-173">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="4219c-174">Tok uživatele pro registraci nebo přihlášení</span><span class="sxs-lookup"><span data-stu-id="4219c-174">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="4219c-175">Primární/Vydavatel/doména tenanta</span><span class="sxs-lookup"><span data-stu-id="4219c-175">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |

<span data-ttu-id="4219c-176">Umístění výstupu zadané s `-o|--output` možností vytvoří složku projektu, pokud neexistuje a bude součástí názvu aplikace.</span><span class="sxs-lookup"><span data-stu-id="4219c-176">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="4219c-177">Předejte tomuto parametru identifikátor URI ID aplikace `app-id-uri` , ale Všimněte si, že se v klientské aplikaci může vyžadovat Změna konfigurace, která je popsaná v části [obory přístupového tokenu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="4219c-177">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="4219c-178">Kromě toho obor nastavený podle hostované Blazor šablony může mít opakovaný hostitel identifikátoru URI ID aplikace.</span><span class="sxs-lookup"><span data-stu-id="4219c-178">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="4219c-179">Ověřte, že je obor konfigurovaný pro `DefaultAccessTokenScopes` kolekci správný v `Program.Main` ( `Program.cs` ) *klientské aplikaci*.</span><span class="sxs-lookup"><span data-stu-id="4219c-179">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="4219c-180">V Azure Portal **Authentication** *Client app's*  >  **Platform configurations**  >  **Web**  >  pro aplikace, které běží na serveru Kestrel s výchozími nastaveními, je 5001 nakonfigurován**identifikátor URI** webu klientské aplikace konfigurace platformy ověřování.</span><span class="sxs-lookup"><span data-stu-id="4219c-180">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="4219c-181">Pokud *klientská aplikace* běží na náhodném IIS Express portu, můžete port pro aplikaci najít ve vlastnostech *aplikace API serveru* na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="4219c-181">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="4219c-182">Pokud port nebyl dříve nakonfigurovaný se známým portem *klientské aplikace* , vraťte se k registraci *klientské aplikace* v Azure Portal a aktualizujte identifikátor URI přesměrování pomocí správného portu.</span><span class="sxs-lookup"><span data-stu-id="4219c-182">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="4219c-183">Konfigurace aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="4219c-183">Server app configuration</span></span>

<span data-ttu-id="4219c-184">*Tato část se týká **`Server`** aplikace řešení.*</span><span class="sxs-lookup"><span data-stu-id="4219c-184">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="4219c-185">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="4219c-185">Authentication package</span></span>

<span data-ttu-id="4219c-186">Podpora ověřování a autorizace volání ASP.NET Core webových rozhraní API je zajištěna [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) balíčkem:</span><span class="sxs-lookup"><span data-stu-id="4219c-186">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="4219c-187">Pro zástupný text je `{VERSION}` nejnovější stabilní verze balíčku, která odpovídá verzi sdílené architektury aplikace, nalezena v **historii verzí** balíčku na adrese [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="4219c-187">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="4219c-188">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="4219c-188">Authentication service support</span></span>

<span data-ttu-id="4219c-189">`AddAuthentication`Metoda nastaví služby ověřování v rámci aplikace a nakonfiguruje obslužnou rutinu JWT nosiče jako výchozí metodu ověřování.</span><span class="sxs-lookup"><span data-stu-id="4219c-189">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="4219c-190"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A>Metoda nastavuje konkrétní parametry v obslužné rutině JWT nosiče vyžadované k ověření tokenů vygenerovaných Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="4219c-190">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="4219c-191"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> a <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Ujistěte se, že:</span><span class="sxs-lookup"><span data-stu-id="4219c-191"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="4219c-192">Aplikace se pokusí analyzovat a ověřit tokeny příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="4219c-192">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="4219c-193">Všechny žádosti o přístup k chráněnému prostředku bez správných přihlašovacích údajů selžou.</span><span class="sxs-lookup"><span data-stu-id="4219c-193">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="4219c-194">Uživatel. Identity . Jméno</span><span class="sxs-lookup"><span data-stu-id="4219c-194">User.Identity.Name</span></span>

<span data-ttu-id="4219c-195">Ve výchozím nastavení `User.Identity.Name` není naplněno.</span><span class="sxs-lookup"><span data-stu-id="4219c-195">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="4219c-196">Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hodnotu z `name` typu deklarace, nakonfigurujte <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4219c-196">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="4219c-197">Nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="4219c-197">App settings</span></span>

<span data-ttu-id="4219c-198">`appsettings.json`Soubor obsahuje možnosti pro konfiguraci obslužné rutiny nosiče JWT používané k ověření přístupových tokenů.</span><span class="sxs-lookup"><span data-stu-id="4219c-198">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="4219c-199">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4219c-199">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="4219c-200">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="4219c-200">WeatherForecast controller</span></span>

<span data-ttu-id="4219c-201">Řadič WeatherForecast (*Controllers/WeatherForecastController. cs*) zpřístupňuje chráněné rozhraní API s [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributem použitým pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="4219c-201">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="4219c-202">Je **důležité** si uvědomit, že:</span><span class="sxs-lookup"><span data-stu-id="4219c-202">It's **important** to understand that:</span></span>

* <span data-ttu-id="4219c-203">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atribut v tomto řadiči rozhraní API je jediná věc, která chrání toto rozhraní API před neoprávněným přístupem.</span><span class="sxs-lookup"><span data-stu-id="4219c-203">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="4219c-204">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atribut použitý v Blazor WebAssembly aplikaci slouží pouze jako pomocný parametr aplikace, který by měl být uživatel autorizován, aby mohla aplikace správně fungovat.</span><span class="sxs-lookup"><span data-stu-id="4219c-204">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="4219c-205">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="4219c-205">Client app configuration</span></span>

<span data-ttu-id="4219c-206">*Tato část se týká **`Client`** aplikace řešení.*</span><span class="sxs-lookup"><span data-stu-id="4219c-206">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="4219c-207">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="4219c-207">Authentication package</span></span>

<span data-ttu-id="4219c-208">Když je aplikace vytvořená tak, aby používala individuální účet B2C ( `IndividualB2C` ), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="4219c-208">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="4219c-209">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4219c-209">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="4219c-210">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="4219c-210">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="4219c-211">Pro zástupný text je `{VERSION}` nejnovější stabilní verze balíčku, která odpovídá verzi sdílené architektury aplikace, nalezena v **historii verzí** balíčku na adrese [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="4219c-211">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="4219c-212">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Balíček [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) do této aplikace přidá balíček.</span><span class="sxs-lookup"><span data-stu-id="4219c-212">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="4219c-213">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="4219c-213">Authentication service support</span></span>

<span data-ttu-id="4219c-214">Přidávají <xref:System.Net.Http.HttpClient> se podpory pro instance, které zahrnují přístupové tokeny při vytváření žádostí na serverový projekt.</span><span class="sxs-lookup"><span data-stu-id="4219c-214">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="4219c-215">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="4219c-215">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="4219c-216">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace (například `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="4219c-216">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="4219c-217">Podpora ověřování uživatelů je registrovaná v kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodou rozšíření poskytovanou [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) balíčkem.</span><span class="sxs-lookup"><span data-stu-id="4219c-217">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="4219c-218">Tato metoda nastavuje služby, které aplikace potřebuje k interakci se Identity zprostředkovatelem (IP).</span><span class="sxs-lookup"><span data-stu-id="4219c-218">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="4219c-219">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="4219c-219">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="4219c-220"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="4219c-220">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="4219c-221">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="4219c-221">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="4219c-222">Soubor zadal konfiguraci `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="4219c-222">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="4219c-223">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4219c-223">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="4219c-224">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="4219c-224">Access token scopes</span></span>

<span data-ttu-id="4219c-225">Výchozí obory přístupového tokenu představují seznam oborů přístupového tokenu, které jsou:</span><span class="sxs-lookup"><span data-stu-id="4219c-225">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="4219c-226">Ve výchozím nastavení zahrnuty v žádosti o přihlášení.</span><span class="sxs-lookup"><span data-stu-id="4219c-226">Included by default in the sign in request.</span></span>
* <span data-ttu-id="4219c-227">Slouží ke zřízení přístupového tokenu hned po ověření.</span><span class="sxs-lookup"><span data-stu-id="4219c-227">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="4219c-228">Všechny obory musí patřit do stejné aplikace na pravidla Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="4219c-228">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="4219c-229">Další obory je možné přidat pro další aplikace API podle potřeby:</span><span class="sxs-lookup"><span data-stu-id="4219c-229">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="4219c-230">Další informace najdete v následujících částech článku o *dalších scénářích* :</span><span class="sxs-lookup"><span data-stu-id="4219c-230">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="4219c-231">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="4219c-231">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="4219c-232">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="4219c-232">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="4219c-233">Režim přihlášení</span><span class="sxs-lookup"><span data-stu-id="4219c-233">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="4219c-234">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="4219c-234">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="4219c-235">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="4219c-235">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="4219c-236">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="4219c-236">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="4219c-237">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="4219c-237">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="4219c-238">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="4219c-238">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="4219c-239">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="4219c-239">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="4219c-240">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="4219c-240">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="4219c-241">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="4219c-241">Run the app</span></span>

<span data-ttu-id="4219c-242">Spusťte aplikaci z projektu serveru.</span><span class="sxs-lookup"><span data-stu-id="4219c-242">Run the app from the Server project.</span></span> <span data-ttu-id="4219c-243">Při použití sady Visual Studio buď:</span><span class="sxs-lookup"><span data-stu-id="4219c-243">When using Visual Studio, either:</span></span>

* <span data-ttu-id="4219c-244">Nastavte rozevírací seznam **projekty po spuštění** na panelu nástrojů na *aplikaci API serveru* a vyberte tlačítko **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="4219c-244">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="4219c-245">Vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .</span><span class="sxs-lookup"><span data-stu-id="4219c-245">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="4219c-246">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4219c-246">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="4219c-247">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="4219c-247">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="4219c-248">Kurz: Vytvoření tenanta Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="4219c-248">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="4219c-249">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="4219c-249">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
