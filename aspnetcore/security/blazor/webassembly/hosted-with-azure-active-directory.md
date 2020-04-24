---
title: Zabezpečení hostované aplikace Blazor ASP.NET Core WebAssembly pomocí Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 5c1113a8177d54bfe71f41e1fd34c25d4dd03012
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138582"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="b71cf-102">Zabezpečení hostované aplikace Blazor ASP.NET Core WebAssembly pomocí Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="b71cf-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="b71cf-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b71cf-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="b71cf-104">Tento článek popisuje, jak vytvořit [ Blazor hostovanou aplikaci WebAssembly](xref:blazor/hosting-models#blazor-webassembly) , která pro ověřování používá [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) .</span><span class="sxs-lookup"><span data-stu-id="b71cf-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="b71cf-105">Registrace aplikací v AAD B2C a vytvoření řešení</span><span class="sxs-lookup"><span data-stu-id="b71cf-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="b71cf-106">Vytvoření tenanta</span><span class="sxs-lookup"><span data-stu-id="b71cf-106">Create a tenant</span></span>

<span data-ttu-id="b71cf-107">Postupujte podle pokynů v [rychlém startu: nastavení tenanta](/azure/active-directory/develop/quickstart-create-new-tenant) pro vytvoření TENANTA v AAD.</span><span class="sxs-lookup"><span data-stu-id="b71cf-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="b71cf-108">Registrace aplikace API serveru</span><span class="sxs-lookup"><span data-stu-id="b71cf-108">Register a server API app</span></span>

<span data-ttu-id="b71cf-109">Postupujte podle pokynů v [rychlém startu: registrace aplikace s využitím Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) a dalších témat Azure AAD k registraci aplikace AAD pro *aplikaci API serveru* v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="b71cf-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="b71cf-110">Vyberte **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="b71cf-110">Select **New registration**.</span></span>
1. <span data-ttu-id="b71cf-111">Zadejte **název** aplikace (například \*\* Blazor AAD serveru\*\*).</span><span class="sxs-lookup"><span data-stu-id="b71cf-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="b71cf-112">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="b71cf-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="b71cf-113">Pro toto prostředí můžete vybrat **účty pouze v tomto organizačním adresáři** (jeden tenant).</span><span class="sxs-lookup"><span data-stu-id="b71cf-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="b71cf-114">*Aplikace API serveru* v tomto scénáři nevyžaduje **identifikátor URI přesměrování** , proto nechejte rozevírací seznam nastavený na **Web** a nezadávejte identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="b71cf-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="b71cf-115">Zakažte **oprávnění** > **udělit správcům oprávnění k OpenID a offline_access** .</span><span class="sxs-lookup"><span data-stu-id="b71cf-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="b71cf-116">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="b71cf-116">Select **Register**.</span></span>

