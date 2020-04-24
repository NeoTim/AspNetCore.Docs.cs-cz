---
title: Zabezpečení hostované aplikace Blazor ASP.NET Core WebAssembly pomocí Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 45ef1e6599777a38da7db753a8868028f3134f4b
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110977"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="e8a09-102">Zabezpečení hostované aplikace Blazor ASP.NET Core WebAssembly pomocí Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="e8a09-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="e8a09-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e8a09-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="e8a09-104">Pokyny v tomto článku se týkají ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="e8a09-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="e8a09-105">Toto téma se bude aktualizovat na verzi Preview 5 v pátek, 24. dubna.</span><span class="sxs-lookup"><span data-stu-id="e8a09-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="e8a09-106">Tento článek popisuje, jak vytvořit Blazor samostatnou aplikaci WebAssembly, která pro ověřování používá [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) .</span><span class="sxs-lookup"><span data-stu-id="e8a09-106">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="e8a09-107">Registrace aplikací v AAD B2C a vytvoření řešení</span><span class="sxs-lookup"><span data-stu-id="e8a09-107">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="e8a09-108">Vytvoření tenanta</span><span class="sxs-lookup"><span data-stu-id="e8a09-108">Create a tenant</span></span>

<span data-ttu-id="e8a09-109">Postupujte podle pokynů v [kurzu: vytvoření tenanta Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) pro vytvoření tenanta AAD B2C a zaznamenání následujících informací:</span><span class="sxs-lookup"><span data-stu-id="e8a09-109">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="e8a09-110">Instance AAD B2C (například `https://contoso.b2clogin.com/`, která zahrnuje koncové lomítko)</span><span class="sxs-lookup"><span data-stu-id="e8a09-110">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="e8a09-111">AAD B2C domény klienta (například `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="e8a09-111">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="e8a09-112">Registrace aplikace API serveru</span><span class="sxs-lookup"><span data-stu-id="e8a09-112">Register a server API app</span></span>

<span data-ttu-id="e8a09-113">Postupujte podle pokynů v [kurzu: registrace aplikace v Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) k registraci aplikace AAD pro *aplikaci API serveru* v oblasti**Registrace aplikací** **Azure Active Directory** > v Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="e8a09-113">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e8a09-114">Vyberte **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="e8a09-114">Select **New registration**.</span></span>
1. <span data-ttu-id="e8a09-115">Zadejte **název** aplikace (například \*\* Blazor AAD B2C serveru\*\*).</span><span class="sxs-lookup"><span data-stu-id="e8a09-115">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="e8a09-116">U **podporovaných typů účtů**vyberte **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="e8a09-116">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="e8a09-117">(více tenantů) pro toto prostředí.</span><span class="sxs-lookup"><span data-stu-id="e8a09-117">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="e8a09-118">*Aplikace API serveru* v tomto scénáři nevyžaduje **identifikátor URI přesměrování** , proto nechejte rozevírací seznam nastavený na **Web** a nezadávejte identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="e8a09-118">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="e8a09-119">Potvrďte, že **oprávnění** > **udělují správcům oprávnění k OpenID a offline_access** jsou povolená.</span><span class="sxs-lookup"><span data-stu-id="e8a09-119">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="e8a09-120">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="e8a09-120">Select **Register**.</span></span>

<span data-ttu-id="e8a09-121">Ve **vystavení rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="e8a09-121">In **Expose an API**:</span></span>

1. <span data-ttu-id="e8a09-122">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="e8a09-122">Select **Add a scope**.</span></span>
1. <span data-ttu-id="e8a09-123">Vyberte **Uložit a pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="e8a09-123">Select **Save and continue**.</span></span>
1. <span data-ttu-id="e8a09-124">Zadejte **název oboru** (například `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="e8a09-124">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="e8a09-125">Zadejte **Zobrazovaný název souhlasu správce** (například `Access API`).</span><span class="sxs-lookup"><span data-stu-id="e8a09-125">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="e8a09-126">Zadejte **Popis souhlasu správce** (například `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="e8a09-126">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="e8a09-127">Potvrďte, že je **stav** nastavený na **povoleno**.</span><span class="sxs-lookup"><span data-stu-id="e8a09-127">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="e8a09-128">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="e8a09-128">Select **Add scope**.</span></span>

<span data-ttu-id="e8a09-129">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="e8a09-129">Record the following information:</span></span>

* <span data-ttu-id="e8a09-130">*Aplikace API serveru* ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="e8a09-130">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="e8a09-131">Identifikátor URI ID aplikace (například `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, nebo vlastní hodnota, kterou jste zadali)</span><span class="sxs-lookup"><span data-stu-id="e8a09-131">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="e8a09-132">ID adresáře (ID klienta) (například `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="e8a09-132">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="e8a09-133">*Aplikace API serveru* Identifikátor URI ID aplikace (například `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, Azure Portal může jako výchozí mít hodnotu ID klienta)</span><span class="sxs-lookup"><span data-stu-id="e8a09-133">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="e8a09-134">Výchozí obor (například `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="e8a09-134">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="e8a09-135">Registrace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="e8a09-135">Register a client app</span></span>

<span data-ttu-id="e8a09-136">Postupujte podle pokynů v [kurzu: znovu zaregistrujte aplikaci v Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) k registraci aplikace AAD pro *klientskou aplikaci* v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="e8a09-136">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e8a09-137">Vyberte **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="e8a09-137">Select **New registration**.</span></span>
1. <span data-ttu-id="e8a09-138">Zadejte **název** aplikace (například \*\* Blazor AAD B2C klienta\*\*).</span><span class="sxs-lookup"><span data-stu-id="e8a09-138">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="e8a09-139">U **podporovaných typů účtů**vyberte **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="e8a09-139">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="e8a09-140">(více tenantů) pro toto prostředí.</span><span class="sxs-lookup"><span data-stu-id="e8a09-140">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="e8a09-141">Vynechejte rozevírací seznam **identifikátor URI přesměrování** nastavený na **Web**a zadejte identifikátor URI pro `https://localhost:5001/authentication/login-callback`přesměrování.</span><span class="sxs-lookup"><span data-stu-id="e8a09-141">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="e8a09-142">Potvrďte, že **oprávnění** > **udělují správcům oprávnění k OpenID a offline_access** jsou povolená.</span><span class="sxs-lookup"><span data-stu-id="e8a09-142">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="e8a09-143">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="e8a09-143">Select **Register**.</span></span>

<span data-ttu-id="e8a09-144">Na webu**Konfigurace** >  **ověřovacích** > platforem**Web**:</span><span class="sxs-lookup"><span data-stu-id="e8a09-144">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="e8a09-145">Ověřte, zda `https://localhost:5001/authentication/login-callback` je **identifikátor URI přesměrování** k dispozici.</span><span class="sxs-lookup"><span data-stu-id="e8a09-145">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="e8a09-146">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="e8a09-146">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="e8a09-147">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="e8a09-147">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="e8a09-148">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="e8a09-148">Select the **Save** button.</span></span>

<span data-ttu-id="e8a09-149">V **oprávněních rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="e8a09-149">In **API permissions**:</span></span>

1. <span data-ttu-id="e8a09-150">Potvrďte, že aplikace má **Microsoft Graph** > oprávnění**uživatel. číst** .</span><span class="sxs-lookup"><span data-stu-id="e8a09-150">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="e8a09-151">Vyberte **Přidat oprávnění** a potom **Moje rozhraní API**.</span><span class="sxs-lookup"><span data-stu-id="e8a09-151">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="e8a09-152">Ve sloupci **název** vyberte *aplikace API serveru* (například \*\* Blazor AAD B2C serveru\*\*).</span><span class="sxs-lookup"><span data-stu-id="e8a09-152">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="e8a09-153">Otevřete seznam **rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="e8a09-153">Open the **API** list.</span></span>
1. <span data-ttu-id="e8a09-154">Povolte přístup k rozhraní API (například `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="e8a09-154">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="e8a09-155">Vyberte **Přidat oprávnění**.</span><span class="sxs-lookup"><span data-stu-id="e8a09-155">Select **Add permissions**.</span></span>
1. <span data-ttu-id="e8a09-156">Vyberte tlačítko **pro udělení obsahu správce pro {TENANT}** .</span><span class="sxs-lookup"><span data-stu-id="e8a09-156">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="e8a09-157">Výběrem **Ano** potvrďte.</span><span class="sxs-lookup"><span data-stu-id="e8a09-157">Select **Yes** to confirm.</span></span>

<span data-ttu-id="e8a09-158">V **Home** > **Azure AD B2C**Azure AD B2C > **toky pro uživatele**doma:</span><span class="sxs-lookup"><span data-stu-id="e8a09-158">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="e8a09-159">Vytvoření uživatelského toku pro registraci a přihlašování</span><span class="sxs-lookup"><span data-stu-id="e8a09-159">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="e8a09-160">Aby bylo možné naplnit `context.User.Identity.Name` `LoginDisplay` součást (*Shared/LoginDisplay. Razor*), vyberte alespoň atribut uživatele > **Zobrazovaný název** **deklarací identity aplikace**.</span><span class="sxs-lookup"><span data-stu-id="e8a09-160">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="e8a09-161">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="e8a09-161">Record the following information:</span></span>

* <span data-ttu-id="e8a09-162">Zaznamenejte ID aplikace *klienta aplikace* (ID klienta) (například `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="e8a09-162">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="e8a09-163">Zaznamenejte si název uživatelského toku pro registraci a přihlašování vytvořený pro aplikaci (například `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="e8a09-163">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="e8a09-164">Vytvoření aplikace</span><span class="sxs-lookup"><span data-stu-id="e8a09-164">Create the app</span></span>

<span data-ttu-id="e8a09-165">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="e8a09-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="e8a09-166">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="e8a09-166">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="e8a09-167">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="e8a09-167">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="e8a09-168">Předejte tomuto `app-id-uri` parametru identifikátor URI ID aplikace, ale Všimněte si, že se v klientské aplikaci může vyžadovat Změna konfigurace, která je popsaná v části [obory přístupového tokenu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="e8a09-168">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="e8a09-169">Konfigurace aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="e8a09-169">Server app configuration</span></span>

<span data-ttu-id="e8a09-170">*Tato část se vztahuje k **serverové** aplikaci řešení.*</span><span class="sxs-lookup"><span data-stu-id="e8a09-170">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e8a09-171">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="e8a09-171">Authentication package</span></span>

<span data-ttu-id="e8a09-172">Podpora ověřování a autorizace volání ASP.NET Core webových rozhraní API je poskytována `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span><span class="sxs-lookup"><span data-stu-id="e8a09-172">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="e8a09-173">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="e8a09-173">Authentication service support</span></span>

<span data-ttu-id="e8a09-174">`AddAuthentication` Metoda nastaví služby ověřování v rámci aplikace a nakonfiguruje obslužnou rutinu JWT nosiče jako výchozí metodu ověřování.</span><span class="sxs-lookup"><span data-stu-id="e8a09-174">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="e8a09-175">`AddAzureADB2CBearer` Metoda nastavuje konkrétní parametry v obslužné rutině JWT nosiče vyžadované k ověření tokenů vygenerovaných Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="e8a09-175">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="e8a09-176">`UseAuthentication`a `UseAuthorization` Ujistěte se, že:</span><span class="sxs-lookup"><span data-stu-id="e8a09-176">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="e8a09-177">Aplikace se pokusí analyzovat a ověřit tokeny příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="e8a09-177">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="e8a09-178">Všechny žádosti o přístup k chráněnému prostředku bez správných přihlašovacích údajů selžou.</span><span class="sxs-lookup"><span data-stu-id="e8a09-178">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="e8a09-179">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="e8a09-179">User.Identity.Name</span></span>

<span data-ttu-id="e8a09-180">Ve výchozím nastavení `User.Identity.Name` není naplněno.</span><span class="sxs-lookup"><span data-stu-id="e8a09-180">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="e8a09-181">Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hodnotu `name` z typu deklarace, nakonfigurujte [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e8a09-181">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="e8a09-182">Nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="e8a09-182">App settings</span></span>

<span data-ttu-id="e8a09-183">Soubor *appSettings. JSON* obsahuje možnosti konfigurace obslužné rutiny nosiče JWT používané k ověření přístupových tokenů.</span><span class="sxs-lookup"><span data-stu-id="e8a09-183">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="e8a09-184">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="e8a09-184">WeatherForecast controller</span></span>

<span data-ttu-id="e8a09-185">Řadič WeatherForecast (*Controllers/WeatherForecastController. cs*) zpřístupňuje chráněné rozhraní API s `[Authorize]` atributem použitým pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="e8a09-185">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="e8a09-186">Je **důležité** si uvědomit, že:</span><span class="sxs-lookup"><span data-stu-id="e8a09-186">It's **important** to understand that:</span></span>

* <span data-ttu-id="e8a09-187">`[Authorize]` Atribut v tomto řadiči rozhraní API je jediná věc, která chrání toto rozhraní API před neoprávněným přístupem.</span><span class="sxs-lookup"><span data-stu-id="e8a09-187">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="e8a09-188">`[Authorize]` Atribut použitý v Blazor aplikaci WebAssembly slouží pouze jako pomocný parametr aplikace, který by měl být uživatelem autorizován, aby mohla aplikace správně fungovat.</span><span class="sxs-lookup"><span data-stu-id="e8a09-188">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="e8a09-189">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="e8a09-189">Client app configuration</span></span>

<span data-ttu-id="e8a09-190">*Tato část se vztahuje k **klientské** aplikaci řešení.*</span><span class="sxs-lookup"><span data-stu-id="e8a09-190">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e8a09-191">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="e8a09-191">Authentication package</span></span>

<span data-ttu-id="e8a09-192">Když je aplikace vytvořená tak, aby používala individuální účet`IndividualB2C`B2C (), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="e8a09-192">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="e8a09-193">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e8a09-193">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e8a09-194">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="e8a09-194">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="e8a09-195">Nahraďte `{VERSION}` odkazem na předchozí balíček verzí `Microsoft.AspNetCore.Blazor.Templates` balíčku, který je uvedený v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="e8a09-195">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="e8a09-196">`Microsoft.Authentication.WebAssembly.Msal` Balíček do této aplikace přidá `Microsoft.AspNetCore.Components.WebAssembly.Authentication` balíček.</span><span class="sxs-lookup"><span data-stu-id="e8a09-196">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="e8a09-197">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="e8a09-197">Authentication service support</span></span>

<span data-ttu-id="e8a09-198">Podpora ověřování uživatelů je registrovaná v kontejneru služby s metodou `AddMsalAuthentication` rozšíření poskytovanou `Microsoft.Authentication.WebAssembly.Msal` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="e8a09-198">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="e8a09-199">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="e8a09-199">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="e8a09-200">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e8a09-200">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="e8a09-201">`AddMsalAuthentication` Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="e8a09-201">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e8a09-202">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e8a09-202">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="e8a09-203">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="e8a09-203">Access token scopes</span></span>

<span data-ttu-id="e8a09-204">Výchozí obory přístupového tokenu představují seznam oborů přístupového tokenu, které jsou:</span><span class="sxs-lookup"><span data-stu-id="e8a09-204">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="e8a09-205">Ve výchozím nastavení zahrnuty v žádosti o přihlášení.</span><span class="sxs-lookup"><span data-stu-id="e8a09-205">Included by default in the sign in request.</span></span>
* <span data-ttu-id="e8a09-206">Slouží ke zřízení přístupového tokenu hned po ověření.</span><span class="sxs-lookup"><span data-stu-id="e8a09-206">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="e8a09-207">Všechny obory musí patřit do stejné aplikace na pravidla Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="e8a09-207">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="e8a09-208">Další obory je možné přidat pro další aplikace API podle potřeby:</span><span class="sxs-lookup"><span data-stu-id="e8a09-208">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="e8a09-209">Pokud Azure Portal poskytne identifikátor URI oboru a **aplikace vyvolá neošetřenou výjimku** , když obdrží od rozhraní API *neautorizovanou odpověď 401* , zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele.</span><span class="sxs-lookup"><span data-stu-id="e8a09-209">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="e8a09-210">Azure Portal například může poskytovat jeden z následujících formátů identifikátoru URI oboru:</span><span class="sxs-lookup"><span data-stu-id="e8a09-210">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="e8a09-211">Zadejte identifikátor URI oboru bez schématu a hostitele:</span><span class="sxs-lookup"><span data-stu-id="e8a09-211">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="e8a09-212">Další informace naleznete v tématu <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="e8a09-212">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a><span data-ttu-id="e8a09-213">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="e8a09-213">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="e8a09-214">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="e8a09-214">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="e8a09-215">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="e8a09-215">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="e8a09-216">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="e8a09-216">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="e8a09-217">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="e8a09-217">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="e8a09-218">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="e8a09-218">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="e8a09-219">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="e8a09-219">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="e8a09-220">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="e8a09-220">Run the app</span></span>

<span data-ttu-id="e8a09-221">Spusťte aplikaci z projektu serveru.</span><span class="sxs-lookup"><span data-stu-id="e8a09-221">Run the app from the Server project.</span></span> <span data-ttu-id="e8a09-222">Při použití sady Visual Studio vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .</span><span class="sxs-lookup"><span data-stu-id="e8a09-222">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e8a09-223">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="e8a09-223">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="e8a09-224">Kurz: Vytvoření tenanta Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="e8a09-224">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="e8a09-225">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="e8a09-225">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
