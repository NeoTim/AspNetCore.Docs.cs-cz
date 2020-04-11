---
title: Zabezpečení ASP.NET Blazor hostované webové sestavy core s Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e2bb0c1bd807d590331b714e3b80d8c4ab434e2f
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123472"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="85fc8-102">Zabezpečení ASP.NET Blazor hostované webové sestavy core s Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="85fc8-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="85fc8-103">[Javier Calvarro Nelson](https://github.com/javiercn) a Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="85fc8-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="85fc8-104">Tento článek popisuje, jak Blazor vytvořit samostatnou aplikaci WebAssembly, která používá [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="85fc8-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="85fc8-105">Registrace aplikací v AAD B2C a vytvoření řešení</span><span class="sxs-lookup"><span data-stu-id="85fc8-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="85fc8-106">Vytvoření tenanta</span><span class="sxs-lookup"><span data-stu-id="85fc8-106">Create a tenant</span></span>

<span data-ttu-id="85fc8-107">Postupujte podle pokynů v [kurzu: Vytvoření klienta Služby Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) k vytvoření klienta AAD B2C a zaznamenat následující informace:</span><span class="sxs-lookup"><span data-stu-id="85fc8-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="85fc8-108">Instance AAD B2C (například `https://contoso.b2clogin.com/`, která zahrnuje koncové lomítko)</span><span class="sxs-lookup"><span data-stu-id="85fc8-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="85fc8-109">Doména klienta AAD B2C (například `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="85fc8-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="85fc8-110">Registrace aplikace serverového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="85fc8-110">Register a server API app</span></span>

<span data-ttu-id="85fc8-111">Postupujte podle pokynů v [kurzu: Zaregistrujte aplikaci ve službě Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) a zaregistrujte aplikaci AAD pro *aplikaci Rozhraní API serveru* v oblasti**registrace aplikací** Azure **Active Directory** > na webu Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="85fc8-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="85fc8-112">Vyberte **možnost Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="85fc8-112">Select **New registration**.</span></span>
1. <span data-ttu-id="85fc8-113">Zadejte **název** aplikace (například \*\* Blazor Server AAD B2C).\*\*</span><span class="sxs-lookup"><span data-stu-id="85fc8-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="85fc8-114">U **podporovaných typů účtů**vyberte možnost Účty v **libovolném organizačním adresáři nebo v libovolném poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="85fc8-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="85fc8-115">(víceklientů) pro tuto zkušenost.</span><span class="sxs-lookup"><span data-stu-id="85fc8-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="85fc8-116">*Aplikace rozhraní API serveru* nevyžaduje identifikátor URI **přesměrování** v tomto scénáři, takže ponechte rozevírací soubor nastavený na **web** a nezadávejte identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="85fc8-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="85fc8-117">Zkontrolujte, zda jsou **povolena** > **oprávnění Udělit oprávnění k udělení oprávnění k otevření a offline_access oprávnění.**</span><span class="sxs-lookup"><span data-stu-id="85fc8-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="85fc8-118">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="85fc8-118">Select **Register**.</span></span>

<span data-ttu-id="85fc8-119">V **vystavit rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="85fc8-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="85fc8-120">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="85fc8-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="85fc8-121">Vyberte **Uložit a pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="85fc8-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="85fc8-122">Zadejte **název oboru** `API.Access`(například).</span><span class="sxs-lookup"><span data-stu-id="85fc8-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="85fc8-123">Zadejte **zobrazovaný název souhlasu správce** `Access API`(například).</span><span class="sxs-lookup"><span data-stu-id="85fc8-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="85fc8-124">Uveďte **popis souhlasu** správce `Allows the app to access server app API endpoints.`(například).</span><span class="sxs-lookup"><span data-stu-id="85fc8-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="85fc8-125">Zkontrolujte, zda je **stav** nastaven na **povoleno**.</span><span class="sxs-lookup"><span data-stu-id="85fc8-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="85fc8-126">Vyberte **přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="85fc8-126">Select **Add scope**.</span></span>

<span data-ttu-id="85fc8-127">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="85fc8-127">Record the following information:</span></span>

* <span data-ttu-id="85fc8-128">*Aplikace rozhraní API serveru* ID aplikace (ID klienta) `11111111-1111-1111-1111-111111111111`(například)</span><span class="sxs-lookup"><span data-stu-id="85fc8-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="85fc8-129">Identifikátor URI ID aplikace `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`(například , nebo vlastní hodnota, kterou jste zadali)</span><span class="sxs-lookup"><span data-stu-id="85fc8-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="85fc8-130">ID adresáře (ID klienta) `222222222-2222-2222-2222-222222222222`(například)</span><span class="sxs-lookup"><span data-stu-id="85fc8-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="85fc8-131">*Aplikace rozhraní API serveru* Identifikátor URI ID aplikace `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`(například portál Azure může výchozí hodnotu ID klienta)</span><span class="sxs-lookup"><span data-stu-id="85fc8-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="85fc8-132">Výchozí obor (například `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="85fc8-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="85fc8-133">Registrace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="85fc8-133">Register a client app</span></span>

<span data-ttu-id="85fc8-134">Postupujte podle pokynů v [kurzu: Zaregistrujte aplikaci ve službě Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) znovu zaregistrovat aplikaci AAD pro *klientskou aplikaci* v oblasti**registrace aplikací** Azure **Active Directory** > na webu Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="85fc8-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="85fc8-135">Vyberte **možnost Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="85fc8-135">Select **New registration**.</span></span>
1. <span data-ttu-id="85fc8-136">Zadejte **název** aplikace (například \*\* Blazor Klient AAD B2C).\*\*</span><span class="sxs-lookup"><span data-stu-id="85fc8-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="85fc8-137">U **podporovaných typů účtů**vyberte možnost Účty v **libovolném organizačním adresáři nebo v libovolném poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="85fc8-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="85fc8-138">(víceklientů) pro tuto zkušenost.</span><span class="sxs-lookup"><span data-stu-id="85fc8-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="85fc8-139">Ponechejte rozevírací soubor **URI přesměrování** nastavený `https://localhost:5001/authentication/login-callback`na **web**a poskytněte identifikátor URI přesměrování aplikace .</span><span class="sxs-lookup"><span data-stu-id="85fc8-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="85fc8-140">Zkontrolujte, zda jsou **povolena** > **oprávnění Udělit oprávnění k udělení oprávnění k otevření a offline_access oprávnění.**</span><span class="sxs-lookup"><span data-stu-id="85fc8-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="85fc8-141">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="85fc8-141">Select **Register**.</span></span>

<span data-ttu-id="85fc8-142">V**konfiguracích platformy** >  **ověřování** > **web**:</span><span class="sxs-lookup"><span data-stu-id="85fc8-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="85fc8-143">Potvrďte, že `https://localhost:5001/authentication/login-callback` je přítomen **identifikátor URI přesměrování.**</span><span class="sxs-lookup"><span data-stu-id="85fc8-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="85fc8-144">V **případě implicitního udělení**zaškrtněte políčka u **tokenů přístupu** a **tokenů ID**.</span><span class="sxs-lookup"><span data-stu-id="85fc8-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="85fc8-145">Zbývající výchozí hodnoty pro aplikaci jsou přijatelné pro toto prostředí.</span><span class="sxs-lookup"><span data-stu-id="85fc8-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="85fc8-146">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="85fc8-146">Select the **Save** button.</span></span>

<span data-ttu-id="85fc8-147">V **rozhraní API oprávnění**:</span><span class="sxs-lookup"><span data-stu-id="85fc8-147">In **API permissions**:</span></span>

1. <span data-ttu-id="85fc8-148">Zkontrolujte, zda má aplikace oprávnění **Microsoft Graph** > **User.Read.**</span><span class="sxs-lookup"><span data-stu-id="85fc8-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="85fc8-149">Vyberte **přidat oprávnění** následované **moje api**.</span><span class="sxs-lookup"><span data-stu-id="85fc8-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="85fc8-150">Ve sloupci **Název** vyberte *aplikaci Server API* (například \*\* Blazor Server AAD B2C).\*\*</span><span class="sxs-lookup"><span data-stu-id="85fc8-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="85fc8-151">Otevřete seznam **rozhraní API.**</span><span class="sxs-lookup"><span data-stu-id="85fc8-151">Open the **API** list.</span></span>
1. <span data-ttu-id="85fc8-152">Povolte přístup k rozhraní `API.Access`API (například).</span><span class="sxs-lookup"><span data-stu-id="85fc8-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="85fc8-153">Vyberte **Přidat oprávnění**.</span><span class="sxs-lookup"><span data-stu-id="85fc8-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="85fc8-154">Vyberte **tlačítko Udělit obsah správce pro {NÁZEV TENANTA}.**</span><span class="sxs-lookup"><span data-stu-id="85fc8-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="85fc8-155">Výběrem **Ano** potvrďte.</span><span class="sxs-lookup"><span data-stu-id="85fc8-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="85fc8-156">V **domácí** > **Azure AD B2C** > **toky uživatelů**:</span><span class="sxs-lookup"><span data-stu-id="85fc8-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="85fc8-157">Vytvoření toku registrace a přihlášení uživatele</span><span class="sxs-lookup"><span data-stu-id="85fc8-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="85fc8-158">Vyberte minimálně uživatelský atribut**Zobrazení názvu** **aplikace,** >  `LoginDisplay` chcete-li `context.User.Identity.Name` naplnit v komponentě (*Shared/LoginDisplay.razor).*</span><span class="sxs-lookup"><span data-stu-id="85fc8-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="85fc8-159">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="85fc8-159">Record the following information:</span></span>

* <span data-ttu-id="85fc8-160">Zaznamenejte ID aplikace *klientské aplikace* `33333333-3333-3333-3333-333333333333`(ID klienta) (například).</span><span class="sxs-lookup"><span data-stu-id="85fc8-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="85fc8-161">Zaznamenejte název uživatelského toku registrace a přihlášení `B2C_1_signupsignin`vytvořený pro aplikaci (například).</span><span class="sxs-lookup"><span data-stu-id="85fc8-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="85fc8-162">Vytvoření aplikace</span><span class="sxs-lookup"><span data-stu-id="85fc8-162">Create the app</span></span>

<span data-ttu-id="85fc8-163">Nahraďte zástupné symboly v následujícím příkazu informacemi zaznamenanými dříve a proveďte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="85fc8-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="85fc8-164">Chcete-li určit výstupní umístění, které vytvoří složku projektu, pokud neexistuje, zahrňte do `-o BlazorSample`příkazu možnost výstupu s cestou (například ).</span><span class="sxs-lookup"><span data-stu-id="85fc8-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="85fc8-165">Název složky se také stane součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="85fc8-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="85fc8-166">Předejte `app-id-uri` možnosti IDENTIFIKÁTOR UD aplikace, ale všimněte si, že změna konfigurace může být vyžadována v klientské aplikaci, která je popsána v části [Obory tokenů aplikace Access.](#access-token-scopes)</span><span class="sxs-lookup"><span data-stu-id="85fc8-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="85fc8-167">Konfigurace serverové aplikace</span><span class="sxs-lookup"><span data-stu-id="85fc8-167">Server app configuration</span></span>

<span data-ttu-id="85fc8-168">*Tato část se týkajících se aplikace **Server** řešení.*</span><span class="sxs-lookup"><span data-stu-id="85fc8-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="85fc8-169">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="85fc8-169">Authentication package</span></span>

<span data-ttu-id="85fc8-170">Podpora ověřování a autorizace volání ASP.NET základní webová api je `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`poskytována :</span><span class="sxs-lookup"><span data-stu-id="85fc8-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="85fc8-171">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="85fc8-171">Authentication service support</span></span>

<span data-ttu-id="85fc8-172">Metoda `AddAuthentication` nastaví ověřovací služby v rámci aplikace a nakonfiguruje obslužnou rutinu Nosiče JWT jako výchozí metodu ověřování.</span><span class="sxs-lookup"><span data-stu-id="85fc8-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="85fc8-173">Metoda `AddAzureADB2CBearer` nastaví konkrétní parametry v obslužné rutině Nosiče JWT, které jsou nutné k ověření tokenů vysílaných službou Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="85fc8-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="85fc8-174">`UseAuthentication`a `UseAuthorization` zajistit, aby:</span><span class="sxs-lookup"><span data-stu-id="85fc8-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="85fc8-175">Aplikace se pokusí analyzovat a ověřit tokeny na příchozí požadavky.</span><span class="sxs-lookup"><span data-stu-id="85fc8-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="85fc8-176">Jakýkoli požadavek na přístup k chráněnému prostředku bez správných pověření se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="85fc8-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="85fc8-177">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="85fc8-177">User.Identity.Name</span></span>

<span data-ttu-id="85fc8-178">Ve výchozím `User.Identity.Name` nastavení není naplněna.</span><span class="sxs-lookup"><span data-stu-id="85fc8-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="85fc8-179">Chcete-li nakonfigurovat aplikaci `name` tak, aby přijímali hodnotu z typu <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`deklarace, nakonfigurujte [tokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) in :</span><span class="sxs-lookup"><span data-stu-id="85fc8-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="85fc8-180">Nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="85fc8-180">App settings</span></span>

<span data-ttu-id="85fc8-181">Soubor *appsettings.json* obsahuje možnosti konfigurace obslužné rutiny nosiče JWT, která slouží k ověření přístupových tokenů.</span><span class="sxs-lookup"><span data-stu-id="85fc8-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="85fc8-182">Řadič WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="85fc8-182">WeatherForecast controller</span></span>

<span data-ttu-id="85fc8-183">Kontroler WeatherForecast *(Controllers/WeatherForecastController.cs*) zpřístupňuje `[Authorize]` chráněné rozhraní API s atributem použitým na řadič.</span><span class="sxs-lookup"><span data-stu-id="85fc8-183">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="85fc8-184">Je **důležité** si uvědomit, že:</span><span class="sxs-lookup"><span data-stu-id="85fc8-184">It's **important** to understand that:</span></span>

* <span data-ttu-id="85fc8-185">Atribut `[Authorize]` v tomto řadiči rozhraní API je jediná věc, která chrání toto rozhraní API před neoprávněným přístupem.</span><span class="sxs-lookup"><span data-stu-id="85fc8-185">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="85fc8-186">Atribut `[Authorize]` použitý v Blazor aplikaci WebAssembly slouží pouze jako nápověda k aplikaci, že uživatel by měl být autorizován pro aplikaci pracovat správně.</span><span class="sxs-lookup"><span data-stu-id="85fc8-186">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="85fc8-187">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="85fc8-187">Client app configuration</span></span>

<span data-ttu-id="85fc8-188">*Tato část se týkajících se **klientské** aplikace řešení.*</span><span class="sxs-lookup"><span data-stu-id="85fc8-188">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="85fc8-189">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="85fc8-189">Authentication package</span></span>

<span data-ttu-id="85fc8-190">Když je aplikace vytvořena pro použití individuálního`IndividualB2C`účtu B2C ( ), aplikace automaticky`Microsoft.Authentication.WebAssembly.Msal`obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="85fc8-190">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="85fc8-191">Balíček obsahuje sadu primitiv, které pomáhají aplikaci ověřovat uživatele a získat tokeny pro volání chráněných api.</span><span class="sxs-lookup"><span data-stu-id="85fc8-191">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="85fc8-192">Pokud přidáváte ověřování do aplikace, přidejte balíček ručně do souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="85fc8-192">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="85fc8-193">Nahraďte `{VERSION}` v předchozím odkazu na `Microsoft.AspNetCore.Blazor.Templates` balíček verzí <xref:blazor/get-started> balíčku uvedenou v článku.</span><span class="sxs-lookup"><span data-stu-id="85fc8-193">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="85fc8-194">Balíček `Microsoft.Authentication.WebAssembly.Msal` transitively `Microsoft.AspNetCore.Components.WebAssembly.Authentication` přidá balíček do aplikace.</span><span class="sxs-lookup"><span data-stu-id="85fc8-194">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="85fc8-195">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="85fc8-195">Authentication service support</span></span>

<span data-ttu-id="85fc8-196">Podpora pro ověřování uživatelů je registrována v `AddMsalAuthentication` kontejneru služby `Microsoft.Authentication.WebAssembly.Msal` s metodou rozšíření poskytované balíček.</span><span class="sxs-lookup"><span data-stu-id="85fc8-196">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="85fc8-197">Tato metoda nastaví všechny služby potřebné pro interakci aplikace s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="85fc8-197">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="85fc8-198">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="85fc8-198">*Program.cs*:</span></span>

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

<span data-ttu-id="85fc8-199">Metoda `AddMsalAuthentication` přijímá zpětné volání ke konfiguraci parametrů potřebných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="85fc8-199">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="85fc8-200">Hodnoty potřebné pro konfiguraci aplikace lze získat z konfigurace Azure Portal AAD při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="85fc8-200">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="85fc8-201">Obory přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="85fc8-201">Access token scopes</span></span>

<span data-ttu-id="85fc8-202">Výchozí obory přístupových tokenů představují seznam oborů přístupových tokenů, které jsou:</span><span class="sxs-lookup"><span data-stu-id="85fc8-202">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="85fc8-203">Ve výchozím nastavení zahrnuto do požadavku na přihlášení.</span><span class="sxs-lookup"><span data-stu-id="85fc8-203">Included by default in the sign in request.</span></span>
* <span data-ttu-id="85fc8-204">Slouží k zřízení přístupového tokenu ihned po ověření.</span><span class="sxs-lookup"><span data-stu-id="85fc8-204">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="85fc8-205">Všechny obory musí patřit do stejné aplikace podle pravidel služby Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="85fc8-205">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="85fc8-206">Další obory lze přidat pro další aplikace rozhraní API podle potřeby:</span><span class="sxs-lookup"><span data-stu-id="85fc8-206">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="85fc8-207">Pokud portál Azure poskytuje identifikátor URI oboru a **aplikace vyvolá neošetřenou výjimku,** když obdrží *neoprávněnou odpověď 401* z rozhraní API, zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele.</span><span class="sxs-lookup"><span data-stu-id="85fc8-207">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="85fc8-208">Portál Azure může například poskytovat jeden z následujících formátů identifikátoru URI oboru:</span><span class="sxs-lookup"><span data-stu-id="85fc8-208">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="85fc8-209">Zadej oblast URI oboru bez schématu a hostitele:</span><span class="sxs-lookup"><span data-stu-id="85fc8-209">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="85fc8-210">Další informace naleznete v tématu <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="85fc8-210">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="85fc8-211">Importuje soubor</span><span class="sxs-lookup"><span data-stu-id="85fc8-211">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="85fc8-212">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="85fc8-212">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="85fc8-213">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="85fc8-213">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="85fc8-214">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="85fc8-214">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="85fc8-215">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="85fc8-215">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="85fc8-216">Ověřovací komponenta</span><span class="sxs-lookup"><span data-stu-id="85fc8-216">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="85fc8-217">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="85fc8-217">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="85fc8-218">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="85fc8-218">Run the app</span></span>

<span data-ttu-id="85fc8-219">Spusťte aplikaci z projektu Server.</span><span class="sxs-lookup"><span data-stu-id="85fc8-219">Run the app from the Server project.</span></span> <span data-ttu-id="85fc8-220">Při použití sady Visual Studio vyberte projekt serveru v **Průzkumníku řešení** a vyberte tlačítko **Spustit** na panelu nástrojů nebo spusťte aplikaci z nabídky **Ladění.**</span><span class="sxs-lookup"><span data-stu-id="85fc8-220">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="85fc8-221">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="85fc8-221">Additional resources</span></span>

* [<span data-ttu-id="85fc8-222">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="85fc8-222">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="85fc8-223">Kurz: Vytvoření tenanta Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="85fc8-223">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="85fc8-224">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="85fc8-224">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