<span data-ttu-id="b71cf-117">V okně **oprávnění rozhraní API**odeberte **Microsoft Graph** > **uživatel. číst** , protože aplikace nevyžaduje přístup k UER nebo k profilu.</span><span class="sxs-lookup"><span data-stu-id="b71cf-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="b71cf-118">Ve **vystavení rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="b71cf-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="b71cf-119">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="b71cf-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="b71cf-120">Vyberte **Uložit a pokračovat**.</span><span class="sxs-lookup"><span data-stu-id="b71cf-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="b71cf-121">Zadejte **název oboru** (například `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="b71cf-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="b71cf-122">Zadejte **Zobrazovaný název souhlasu správce** (například `Access API`).</span><span class="sxs-lookup"><span data-stu-id="b71cf-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="b71cf-123">Zadejte **Popis souhlasu správce** (například `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="b71cf-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="b71cf-124">Potvrďte, že je **stav** nastavený na **povoleno**.</span><span class="sxs-lookup"><span data-stu-id="b71cf-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="b71cf-125">Vyberte **Přidat obor**.</span><span class="sxs-lookup"><span data-stu-id="b71cf-125">Select **Add scope**.</span></span>

<span data-ttu-id="b71cf-126">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="b71cf-126">Record the following information:</span></span>

* <span data-ttu-id="b71cf-127">*Aplikace API serveru* ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="b71cf-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="b71cf-128">Identifikátor URI ID aplikace (například `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, nebo vlastní hodnota, kterou jste zadali)</span><span class="sxs-lookup"><span data-stu-id="b71cf-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="b71cf-129">ID adresáře (ID klienta) (například `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="b71cf-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="b71cf-130">Doména tenanta AAD (například `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="b71cf-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="b71cf-131">Výchozí obor (například `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="b71cf-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="b71cf-132">Registrace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="b71cf-132">Register a client app</span></span>

<span data-ttu-id="b71cf-133">Postupujte podle pokynů v [rychlém startu: registrace aplikace pomocí platformy Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) a dalších témat Azure AAD k registraci aplikace AAD pro *klientskou aplikaci* v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="b71cf-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="b71cf-134">Vyberte **Nová registrace**.</span><span class="sxs-lookup"><span data-stu-id="b71cf-134">Select **New registration**.</span></span>
1. <span data-ttu-id="b71cf-135">Zadejte **název** aplikace (například \*\* Blazor AAD klienta\*\*).</span><span class="sxs-lookup"><span data-stu-id="b71cf-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="b71cf-136">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="b71cf-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="b71cf-137">Pro toto prostředí můžete vybrat **účty pouze v tomto organizačním adresáři** (jeden tenant).</span><span class="sxs-lookup"><span data-stu-id="b71cf-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="b71cf-138">Vynechejte rozevírací seznam **identifikátor URI přesměrování** nastavený na **Web**a zadejte identifikátor URI pro `https://localhost:5001/authentication/login-callback`přesměrování.</span><span class="sxs-lookup"><span data-stu-id="b71cf-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="b71cf-139">Zakažte **oprávnění** > **udělit správcům oprávnění k OpenID a offline_access** .</span><span class="sxs-lookup"><span data-stu-id="b71cf-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="b71cf-140">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="b71cf-140">Select **Register**.</span></span>

<span data-ttu-id="b71cf-141">Na webu**Konfigurace** >  **ověřovacích** > platforem**Web**:</span><span class="sxs-lookup"><span data-stu-id="b71cf-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="b71cf-142">Ověřte, zda `https://localhost:5001/authentication/login-callback` je **identifikátor URI přesměrování** k dispozici.</span><span class="sxs-lookup"><span data-stu-id="b71cf-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="b71cf-143">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="b71cf-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="b71cf-144">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="b71cf-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="b71cf-145">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="b71cf-145">Select the **Save** button.</span></span>

<span data-ttu-id="b71cf-146">V **oprávněních rozhraní API**:</span><span class="sxs-lookup"><span data-stu-id="b71cf-146">In **API permissions**:</span></span>

1. <span data-ttu-id="b71cf-147">Potvrďte, že aplikace má **Microsoft Graph** > oprávnění**uživatel. číst** .</span><span class="sxs-lookup"><span data-stu-id="b71cf-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="b71cf-148">Vyberte **Přidat oprávnění** a potom **Moje rozhraní API**.</span><span class="sxs-lookup"><span data-stu-id="b71cf-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="b71cf-149">Vyberte *aplikaci API serveru* ze sloupce **název** (například \*\* Blazor AAD serveru\*\*).</span><span class="sxs-lookup"><span data-stu-id="b71cf-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="b71cf-150">Otevřete seznam **rozhraní API** .</span><span class="sxs-lookup"><span data-stu-id="b71cf-150">Open the **API** list.</span></span>
1. <span data-ttu-id="b71cf-151">Povolte přístup k rozhraní API (například `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="b71cf-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="b71cf-152">Vyberte **Přidat oprávnění**.</span><span class="sxs-lookup"><span data-stu-id="b71cf-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="b71cf-153">Vyberte tlačítko **pro udělení obsahu správce pro {TENANT}** .</span><span class="sxs-lookup"><span data-stu-id="b71cf-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="b71cf-154">Výběrem **Ano** potvrďte.</span><span class="sxs-lookup"><span data-stu-id="b71cf-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="b71cf-155">Zaznamenejte ID aplikace *klienta aplikace* (ID klienta) (například `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="b71cf-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="b71cf-156">Vytvoření aplikace</span><span class="sxs-lookup"><span data-stu-id="b71cf-156">Create the app</span></span>

<span data-ttu-id="b71cf-157">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="b71cf-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="b71cf-158">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="b71cf-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="b71cf-159">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="b71cf-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="b71cf-160">Předejte tomuto `app-id-uri` parametru identifikátor URI ID aplikace, ale Všimněte si, že se v klientské aplikaci může vyžadovat Změna konfigurace, která je popsaná v části [obory přístupového tokenu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="b71cf-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="b71cf-161">Konfigurace aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="b71cf-161">Server app configuration</span></span>

<span data-ttu-id="b71cf-162">*Tato část se vztahuje k **serverové** aplikaci řešení.*</span><span class="sxs-lookup"><span data-stu-id="b71cf-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="b71cf-163">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="b71cf-163">Authentication package</span></span>

<span data-ttu-id="b71cf-164">Podpora ověřování a autorizace volání ASP.NET Core webových rozhraní API je poskytována `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span><span class="sxs-lookup"><span data-stu-id="b71cf-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="b71cf-165">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="b71cf-165">Authentication service support</span></span>

<span data-ttu-id="b71cf-166">`AddAuthentication` Metoda nastaví služby ověřování v rámci aplikace a nakonfiguruje obslužnou rutinu JWT nosiče jako výchozí metodu ověřování.</span><span class="sxs-lookup"><span data-stu-id="b71cf-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="b71cf-167">`AddAzureADBearer` Metoda nastavuje konkrétní parametry v obslužné rutině JWT nosiče vyžadované k ověření tokenů vygenerovaných Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="b71cf-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="b71cf-168">`UseAuthentication`a `UseAuthorization` Ujistěte se, že:</span><span class="sxs-lookup"><span data-stu-id="b71cf-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="b71cf-169">Aplikace se pokusí analyzovat a ověřit tokeny příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="b71cf-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="b71cf-170">Všechny žádosti o přístup k chráněnému prostředku bez správných přihlašovacích údajů selžou.</span><span class="sxs-lookup"><span data-stu-id="b71cf-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="b71cf-171">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="b71cf-171">User.Identity.Name</span></span>

<span data-ttu-id="b71cf-172">Ve výchozím nastavení rozhraní API serverové aplikace naplní `User.Identity.Name` hodnotu z typu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` deklarace (například `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span><span class="sxs-lookup"><span data-stu-id="b71cf-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="b71cf-173">Pokud chcete aplikaci nakonfigurovat tak, aby přijímala hodnotu `name` z typu deklarace, nakonfigurujte [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b71cf-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="b71cf-174">Nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="b71cf-174">App settings</span></span>

<span data-ttu-id="b71cf-175">Soubor *appSettings. JSON* obsahuje možnosti konfigurace obslužné rutiny nosiče JWT používané k ověření přístupových tokenů.</span><span class="sxs-lookup"><span data-stu-id="b71cf-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="b71cf-176">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b71cf-176">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="b71cf-177">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="b71cf-177">WeatherForecast controller</span></span>

<span data-ttu-id="b71cf-178">Řadič WeatherForecast (*Controllers/WeatherForecastController. cs*) zpřístupňuje chráněné rozhraní API s `[Authorize]` atributem použitým pro kontroler.</span><span class="sxs-lookup"><span data-stu-id="b71cf-178">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="b71cf-179">Je **důležité** si uvědomit, že:</span><span class="sxs-lookup"><span data-stu-id="b71cf-179">It's **important** to understand that:</span></span>

* <span data-ttu-id="b71cf-180">`[Authorize]` Atribut v tomto řadiči rozhraní API je jediná věc, která chrání toto rozhraní API před neoprávněným přístupem.</span><span class="sxs-lookup"><span data-stu-id="b71cf-180">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="b71cf-181">`[Authorize]` Atribut použitý v Blazor aplikaci WebAssembly slouží pouze jako pomocný parametr aplikace, který by měl být uživatelem autorizován, aby mohla aplikace správně fungovat.</span><span class="sxs-lookup"><span data-stu-id="b71cf-181">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="b71cf-182">Konfigurace klientské aplikace</span><span class="sxs-lookup"><span data-stu-id="b71cf-182">Client app configuration</span></span>

<span data-ttu-id="b71cf-183">*Tato část se vztahuje k **klientské** aplikaci řešení.*</span><span class="sxs-lookup"><span data-stu-id="b71cf-183">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="b71cf-184">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="b71cf-184">Authentication package</span></span>

<span data-ttu-id="b71cf-185">Když je aplikace vytvořená tak, aby používala pracovní nebo`SingleOrg`školní účty (), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="b71cf-185">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="b71cf-186">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b71cf-186">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="b71cf-187">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="b71cf-187">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="b71cf-188">Nahraďte `{VERSION}` odkazem na předchozí balíček verzí `Microsoft.AspNetCore.Blazor.Templates` balíčku, který je uvedený v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="b71cf-188">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="b71cf-189">`Microsoft.Authentication.WebAssembly.Msal` Balíček do této aplikace přidá `Microsoft.AspNetCore.Components.WebAssembly.Authentication` balíček.</span><span class="sxs-lookup"><span data-stu-id="b71cf-189">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="b71cf-190">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="b71cf-190">Authentication service support</span></span>

<span data-ttu-id="b71cf-191">Přidávají `HttpClient` se podpory pro instance, které zahrnují přístupové tokeny při vytváření žádostí na serverový projekt.</span><span class="sxs-lookup"><span data-stu-id="b71cf-191">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="b71cf-192">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b71cf-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="b71cf-193">Podpora ověřování uživatelů je registrovaná v kontejneru služby s metodou `AddMsalAuthentication` rozšíření poskytovanou `Microsoft.Authentication.WebAssembly.Msal` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="b71cf-193">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="b71cf-194">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="b71cf-194">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="b71cf-195">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b71cf-195">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="b71cf-196">`AddMsalAuthentication` Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="b71cf-196">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="b71cf-197">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b71cf-197">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="b71cf-198">Konfigurace je dodána souborem *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b71cf-198">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "AzureAd": {
        "Authority": "https://login.microsoftonline.com/{TENANT ID}",
        "ClientId": "{CLIENT APP CLIENT ID}",
        "ValidateAuthority": true
    }
}
```

<span data-ttu-id="b71cf-199">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b71cf-199">Example:</span></span>

```json
{
    "AzureAd": {
        "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
        "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
        "ValidateAuthority": true
    }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="b71cf-200">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="b71cf-200">Access token scopes</span></span>

<span data-ttu-id="b71cf-201">Výchozí obory přístupového tokenu představují seznam oborů přístupového tokenu, které jsou:</span><span class="sxs-lookup"><span data-stu-id="b71cf-201">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="b71cf-202">Ve výchozím nastavení zahrnuty v žádosti o přihlášení.</span><span class="sxs-lookup"><span data-stu-id="b71cf-202">Included by default in the sign in request.</span></span>
* <span data-ttu-id="b71cf-203">Slouží ke zřízení přístupového tokenu hned po ověření.</span><span class="sxs-lookup"><span data-stu-id="b71cf-203">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="b71cf-204">Všechny obory musí patřit do stejné aplikace na pravidla Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="b71cf-204">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="b71cf-205">Další obory je možné přidat pro další aplikace API podle potřeby:</span><span class="sxs-lookup"><span data-stu-id="b71cf-205">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="b71cf-206">Pokud Azure Portal poskytne identifikátor URI oboru a **aplikace vyvolá neošetřenou výjimku** , když obdrží od rozhraní API *neautorizovanou odpověď 401* , zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele.</span><span class="sxs-lookup"><span data-stu-id="b71cf-206">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="b71cf-207">Azure Portal například může poskytovat jeden z následujících formátů identifikátoru URI oboru:</span><span class="sxs-lookup"><span data-stu-id="b71cf-207">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="b71cf-208">Zadejte identifikátor URI oboru bez schématu a hostitele:</span><span class="sxs-lookup"><span data-stu-id="b71cf-208">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="b71cf-209">Další informace najdete v následujících částech článku o *dalších scénářích* :</span><span class="sxs-lookup"><span data-stu-id="b71cf-209">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="b71cf-210">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="b71cf-210">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="b71cf-211">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="b71cf-211">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="b71cf-212">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="b71cf-212">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="b71cf-213">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="b71cf-213">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="b71cf-214">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="b71cf-214">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="b71cf-215">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="b71cf-215">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="b71cf-216">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="b71cf-216">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="b71cf-217">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="b71cf-217">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="b71cf-218">Komponenta FetchData</span><span class="sxs-lookup"><span data-stu-id="b71cf-218">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="b71cf-219">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="b71cf-219">Run the app</span></span>

<span data-ttu-id="b71cf-220">Spusťte aplikaci z projektu serveru.</span><span class="sxs-lookup"><span data-stu-id="b71cf-220">Run the app from the Server project.</span></span> <span data-ttu-id="b71cf-221">Při použití sady Visual Studio vyberte projekt serveru v **Průzkumník řešení** a na panelu nástrojů vyberte tlačítko **Spustit** nebo spusťte aplikaci z nabídky **ladění** .</span><span class="sxs-lookup"><span data-stu-id="b71cf-221">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="b71cf-222">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="b71cf-222">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="b71cf-223">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="b71cf-223">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
