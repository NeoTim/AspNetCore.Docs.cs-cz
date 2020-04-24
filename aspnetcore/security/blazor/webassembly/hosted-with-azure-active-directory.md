---
title: Zabezpečení hostované aplikace Blazor ASP.NET Core WebAssembly pomocí Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8c24546da50607d692a9cdc9f9c007d6ac8645ad
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110925"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="5af2a-102">Zabezpečení hostované aplikace Blazor ASP.NET Core WebAssembly pomocí Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="5af2a-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="5af2a-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5af2a-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="5af2a-104">Pokyny v tomto článku se týkají ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="5af2a-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="5af2a-105">Toto téma se bude aktualizovat na verzi Preview 5 v pátek, 24. dubna.</span><span class="sxs-lookup"><span data-stu-id="5af2a-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="5af2a-106">Tento článek popisuje, jak vytvořit [ Blazor hostovanou aplikaci WebAssembly](xref:blazor/hosting-models#blazor-webassembly) , která pro ověřování používá [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) .</span><span class="sxs-lookup"><span data-stu-id="5af2a-106">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="5af2a-107">Registrace aplikací v AAD B2C a vytvoření řešení</span><span class="sxs-lookup"><span data-stu-id="5af2a-107">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="5af2a-108">Vytvoření tenanta</span><span class="sxs-lookup"><span data-stu-id="5af2a-108">Create a tenant</span></span>

<span data-ttu-id="5af2a-109">Postupujte podle pokynů v [rychlém startu: nastavení tenanta](/azure/active-directory/develop/quickstart-create-new-tenant) pro vytvoření TENANTA v AAD.</span><span class="sxs-lookup"><span data-stu-id="5af2a-109">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="5af2a-110">Registrace aplikace API serveru</span><span class="sxs-lookup"><span data-stu-id="5af2a-110">Register a server API app</span></span>

<span data-ttu-id="5af2a-111">Postupujte podle pokynů v [rychlém startu: registrace aplikace s využitím Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) a dalších témat Azure AAD k registraci aplikace AAD pro *aplikaci API serveru* v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="5af2a-111">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="5af2a-112">Vyberte **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="5af2a-112">Select **New registration**.</span></span>
1. <span data-ttu-id="5af2a-113">Zadejte **název** aplikace (například \*\* Blazor AAD serveru\*\*).</span><span class="sxs-lookup"><span data-stu-id="5af2a-113">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="5af2a-114">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="5af2a-114">Choose a **Supported account types**.</span></span> <span data-ttu-id="5af2a-115">Pro toto prostředí můžete vybrat **účty pouze v tomto organizačním adresáři** (jeden tenant).</span><span class="sxs-lookup"><span data-stu-id="5af2a-115">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="5af2a-116">*Aplikace API serveru* v tomto scénáři nevyžaduje **identifikátor URI přesměrování** , proto nechejte rozevírací seznam nastavený na **Web** a nezadávejte identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="5af2a-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="5af2a-117">Zakažte **oprávnění** > **udělit správcům oprávnění k OpenID a offline_access** .</span><span class="sxs-lookup"><span data-stu-id="5af2a-117">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="5af2a-118">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="5af2a-118">Select **Register**.</span></span>

<span data-ttu-id="5af2a-119">V okně **oprávnění rozhraní API**odeberte **Microsoft Graph** > **uživatel. číst** , protože aplikace nevyžaduje přístup k UER nebo k profilu.</span><span class="sxs-lookup"><span data-stu-id="5af2a-119">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="5af2a-120">Ve **vystavení rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="5af2a-120">In **Expose an API**:</span></span>

