---
title: Zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly pomocí Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 9332eddd3d428e8a25910d387f95b870926d5ae5
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103775"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="16f78-102">Zabezpečení Blazor hostované aplikace ASP.NET Core WebAssembly pomocí Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="16f78-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="16f78-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="16f78-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="16f78-104">Tento článek popisuje, jak vytvořit [ Blazor hostovanou aplikaci WebAssembly](xref:blazor/hosting-models#blazor-webassembly) , která pro ověřování používá [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) .</span><span class="sxs-lookup"><span data-stu-id="16f78-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="16f78-105">Registrace aplikací v AAD a vytvoření řešení</span><span class="sxs-lookup"><span data-stu-id="16f78-105">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="16f78-106">Vytvoření tenanta</span><span class="sxs-lookup"><span data-stu-id="16f78-106">Create a tenant</span></span>

<span data-ttu-id="16f78-107">Postupujte podle pokynů v [rychlém startu: nastavení tenanta](/azure/active-directory/develop/quickstart-create-new-tenant) pro vytvoření TENANTA v AAD.</span><span class="sxs-lookup"><span data-stu-id="16f78-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="16f78-108">Registrace aplikace API serveru</span><span class="sxs-lookup"><span data-stu-id="16f78-108">Register a server API app</span></span>

<span data-ttu-id="16f78-109">Postupujte podle pokynů v [rychlém startu: registrace aplikace pomocí platformy Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) a dalších témat služby Azure AAD k registraci aplikace AAD pro *aplikaci API serveru* a následnému provedení následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="16f78-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="16f78-110">V **Azure Active Directory**  >  **Registrace aplikací**vyberte možnost **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="16f78-110">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="16f78-111">Zadejte **název** aplikace (například \*\* Blazor AAD serveru\*\*).</span><span class="sxs-lookup"><span data-stu-id="16f78-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="16f78-112">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="16f78-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="16f78-113">Pro toto prostředí můžete vybrat **účty pouze v tomto organizačním adresáři** (jeden tenant).</span><span class="sxs-lookup"><span data-stu-id="16f78-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="16f78-114">*Aplikace API serveru* v tomto scénáři nevyžaduje **identifikátor URI přesměrování** , proto nechejte rozevírací seznam nastavený na **Web** a nezadávejte identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="16f78-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="16f78-115">Zakažte **oprávnění**  >  **udělení souhlasu správce OpenID a offline_access oprávnění** .</span><span class="sxs-lookup"><span data-stu-id="16f78-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="16f78-116">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="16f78-116">Select **Register**.</span></span>

<span data-ttu-id="16f78-117">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="16f78-117">Record the following information:</span></span>

* <span data-ttu-id="16f78-118">*Aplikace API serveru* ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="16f78-118">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="16f78-119">ID adresáře (ID klienta) (například `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="16f78-119">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="16f78-120">Doména tenanta AAD (například `contoso.onmicrosoft.com` ): doména je k dispozici jako **doména vydavatele** v okně **značky** Azure Portal pro registrovanou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="16f78-120">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="16f78-121">V okně **oprávnění rozhraní API**odeberte **Microsoft Graph**  >  **uživatel. číst** , protože aplikace nevyžaduje přihlášení ani přístup k profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="16f78-121">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="16f78-122">Ve **vystavení rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="16f78-122">In **Expose an API**:</span></span>

1. <span data-ttu-id="16f78-123">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="16f78-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="16f78-124">Vyberte **Uložit a pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="16f78-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="16f78-125">Zadejte **název oboru** (například `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="16f78-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="16f78-126">Zadejte **Zobrazovaný název souhlasu správce** (například `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="16f78-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="16f78-127">Zadejte **Popis souhlasu správce** (například `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="16f78-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="16f78-128">Potvrďte, že je **stav** nastavený na **povoleno**.</span><span class="sxs-lookup"><span data-stu-id="16f78-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="16f78-129">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="16f78-129">Select **Add scope**.</span></span>

<span data-ttu-id="16f78-130">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="16f78-130">Record the following information:</span></span>

* <span data-ttu-id="16f78-131">Identifikátor URI ID aplikace (například, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` nebo vlastní hodnota, kterou jste zadali)</span><span class="sxs-lookup"><span data-stu-id="16f78-131">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="16f78-132">Výchozí obor (například `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="16f78-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="16f78-133">Registrace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="16f78-133">Register a client app</span></span>

<span data-ttu-id="16f78-134">Postupujte podle pokynů v [rychlém startu: registrace aplikace s využitím platformy Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) a dalších témat Azure AAD k registraci aplikace AAD pro *klientskou aplikaci* a následnému provedení následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="16f78-134">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="16f78-135">V **Azure Active Directory**  >  **Registrace aplikací**vyberte možnost **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="16f78-135">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="16f78-136">Zadejte **název** aplikace (například \*\* Blazor AAD klienta\*\*).</span><span class="sxs-lookup"><span data-stu-id="16f78-136">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="16f78-137">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="16f78-137">Choose a **Supported account types**.</span></span> <span data-ttu-id="16f78-138">Pro toto prostředí můžete vybrat **účty pouze v tomto organizačním adresáři** (jeden tenant).</span><span class="sxs-lookup"><span data-stu-id="16f78-138">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="16f78-139">Ponechte rozevírací seznam **URI přesměrování** nastavenou na **Web** a zadejte následující identifikátor URI pro přesměrování: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="16f78-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="16f78-140">Výchozí port pro aplikaci běžící na Kestrel je 5001.</span><span class="sxs-lookup"><span data-stu-id="16f78-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="16f78-141">Pokud je aplikace spuštěná na jiném Kestrel portu, použijte port aplikace.</span><span class="sxs-lookup"><span data-stu-id="16f78-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="16f78-142">Pro IIS Express se náhodně generovaný port pro aplikaci dá najít ve vlastnostech serverové aplikace na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="16f78-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="16f78-143">Vzhledem k tomu, že aplikace v tomto okamžiku neexistuje a port IIS Express není znám, vraťte se k tomuto kroku po vytvoření aplikace a aktualizaci identifikátoru URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="16f78-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="16f78-144">V části [Vytvoření aplikace](#create-the-app) se zobrazí zpráva s upozorněním, že IIS Express uživatelé chtějí aktualizovat identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="16f78-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="16f78-145">Zakažte **oprávnění**  >  **udělení souhlasu správce OpenID a offline_access oprávnění** .</span><span class="sxs-lookup"><span data-stu-id="16f78-145">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="16f78-146">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="16f78-146">Select **Register**.</span></span>

<span data-ttu-id="16f78-147">Zaznamenejte ID aplikace *klienta aplikace* (ID klienta) (například `33333333-3333-3333-3333-333333333333` ).</span><span class="sxs-lookup"><span data-stu-id="16f78-147">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

<span data-ttu-id="16f78-148">Na webu konfigurace **ověřovacích**  >  **platforem**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="16f78-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="16f78-149">Ověřte, zda je **identifikátor URI přesměrování** k `https://localhost:{PORT}/authentication/login-callback` dispozici.</span><span class="sxs-lookup"><span data-stu-id="16f78-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="16f78-150">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="16f78-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="16f78-151">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="16f78-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="16f78-152">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="16f78-152">Select the **Save** button.</span></span>

<span data-ttu-id="16f78-153">V **oprávněních rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="16f78-153">In **API permissions**:</span></span>

1. <span data-ttu-id="16f78-154">Potvrďte, že aplikace má **Microsoft Graph**  >  oprávnění**uživatel. číst** .</span><span class="sxs-lookup"><span data-stu-id="16f78-154">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="16f78-155">Vyberte **Přidat oprávnění** a potom **Moje rozhraní API**.</span><span class="sxs-lookup"><span data-stu-id="16f78-155">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="16f78-156">Vyberte *aplikaci API serveru* ze sloupce **název** (například \*\* Blazor AAD serveru\*\*).</span><span class="sxs-lookup"><span data-stu-id="16f78-156">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="16f78-157">Otevřete seznam **rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="16f78-157">Open the **API** list.</span></span>
1. <span data-ttu-id="16f78-158">Povolte přístup k rozhraní API (například `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="16f78-158">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="16f78-159">Vyberte **Přidat oprávnění**.</span><span class="sxs-lookup"><span data-stu-id="16f78-159">Select **Add permissions**.</span></span>
1. <span data-ttu-id="16f78-160">Vyberte tlačítko **pro udělení obsahu správce pro {TENANT}** .</span><span class="sxs-lookup"><span data-stu-id="16f78-160">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="16f78-161">Výběrem **Ano** potvrďte.</span><span class="sxs-lookup"><span data-stu-id="16f78-161">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="16f78-162">Vytvoření aplikace</span><span class="sxs-lookup"><span data-stu-id="16f78-162">Create the app</span></span>

<span data-ttu-id="16f78-163">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="16f78-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="16f78-164">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="16f78-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="16f78-165">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="16f78-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="16f78-166">Předejte tomuto parametru identifikátor URI ID aplikace `app-id-uri` , ale Všimněte si, že se v klientské aplikaci může vyžadovat Změna konfigurace, která je popsaná v části [obory přístupového tokenu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="16f78-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="16f78-167">V Azure Portal **Authentication** *Client app's*  >  **Platform configurations**  >  **Web**  >  pro aplikace, které běží na serveru Kestrel s výchozími nastaveními, je 5001 nakonfigurován**identifikátor URI** webu klientské aplikace konfigurace platformy ověřování.</span><span class="sxs-lookup"><span data-stu-id="16f78-167">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="16f78-168">Pokud *klientská aplikace* běží na náhodném IIS Express portu, port pro aplikaci najdete ve vlastnostech *serverové aplikace* na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="16f78-168">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="16f78-169">Pokud port nebyl dříve nakonfigurovaný se známým portem *klientské aplikace* , vraťte se k registraci *klientské aplikace* v Azure Portal a aktualizujte identifikátor URI přesměrování pomocí správného portu.</span><span class="sxs-lookup"><span data-stu-id="16f78-169">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="16f78-170">Konfigurace aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="16f78-170">Server app configuration</span></span>

<span data-ttu-id="16f78-171">*Tato část se vztahuje k **serverové** aplikaci řešení.*</span><span class="sxs-lookup"><span data-stu-id="16f78-171">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="16f78-172">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="16f78-172">Authentication package</span></span>

<span data-ttu-id="16f78-173">Podpora ověřování a autorizace volání ASP.NET Core webových rozhraní API je poskytována balíčkem [Microsoft. AspNetCore. Authentication. AzureAD. UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) :</span><span class="sxs-lookup"><span data-stu-id="16f78-173">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [Microsoft.AspNetCore.Authentication.AzureAD.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="16f78-174">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="16f78-174">Authentication service support</span></span>

<span data-ttu-id="16f78-175">`AddAuthentication`Metoda nastaví služby ověřování v rámci aplikace a nakonfiguruje obslužnou rutinu JWT nosiče jako výchozí metodu ověřování.</span><span class="sxs-lookup"><span data-stu-id="16f78-175">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="16f78-176"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>Metoda nastavuje konkrétní parametry v obslužné rutině JWT nosiče vyžadované k ověření tokenů vygenerovaných Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="16f78-176">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="16f78-177"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>a <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Ujistěte se, že:</span><span class="sxs-lookup"><span data-stu-id="16f78-177"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="16f78-178">Aplikace se pokusí analyzovat a ověřit tokeny příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="16f78-178">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="16f78-179">Všechny žádosti o přístup k chráněnému prostředku bez správných přihlašovacích údajů selžou.</span><span class="sxs-lookup"><span data-stu-id="16f78-179">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="16f78-180">Uživatel. Identity . Jméno</span><span class="sxs-lookup"><span data-stu-id="16f78-180">User.Identity.Name</span></span>

<span data-ttu-id="16f78-181">Ve výchozím nastavení rozhraní API serverové aplikace naplní `User.Identity.Name` hodnotu z `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` typu deklarace (například `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="16f78-181">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="16f78-182">Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hodnotu z `name` typu deklarace, nakonfigurujte [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="16f78-182">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="16f78-183">Nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="16f78-183">App settings</span></span>

<span data-ttu-id="16f78-184">*appsettings.jsv* souboru obsahuje možnosti pro konfiguraci obslužné rutiny nosiče JWT používané k ověření přístupových tokenů:</span><span class="sxs-lookup"><span data-stu-id="16f78-184">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="16f78-185">Příklad:</span><span class="sxs-lookup"><span data-stu-id="16f78-185">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="16f78-186">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="16f78-186">WeatherForecast controller</span></span>

<span data-ttu-id="16f78-187">Řadič WeatherForecast (*Controllers/WeatherForecastController. cs*) zpřístupňuje chráněné rozhraní API s [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributem použitým pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="16f78-187">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="16f78-188">Je **důležité** si uvědomit, že:</span><span class="sxs-lookup"><span data-stu-id="16f78-188">It's **important** to understand that:</span></span>

* <span data-ttu-id="16f78-189">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atribut v tomto řadiči rozhraní API je jediná věc, která chrání toto rozhraní API před neoprávněným přístupem.</span><span class="sxs-lookup"><span data-stu-id="16f78-189">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="16f78-190">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atribut použitý v Blazor aplikaci WebAssembly slouží pouze jako pomocný parametr aplikace, který by měl být uživatelem autorizován, aby mohla aplikace správně fungovat.</span><span class="sxs-lookup"><span data-stu-id="16f78-190">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="16f78-191">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="16f78-191">Client app configuration</span></span>

<span data-ttu-id="16f78-192">*Tato část se vztahuje k **klientské** aplikaci řešení.*</span><span class="sxs-lookup"><span data-stu-id="16f78-192">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="16f78-193">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="16f78-193">Authentication package</span></span>

<span data-ttu-id="16f78-194">Když je aplikace vytvořená tak, aby používala pracovní nebo školní účty ( `SingleOrg` ), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="16f78-194">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="16f78-195">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="16f78-195">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="16f78-196">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="16f78-196">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="16f78-197">Balíček [Microsoft. Authentication. WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) po přechodu do aplikace přidá balíček [Microsoft. AspNetCore. Components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="16f78-197">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="16f78-198">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="16f78-198">Authentication service support</span></span>

<span data-ttu-id="16f78-199">Přidávají <xref:System.Net.Http.HttpClient> se podpory pro instance, které zahrnují přístupové tokeny při vytváření žádostí na serverový projekt.</span><span class="sxs-lookup"><span data-stu-id="16f78-199">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="16f78-200">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="16f78-200">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="16f78-201">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace (například `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="16f78-201">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="16f78-202">Podpora ověřování uživatelů je zaregistrovaná v kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodou rozšíření poskytovanou balíčkem [Microsoft. Authentication. WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) .</span><span class="sxs-lookup"><span data-stu-id="16f78-202">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="16f78-203">Tato metoda nastavuje služby, které aplikace potřebuje k interakci se Identity zprostředkovatelem (IP).</span><span class="sxs-lookup"><span data-stu-id="16f78-203">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="16f78-204">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="16f78-204">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="16f78-205"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="16f78-205">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="16f78-206">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="16f78-206">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="16f78-207">Konfigurace je dodána souborem *wwwroot/appsettings.jsv* souboru:</span><span class="sxs-lookup"><span data-stu-id="16f78-207">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="16f78-208">Příklad:</span><span class="sxs-lookup"><span data-stu-id="16f78-208">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="16f78-209">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="16f78-209">Access token scopes</span></span>

<span data-ttu-id="16f78-210">Výchozí obory přístupového tokenu představují seznam oborů přístupového tokenu, které jsou:</span><span class="sxs-lookup"><span data-stu-id="16f78-210">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="16f78-211">Ve výchozím nastavení zahrnuty v žádosti o přihlášení.</span><span class="sxs-lookup"><span data-stu-id="16f78-211">Included by default in the sign in request.</span></span>
* <span data-ttu-id="16f78-212">Slouží ke zřízení přístupového tokenu hned po ověření.</span><span class="sxs-lookup"><span data-stu-id="16f78-212">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="16f78-213">Všechny obory musí patřit do stejné aplikace na pravidla Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="16f78-213">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="16f78-214">Další obory je možné přidat pro další aplikace API podle potřeby:</span><span class="sxs-lookup"><span data-stu-id="16f78-214">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="16f78-215">Další informace najdete v následujících částech článku o *dalších scénářích* :</span><span class="sxs-lookup"><span data-stu-id="16f78-215">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="16f78-216">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="16f78-216">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="16f78-217">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="16f78-217">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="16f78-218">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="16f78-218">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="16f78-219">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="16f78-219">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="16f78-220">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="16f78-220">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="16f78-221">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="16f78-221">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="16f78-222">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="16f78-222">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="16f78-223">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="16f78-223">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="16f78-224">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="16f78-224">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="16f78-225">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="16f78-225">Run the app</span></span>

<span data-ttu-id="16f78-226">Spusťte aplikaci z projektu serveru.</span><span class="sxs-lookup"><span data-stu-id="16f78-226">Run the app from the Server project.</span></span> <span data-ttu-id="16f78-227">Při použití sady Visual Studio buď:</span><span class="sxs-lookup"><span data-stu-id="16f78-227">When using Visual Studio, either:</span></span>

* <span data-ttu-id="16f78-228">Nastavte rozevírací seznam **projekty po spuštění** na panelu nástrojů na *aplikaci API serveru* a vyberte tlačítko **Spustit** .</span><span class="sxs-lookup"><span data-stu-id="16f78-228">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="16f78-229">Vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .</span><span class="sxs-lookup"><span data-stu-id="16f78-229">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="16f78-230">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="16f78-230">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="16f78-231">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="16f78-231">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="16f78-232">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="16f78-232">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