1. <span data-ttu-id="5af2a-121">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="5af2a-121">Select **Add a scope**.</span></span>
1. <span data-ttu-id="5af2a-122">Vyberte **Uložit a pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="5af2a-122">Select **Save and continue**.</span></span>
1. <span data-ttu-id="5af2a-123">Zadejte **název oboru** (například `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="5af2a-123">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="5af2a-124">Zadejte **Zobrazovaný název souhlasu správce** (například `Access API`).</span><span class="sxs-lookup"><span data-stu-id="5af2a-124">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="5af2a-125">Zadejte **Popis souhlasu správce** (například `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="5af2a-125">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="5af2a-126">Potvrďte, že je **stav** nastavený na **povoleno**.</span><span class="sxs-lookup"><span data-stu-id="5af2a-126">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="5af2a-127">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="5af2a-127">Select **Add scope**.</span></span>

<span data-ttu-id="5af2a-128">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="5af2a-128">Record the following information:</span></span>

* <span data-ttu-id="5af2a-129">*Aplikace API serveru* ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="5af2a-129">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="5af2a-130">Identifikátor URI ID aplikace (například `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, nebo vlastní hodnota, kterou jste zadali)</span><span class="sxs-lookup"><span data-stu-id="5af2a-130">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="5af2a-131">ID adresáře (ID klienta) (například `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="5af2a-131">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="5af2a-132">Doména tenanta AAD (například `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="5af2a-132">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="5af2a-133">Výchozí obor (například `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="5af2a-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="5af2a-134">Registrace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="5af2a-134">Register a client app</span></span>

<span data-ttu-id="5af2a-135">Postupujte podle pokynů v [rychlém startu: registrace aplikace pomocí platformy Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) a dalších témat Azure AAD k registraci aplikace AAD pro *klientskou aplikaci* v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="5af2a-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="5af2a-136">Vyberte **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="5af2a-136">Select **New registration**.</span></span>
1. <span data-ttu-id="5af2a-137">Zadejte **název** aplikace (například \*\* Blazor AAD klienta\*\*).</span><span class="sxs-lookup"><span data-stu-id="5af2a-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="5af2a-138">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="5af2a-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="5af2a-139">Pro toto prostředí můžete vybrat **účty pouze v tomto organizačním adresáři** (jeden tenant).</span><span class="sxs-lookup"><span data-stu-id="5af2a-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="5af2a-140">Vynechejte rozevírací seznam **identifikátor URI přesměrování** nastavený na **Web**a zadejte identifikátor URI pro `https://localhost:5001/authentication/login-callback`přesměrování.</span><span class="sxs-lookup"><span data-stu-id="5af2a-140">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="5af2a-141">Zakažte **oprávnění** > **udělit správcům oprávnění k OpenID a offline_access** .</span><span class="sxs-lookup"><span data-stu-id="5af2a-141">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="5af2a-142">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="5af2a-142">Select **Register**.</span></span>

<span data-ttu-id="5af2a-143">Na webu**Konfigurace** >  **ověřovacích** > platforem**Web**:</span><span class="sxs-lookup"><span data-stu-id="5af2a-143">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="5af2a-144">Ověřte, zda `https://localhost:5001/authentication/login-callback` je **identifikátor URI přesměrování** k dispozici.</span><span class="sxs-lookup"><span data-stu-id="5af2a-144">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="5af2a-145">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="5af2a-145">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="5af2a-146">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="5af2a-146">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="5af2a-147">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="5af2a-147">Select the **Save** button.</span></span>

<span data-ttu-id="5af2a-148">V **oprávněních rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="5af2a-148">In **API permissions**:</span></span>

1. <span data-ttu-id="5af2a-149">Potvrďte, že aplikace má **Microsoft Graph** > oprávnění**uživatel. číst** .</span><span class="sxs-lookup"><span data-stu-id="5af2a-149">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="5af2a-150">Vyberte **Přidat oprávnění** a potom **Moje rozhraní API**.</span><span class="sxs-lookup"><span data-stu-id="5af2a-150">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="5af2a-151">Vyberte *aplikaci API serveru* ze sloupce **název** (například \*\* Blazor AAD serveru\*\*).</span><span class="sxs-lookup"><span data-stu-id="5af2a-151">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="5af2a-152">Otevřete seznam **rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="5af2a-152">Open the **API** list.</span></span>
1. <span data-ttu-id="5af2a-153">Povolte přístup k rozhraní API (například `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="5af2a-153">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="5af2a-154">Vyberte **Přidat oprávnění**.</span><span class="sxs-lookup"><span data-stu-id="5af2a-154">Select **Add permissions**.</span></span>
1. <span data-ttu-id="5af2a-155">Vyberte tlačítko **pro udělení obsahu správce pro {TENANT}** .</span><span class="sxs-lookup"><span data-stu-id="5af2a-155">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="5af2a-156">Výběrem **Ano** potvrďte.</span><span class="sxs-lookup"><span data-stu-id="5af2a-156">Select **Yes** to confirm.</span></span>

<span data-ttu-id="5af2a-157">Zaznamenejte ID aplikace *klienta aplikace* (ID klienta) (například `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="5af2a-157">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="5af2a-158">Vytvoření aplikace</span><span class="sxs-lookup"><span data-stu-id="5af2a-158">Create the app</span></span>

<span data-ttu-id="5af2a-159">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="5af2a-159">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="5af2a-160">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="5af2a-160">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="5af2a-161">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="5af2a-161">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="5af2a-162">Předejte tomuto `app-id-uri` parametru identifikátor URI ID aplikace, ale Všimněte si, že se v klientské aplikaci může vyžadovat Změna konfigurace, která je popsaná v části [obory přístupového tokenu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="5af2a-162">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="5af2a-163">Konfigurace aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="5af2a-163">Server app configuration</span></span>

<span data-ttu-id="5af2a-164">*Tato část se vztahuje k **serverové** aplikaci řešení.*</span><span class="sxs-lookup"><span data-stu-id="5af2a-164">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="5af2a-165">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="5af2a-165">Authentication package</span></span>

<span data-ttu-id="5af2a-166">Podpora ověřování a autorizace volání ASP.NET Core webových rozhraní API je poskytována `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span><span class="sxs-lookup"><span data-stu-id="5af2a-166">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="5af2a-167">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="5af2a-167">Authentication service support</span></span>

<span data-ttu-id="5af2a-168">`AddAuthentication` Metoda nastaví služby ověřování v rámci aplikace a nakonfiguruje obslužnou rutinu JWT nosiče jako výchozí metodu ověřování.</span><span class="sxs-lookup"><span data-stu-id="5af2a-168">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="5af2a-169">`AddAzureADBearer` Metoda nastavuje konkrétní parametry v obslužné rutině JWT nosiče vyžadované k ověření tokenů vygenerovaných Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="5af2a-169">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="5af2a-170">`UseAuthentication`a `UseAuthorization` Ujistěte se, že:</span><span class="sxs-lookup"><span data-stu-id="5af2a-170">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="5af2a-171">Aplikace se pokusí analyzovat a ověřit tokeny příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="5af2a-171">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="5af2a-172">Všechny žádosti o přístup k chráněnému prostředku bez správných přihlašovacích údajů selžou.</span><span class="sxs-lookup"><span data-stu-id="5af2a-172">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="5af2a-173">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="5af2a-173">User.Identity.Name</span></span>

<span data-ttu-id="5af2a-174">Ve výchozím nastavení rozhraní API serverové aplikace naplní `User.Identity.Name` hodnotu z typu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` deklarace (například `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span><span class="sxs-lookup"><span data-stu-id="5af2a-174">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="5af2a-175">Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hodnotu `name` z typu deklarace, nakonfigurujte [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5af2a-175">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="5af2a-176">Nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="5af2a-176">App settings</span></span>

<span data-ttu-id="5af2a-177">Soubor *appSettings. JSON* obsahuje možnosti konfigurace obslužné rutiny nosiče JWT používané k ověření přístupových tokenů.</span><span class="sxs-lookup"><span data-stu-id="5af2a-177">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="5af2a-178">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="5af2a-178">WeatherForecast controller</span></span>

<span data-ttu-id="5af2a-179">Řadič WeatherForecast (*Controllers/WeatherForecastController. cs*) zpřístupňuje chráněné rozhraní API s `[Authorize]` atributem použitým pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="5af2a-179">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="5af2a-180">Je **důležité** si uvědomit, že:</span><span class="sxs-lookup"><span data-stu-id="5af2a-180">It's **important** to understand that:</span></span>

* <span data-ttu-id="5af2a-181">`[Authorize]` Atribut v tomto řadiči rozhraní API je jediná věc, která chrání toto rozhraní API před neoprávněným přístupem.</span><span class="sxs-lookup"><span data-stu-id="5af2a-181">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="5af2a-182">`[Authorize]` Atribut použitý v Blazor aplikaci WebAssembly slouží pouze jako pomocný parametr aplikace, který by měl být uživatelem autorizován, aby mohla aplikace správně fungovat.</span><span class="sxs-lookup"><span data-stu-id="5af2a-182">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="5af2a-183">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="5af2a-183">Client app configuration</span></span>

<span data-ttu-id="5af2a-184">*Tato část se vztahuje k **klientské** aplikaci řešení.*</span><span class="sxs-lookup"><span data-stu-id="5af2a-184">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="5af2a-185">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="5af2a-185">Authentication package</span></span>

<span data-ttu-id="5af2a-186">Když je aplikace vytvořená tak, aby používala pracovní nebo`SingleOrg`školní účty (), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="5af2a-186">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="5af2a-187">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5af2a-187">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="5af2a-188">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="5af2a-188">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="5af2a-189">Nahraďte `{VERSION}` odkazem na předchozí balíček verzí `Microsoft.AspNetCore.Blazor.Templates` balíčku, který je uvedený v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="5af2a-189">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="5af2a-190">`Microsoft.Authentication.WebAssembly.Msal` Balíček do této aplikace přidá `Microsoft.AspNetCore.Components.WebAssembly.Authentication` balíček.</span><span class="sxs-lookup"><span data-stu-id="5af2a-190">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="5af2a-191">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="5af2a-191">Authentication service support</span></span>

<span data-ttu-id="5af2a-192">Podpora ověřování uživatelů je registrovaná v kontejneru služby s metodou `AddMsalAuthentication` rozšíření poskytovanou `Microsoft.Authentication.WebAssembly.Msal` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="5af2a-192">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="5af2a-193">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="5af2a-193">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="5af2a-194">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5af2a-194">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="5af2a-195">`AddMsalAuthentication` Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="5af2a-195">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="5af2a-196">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="5af2a-196">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="5af2a-197">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="5af2a-197">Access token scopes</span></span>

<span data-ttu-id="5af2a-198">Výchozí obory přístupového tokenu představují seznam oborů přístupového tokenu, které jsou:</span><span class="sxs-lookup"><span data-stu-id="5af2a-198">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="5af2a-199">Ve výchozím nastavení zahrnuty v žádosti o přihlášení.</span><span class="sxs-lookup"><span data-stu-id="5af2a-199">Included by default in the sign in request.</span></span>
* <span data-ttu-id="5af2a-200">Slouží ke zřízení přístupového tokenu hned po ověření.</span><span class="sxs-lookup"><span data-stu-id="5af2a-200">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="5af2a-201">Všechny obory musí patřit do stejné aplikace na pravidla Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="5af2a-201">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="5af2a-202">Další obory je možné přidat pro další aplikace API podle potřeby:</span><span class="sxs-lookup"><span data-stu-id="5af2a-202">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="5af2a-203">Pokud Azure Portal poskytne identifikátor URI oboru a **aplikace vyvolá neošetřenou výjimku** , když obdrží od rozhraní API *neautorizovanou odpověď 401* , zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele.</span><span class="sxs-lookup"><span data-stu-id="5af2a-203">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="5af2a-204">Azure Portal například může poskytovat jeden z následujících formátů identifikátoru URI oboru:</span><span class="sxs-lookup"><span data-stu-id="5af2a-204">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="5af2a-205">Zadejte identifikátor URI oboru bez schématu a hostitele:</span><span class="sxs-lookup"><span data-stu-id="5af2a-205">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="5af2a-206">Další informace naleznete v tématu <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="5af2a-206">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a><span data-ttu-id="5af2a-207">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="5af2a-207">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="5af2a-208">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="5af2a-208">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="5af2a-209">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="5af2a-209">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="5af2a-210">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="5af2a-210">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="5af2a-211">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="5af2a-211">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="5af2a-212">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="5af2a-212">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="5af2a-213">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="5af2a-213">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="5af2a-214">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="5af2a-214">Run the app</span></span>

<span data-ttu-id="5af2a-215">Spusťte aplikaci z projektu serveru.</span><span class="sxs-lookup"><span data-stu-id="5af2a-215">Run the app from the Server project.</span></span> <span data-ttu-id="5af2a-216">Při použití sady Visual Studio vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .</span><span class="sxs-lookup"><span data-stu-id="5af2a-216">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="5af2a-217">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="5af2a-217">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="5af2a-218">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="5af2a-218">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
